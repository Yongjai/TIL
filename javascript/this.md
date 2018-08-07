# All about `this`

> 자바스크립트를 공부하면서 this에 대한 개념을 명확히 하기 위해 이 부분을 정리하려고 합니다.

5가지 경우에서 this가 어떻게 사용되는지를 다루고 있습니다.

* 전역 공간에서의 `this`
* 함수 내부에서의 `this`
* 메소드 호출 시의 `this`
* 콜백에서의 `this`
* 생성자 함수에서의 `this`



### 전역공간에서의 this

> 전역객체를 가르킵니다. 브라우저에서 this를 출력하면 Window 객체가 나옵니다. 



### 함수 내부에서의 this

> 함수에서 this를 출력하면 어떻게 되는 지 알아보겠습니다.

함수 내부에서 this를 출력해도 Window가 나옵니다. 

```js
function a() {
    console.log(this);
}
a();
```

외부 함수 a의 내부 함수 b에서 다시 this를 출력해도 Window가 나옵니다.

```js
function a() {
    function b() {
        console.log(this);
    }
    b();
}
a();
```

a 객체의 내의 메소드 b 안에 있는 c 함수를 실행했을 때, c 내부에서 this를 출력해도 Window가 나옵니다.

```js
var a = {
    b: function() {
        function c() {
            console.log(this);
        }
        c();
    }
}
a.b();
```

결과적으로 함수 내부에서의 this는 기본적으로로 전역 객체를 가르킵니다. 즉, 바뀔 수도 있다는 이야기 입니다.

```Js
var a = 10;
var obj = {
    a: 20.
    b: function() {
     console.log(this.a); // 1번
    
    function c() {
     console.log(this.a); // 2번
	}
	c();
  }
}
obj.b();
```

위 코드를 출력하게 되면 1번에서는 20이 출력이 됩니다. 하지만 2번에서는 10이 출력이 됩니다. 이유는 b는 메소드이지만 c는 함수이기 떄문입니다. 그렇다면 2번에서 20이 출력이 되도록 만들 수는 없을까요?

방법은 scope chain을 사용하는 방법이 있습니다.

```Js
var a = 10;
var obj = {
    a: 20.
    b: function() {
     var that = this;
     console.log(this.a); // 1번
    
    function c() {
     console.log(that.a); // 2번
	}
	c();
  }
}
obj.b();
```

위 코드와 같이 `vartthar = this` 를 추가해주면 됩니다. that라는 변수에 this를 담아버리고 내부 함수에서 that을 찾게 되는데 self가 없기 때문에 스코프 체인을 타고 올라가서 위에서 찾게 됩니다. 이렇게 되면 2번에서도 20을 출력하게 됩니다.



### 메소드 호출시의 this

> 메소드를 호출했을 때의 this는 메소드를 호출한 주체 입니다. 

```Js
var a = {
    b: function() {
        console.log(this);
    }
}
a.b();
```

이렇게 되면 this는 a 객체를 가르키게 됩니다.

```Js
var a = {
    b: {
        c: function() {
            console.log(this);
        }
    }
}
a.b.c();
```

이렇게 되면 a.b 객체를 가르키게 됩니다. 

혹시 눈치 채셨나요? 메소드 명 바로 앞에 있는 `.` 까지가 this가 된다는 것을 알 수 있습니다.



### callback에서의 this

> 주제에 앞서 call, apply, bind 메소드에 대해서 알아보도록 하겠습니다.

```Js
function a(x, y, z) {
    console.log(this, x, y, z);
}
var b = {
    c: 'eee'
};

a.call(b, 1, 2, 3);
a.apply(b, [1, 2, 3]);

var c = a.bind(b, 1, 2);
c(3);
```

위 코드를 실행하면 모두 `Object {c: "eee"} 1 2 3`이라는 결과가 출력됩니다.

각각의 API 문서를 보면 다음과 같이 소개되어 있습니다.

* **call** :: `func.call(thisArg[, arg1[, arg2[, ...]]])`
* **apply** :: `func.apply(thisArg, [argsArray])`
* **bind** :: `func.bind(thisArg[, arg1[, arg2[, ...]]])`

여기서 thisArg는 모두 동일한데 이는 함수를 실행할 때, 함수 안에서 this를 thisArg로 인식하라는 뜻으로 직접 명시할 수 있습니다. 다시 코드를 살펴보겠습니다.

```Js
function a(x, y, z) {
    console.log(this, x, y, z);
}
var b = {
    c: 'eee'
};

// a를 호출하는 데 this는 b로 정하고 인자로 1,2,3을 받아주세요.
a.call(b, 1, 2, 3);

// a를 호출하는 데 this는 b로 정하고 인자는 [1,2,3] 하나로 받아주세요.
a.apply(b, [1, 2, 3]);

// a를 가지고 새로운 함수를 만드는데 this는 b가 되고 인자에는 1과 2를 넣어두겠다. 
// 이렇게 만든 새로운 함수는 변수 c에 담아두고 3번째 인자만 받겠다.
var c = a.bind(b, 1, 2); 
c(3);
```

call과 apply의 차이는 두 번째 자리부터 인자들을 계속 받거나 두 번째 자리에서 한 번에 하나의 인자로 받아오느냐의 차이입니다. 이 외에 차이는 없습니다. 또한 call과 apply는 즉시 호출 합니다. 반면에 bind는 새로운 함수를 생성할 뿐 호출을 하진 않습니다. 



이제 본격적으로 콜백 함수에서의 this를 살펴보겠습니다.

```Js
var callback = function() {
    console.log(this);
};

var obj = {
    a: 1,
    b: function(cb) {
        cb();
        // cb.call(this);
    }
};
obj.b(callback);
```

`obj.b()` 는  콜백함수를 받아서 그대로 실행하는 메소드 입니다. cb를 받아서 그대로 출력하는 겁니다. 여기선 당연히 callback이 함수이기 때문에 여기서 this는 Window를 가르키고 있습니다. 그런데 call을 이용해서 this를 다른 것으로 명시하면 다른 결과가 나오게 됩니다. 



```Js
var callback = function() {
    console.log(this);
};

var obj = {
    a: 1
};
setTimeout(callback, 100); // 1번
setTimeout(callback.bind(obj), 100); // 2번
```

1번의 setTimeout의 경우, callback에 대한 this를 별도로 명시하고 있지 않기 때문에 this는 Window를 가르키고 있게 됩니다. 여기서 this를 원하는 값으로 명시하고 싶다면 2번과 같은 방법으로 가능합니다. bind가 없었을 때에는 위에서 소개했던 방식인 `var that = this`의 방식을 이용해서 처리했다고 합니다.

 

간단하게 정리를 하자면 다음과 같습니다.

* 기본적으로 함수의 this와 같습니다.
* 제어권을 가진 함수가 callback의 this를 명시한 경우 그에 따르게 됩니다.
* 직접 this를 반인딩한 채로 callback을 넘기면 그에 따르게 됩니다.





### 생성자 함수에서의 this

> 생성자 함수에서의 this는 인스턴스를 가르키게 됩니다.

이 부분은 다음 코드를 보면 이해가 되리라 생각이 되기 때문에 넘어가겠습니다.

```Js
function Person(n, a) {
    this.name = n;
    this.age = a;
}

var yongjai = new Person('용자이', 25);
console.log(yongjai);
```