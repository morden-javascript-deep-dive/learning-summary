# 26장 ES6 함수의 추가 기능
## 함수의 구분
- ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.
- 객체에 바인딩 된 함수도 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출도 가능했다.
- 이는 사용목적에 따라 구분이 없으므로 호출방식에 특별한 제약이 없고 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성한다.
- 따라서  ES6에서는 사용목적에 따라 세 가지 종류로 명확히 구분했다.
![](https://i.ibb.co/T448P2c/image.png)
## 메서드
- ES6이전에는 명확한 정의가 없던 메서드를 이후에는 메서드는 메서드 축약 표현으로 정의된 함수만을 의미하는 것으로 명확한 정의가 규정되었다.
- ES6 메서드는 인스턴스를 생성할 수 없는 non-constructor이다.
    - 따라서 프로토타입 프로퍼티가 없고 프로토타입도 생성하지 않는다.
- ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다.
    - super 참조는 [[HomeObject]]를 사용하여 수퍼클래스의 메서드를 참조하므로 super키워드를 사용할 수 있다.
## 화살표 함수
- 함수 정의
    - 함수 표현식으로 정의해야한다. 호출 방식은 기존 함수와 동일하다.
    ```javascript
    const multiply = (x, y) => x * y;
    multiply(2, 3); 
    ```
- 매개 변수 선언
    - 매개 변수가 여러 개인 경우 () 안에 매개변수를 선언한다.
    - 매개 변수가 한개라면 생략 가능하다.
    - 하지만 매개 변수가 없다면 생략할 수 없다.
- 함수 몸체 정의
    - 함수 몸체가 하나의 문이라면 중괄호를 생략할 수 있다.
    - 하지만 생략한 경우 몸체 내부의 문이 표현식이 아닌 문이라면 에러가 발생한다.
    - 객체 리터럴을 반환하는 경우 객체 리터럴을 소괄호()로 감싸주어야한다.
    - 객체 리터럴을 소괄호로 감싸지 않으면 객체 리터럴의 중괄호 {}를 함수 몸체를 감싸는 중괄호로 잘못 해석한다.
    ```javascript
    const create = (id, content) => { id, content };
    create(1, 'JavaScript'); //undefined
    ```
    - 화살표 함수도 즉시 실행 함수로 사용할 수 있다.
    ```javascript
    const person = (name =>({
        sayHi() { return `Hi My name is ${name}.`;}
    }))('Lee');
    
    console.log(person.sayHi());
    ```
    - 화살표 함수도 일급 객체이므로 Array.prototype.map, Array.prototype.filter, Array.prototype.reduce 같은 고차 함수도 인수에 전달할 수 있다.
    ```javascript
    [1, 2, 3].map(function(v) {
        return v * 2;
    });

    [1, 2, 3].map(v => v * 2);
    ```
- 화살표 함수와 일반 함수의 차이
    - 화살표 함수는 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.
    - 중복된 매개변수 이름을 선언할 수 없다.
        - 일반함수는 중복된 매개변수를 사용해도 에러가 나지 않지만(단, strict mode는 제외) 화살표함수는 에러가 발생한다.
    - 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 가지지 않는다.
        - 따라서 함수 내부에서 this, arguments, super, new.target을 참조하면 스코프 체인을 통해 상위 스코프의 this, arguments, super, new.target을 참조한다.
- this
    ```javascript
    class Prefixer {
        constructor(prefix) {
            this.prefix = prefix;
        }
        add(arr) {
            // 1
            return arr.map(function (item) {
                return this.prefix + item; //2
            });
        }
    }

    const prefixer = new Prefixer('-webkit-');
    console.log(prefixer.add(['transition', 'user-select']));
    ```
    - 위와 같은 예제에서 프로토타입 메서드 내부 1의 this는 메서드를 호출한 객체인 prefixer 객체를 가리킨다.
    - 하지만 Array.prototype.map의 인수로 전달한 콜백 함수의 내부인 2에서는 this는 undefined를 가진다.
    - 이는 Array.prototype.map 메서드가 콜백 함수를 일반 함수로서 호출하기 때문이다.
    - 일반 함수로서 호출된 this는 stric mode가 적용되면 암묵적으로 전역 객체가 아닌 undefined가 반환된다.
    - 이때 콜백 함수의 this(2)와 외부함수의 this(1)가 서로 다른 값을 가리키기 때문에 typeerror가 난 것이다.
    - 따라서 ES6에서드는 화살표 함수를 사용해 콜백함수의 this 문제를 해결가능하다.
     ```javascript
    class Prefixer {
        constructor(prefix) {
            this.prefix = prefix;
        }
        add(arr) {
            return arr.map(item => this.prefix + item);
        }
    }

    const prefixer = new Prefixer('-webkit-');
    console.log(prefixer.add(['transition', 'user-select']));
    ```
    - 화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다.
    - 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다.
    - 이를 lexical this라고 한다.
    - 화살표 함수는 함수 자체의 this 바인딩을 갖지 않기 때문에 Function.prototype.call, Function.prototype.apply, Function.prototype.bind 메서드를 사용해도 화살표 함수 내부의 this를 교체할 수 없다.
    - Function.prototype.call, Function.prototype.apply, Function.prototype.bind 메서드를 호출할 수 없다는 것은 아니다. 화살표 함수는 함수 자체의 this바인딩을 갖지 않기 때문에 this를 교체할 수 없고 언제나 상위 스코프의 this 바인딩을 참조한다.
    - 화살표 함수로 메서드를 정의하는 것은 this가 전역 객체를 가리키기 때문에 바람직 하지 않다.
    ```javascript
    const person = {
        name: 'Lee',
        sayHi: () => console.log(`Hi ${this.name}`)
    };

    person.sayHi();
    ```
    - 따라서 메서드를 정의할 때는 ES6 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋다.
    ```javascript
    const person = {
        name: 'Lee',
        sayHi() { console.log(`Hi ${this.name}`)
        }
    };

    person.sayHi();
    ```
    - 프로토타입 객체의 프로퍼티에 화살표 함수를 할당 하는것도 동일한 문제가 발생한다.
    - 따라서 프로퍼티를 동적 추가할 때는 일반 함수를 할당한다.
- super
    - 화살표 함수는 super 바인딩을 갖지 않기 때문에 내부에서 super를 참조하면 상위 스코프의 super를 참조한다.
    - super는 [[HomeObject]]를 갖는 ES6 메서드 내에서만 사용할 수 있는 키워드다.
- arguments
    - 화살표 함수는 arguments 바인딩을 갖지 않는다. 따라서 this와 마찬가지로 상위 스코프의 arguments를 참조한다.
    - 화살표 함수는 argument 객체를 사용할 수 없기 때문에 화살표 함수로 가변 인자함 수를 구현할 때는 반드시 Rest 파라미터를 사용해야한다.
## Rest 파라미터
- 기본 문법
    - Rest 파라미터는 함수에 전달된 인수드르이 목록을 배열로 받는다.
    ```javascript
    function foo(...rest) {
        console.log(rest); 
    }

    foo(1, 2, 3, 4, 5);
    ```
    - 일반 매개변수와 Rest 파라미터는 함께 사용이 가능하다.
    ```javascript
    function foo(param, ...rest) {
        console.log(param); // 1
        console.log(rest); // [2, 3, 4, 5]
    }

    foo(1, 2, 3, 4, 5);
    ```
    - Rest 파라미터는 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당되기 때문에 마지막 파라미터여야한다.
    - 단 하나만 선언할 수 있다.
    - 함수 정의 시 선언한 매개 변수 개수를 나타내는 함수 객체의 length 프로퍼티에 영향을 주지 않는다.
- Rest 파라미터와 arguments 객체
    - argument 객체는 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며 함수 내부에서 지역 변수처럼 사용할 수 있다.
    - arguments 객체는 배열이 아닌 유사 배열이므로 Function.prototype.call, Function prototype.apply메서드를 사용해 배열로 변환해야하는 단점이 있다.
    - 하지만 rest파라미터를 사용하면 가변 인자함수의 인수 목록을 배열로 직접 전달 받기 때문에 유사 배열 객체인 arguments 객체를 배열로 변환하는 번거로움을 피할 수 있다.
- 매개변수 기본값
    - 