# 28장 Number
## Number 생성자 함수
- Number 객체는 생성자 함수, new 연산자와 함께 Number 인스턴스 생성 가능
```javascript
const numobj = new Number();
console.log(numobj); // Number {[[PrimitbeValue]]: 0 }

const numobj = new Number(10);
console.log(numobj); // Number {[[PrimitbeValue]]: 10 }

const numobj = new Number('10');
console.log(numobj); // Number {[[PrimitbeValue]]: 10 }

const numobj = new Number('Hello');
console.log(numobj); // Number {[[PrimitbeValue]]: NaN }


```
- 개발자 도구로 보면 접근 불가능한 PrimitbeValue가 보이는데 이는 [[NumberData]] 내부 슬롯을 가르킴
- ES5에서는 NumberData를 PrimitbeValue라고 불렀다.

## Number 프로퍼티
- Number.EPSILON
    - Number.EPSILON은 1과 1보다 큰 숫자 중에서 가장 작은 숫자와의 차이와 같음
    - 부동소수점 산술 연산은 IEEE 754를 2진법으로 반환할때 무한소수가 되어 미세한 오차가 생김
    - 부동 소수점으로 인해 발생하는 오차를 해결하기 위해 사용
    ```javascript
    0.1 + 0.2; // 0.300000000000004
    0.1 + 0.2 === 0.3; // false
    function isEqual(a,b) {
        return Math.abs(a - b) < Number.EPSILON;
    }
    isEqual(0.1 + 0.2, 0.3)
    ```
- Number.MAX_VALUE
    - Number.MAX_VALUE는 자바스크립트에서 표현 가능한 가장 큰 양수 값
    - 이보다 큰 숫자는 Infinity
- Number.MIN_VALUE
    - Number.MIN_VALUE는 자바스크립트에서 표현 가능한 가장 작은 양수 값
    - 이보다 작은 숫자는 0
- Number.MAX_SAFE_INTERGER
    - Number.MAX_SAFE_INTERGER는 자바스크립트에서 안전하게 표현 가능한 가장 큰 정수 값
    - 9007199254740991
- Number.MIN_SAFE_INTERGER
    - Number.MIN_SAFE_INTERGER는 자바스크립트에서 안전하게 표현 가능한 가장 작은 정수 값
    - -9007199254740991
- Number.POSITIVE_INFINITY
    - 양의 무한대를 나타내는 숫자값 infinity
- Number.NECATIVE_INFINITY
    - 음의 무한대를 나타내는 숫자값 -infinity
- Number.NaN
    - 숫자가 아님(Not-a-Number)을 나타내는 숫자값이다. Number.NaN은 window.NaN과 같다.
## Number 메서드
- Number.isFinite
    - ES6도입, 인수로 전달된 숫자값이 정상적인 유한수인지 검사해 결과를 불리언 값으로 반환
    - 정상적인 유한수면 true를, 아니라면 false를 반환
    - NaN은 언제나 false
    - isFinite는 인수를 숫자로 암묵적 타입 변환하지만 Number.isFinite는 타입변환하지 않음
- Number.isInteger
    - ES6도입, 인수로 전달된 숫자값이 정수인지 검사해 불리언 값으로 반환
    - 암묵적 타입 변환하지 않음
- Number.isNaN
    - ES6도입, 인수로 전달된 숫자값이 NaN인지 검사하여 결과를 불리언 값으로 반환
    - NaN이라면 true
    - isNaN은 인수를 암묵적 타입변환하지만 Number.isNaN은 타입변환 하지 않음
    - 따라서 Number.isNaN에 undefined를 넣으면 false가 나오지만 isNaN은 true가 나옴
- Number.isSafeInteger
    - 인수로 전달된 값이 안전한 정수인지 검사하여 불리언 값 반환
    - 안전한 정수값이란 -(2^53 -1)과 2^53 -1 사이의 정수값이다.
    - 암묵적 타입 변환하지 않음
- Number.prototype.toExponential
    - 숫자를 지수 표기법으로 변환하여 문자열을 반환
    - 지수 표기법은 매우 크거나 작은 숫자를 표기할 때 사용, e 앞에 있는 숫자에 10의 n승을 곱하는 형식으로 수를 나타내는 방식
    - 인수로 소수점 이하로 표현할 자릿수를 전달 가능
    ```javascript
    (77.1234).toExponential(); // "77.1234e+1"
    (77.1234).toExponential(4); // "77.123e+1"
    ```
    - 숫자리터럴과 함께 Number 프로토타입을 사용하면 에러 발생
    ```javascript
    77.toExponential(); // SyntaxError
    ```
    - 숫자 뒤에 오는.은 의미가 모호하다 숫자 구분기호 일수도 있고 프로퍼티 접근 연산자일 수도 있기 때문에 숫자뒤에 오는 .은 소수 구분 기호로 해석한다.
    ```javascript
    77.1234.toExponential(); // 첫번째.은 숫자뒤에오므로 소수 구분기호로, 두번째 .은 프로퍼티 연산자로 인식

    (77).toExponential(); // 혼란 방지를 위해 그룹 연산자 사용 권장

    77 .toExponential(); // 공백을 통한 구분 방법도 가능
    ``` 
- Number.prototype.toFixed
    - 숫자를 반올림해 문자열로 반환, 인수로 반올림하는 숫자 자릿수를 지정 가능
    - 인수 생략 시 0이 지정된다.
    ```javascript
    (12345.6789).toFixed(); //12345
    (12345.6789).toFixed(1); //12345.7
    ```
- Number.prototype.toPrecision
    - 인수로 전달받은 전체 자릿수까지 유효하도록 나머지 자릿수를 반올림해 문자열 반환
    - 전체 자릿수로 표현할 수 없다면 지수 표기법으로 결과를 반환
    - 인수 생략 시 0이 지정된다.
    ```javascript
    (12345.6789).toFixed(); // 12345.6789, 전체 자릿수 유효
    (12345.6789).toFixed(1); // 1e+4
    ```
- Number.prototype.toString
    - 숫자를 문자열로 변환하여 반환, 진법을 나타내는 2~36 사이의 정수값을 인수로 전달 가능, 인수를 생략하면 기본값 10진법이 지정된다.
    ```javascript
    (10).toString(); // "10"
    (16).toString(2); // "10000", 2진수 문자열 반환
    ```

# 29장 Math
## Math 프로퍼티
- Math.PI
    - 원주율 PI값(3.141592653589793)을 반환
## Math 메서드
- Math.abs
    - 인수로 전달된 숫자의 절대값 반환, 절대값은 반드시 0또는 양수여야함
    - [], '', null은 0을 반환
    - undefined, {}, 'string'은 NaN을 반환
    - 인수가 없다면 NaN을 반환
- Math.round
    - 인수로 전달된 숫자의 소수점 이하를 반올림한 정수를 반환
    - 인수가 없다면 NaN 반환
- Math.ceil
    - 인수로 전달된 숫자의 소수점 이하를 올림한 정수 반환
    - 인수가 없다면 NaN을 반환
- Math.floor
    - 인수로 전달된 숫자의 소수점 이하를 내림한 정수 반환
    - 인수가 없다면 NaN을 반환
- Math.squt
    - 인수로 전달된 숫자의 제곱근을 반환
    - 인수가 없고 음수라면 NaN을 반환
- Math.random
    - 임의의 난수를 반환, 0~1 미만의 실수
    - 0은 포함하지면 1은 포함하지 않음
- Math.pow
    - 첫번째 인수를 밑으로 두번째 인수를 지수로 거듭제곱한 결과를 반환
    ```javascript
    Math.pow(2, 8) // 256
    Math.pow(2, -1) // 0.5
    Math.pow(2); // NaN
    ```
    - ES7에서는 Math.pow보다 지수연산자를 사용하는 것이 가독성이 좋다.
    ```javascript
    2 ** 2 ** 2; // 16
    Math.pow(Math.pow(2, 2), 2) // 16
    ```
- Math.max
    - 전달받은 인수 중 가장 큰 수를 반환, 인수가 없다면 -indfinity를 반환
    - 배열을 인수로 전달하려면 Function.prototype.apply 메서드 혹은 스프레드 문법을 사용해야한다.
    ```javascript
    Math.max.apply(null, [1,2,3]); // 3
    Math.max(...[1,2,3]) // 3
    ```
- Math.min
    - 전달받은 인수 중 가장 작은 수를 반환, 인수가 없다면 indfinity를 반환
    - 배열을 인수로 전달하려면 Function.prototype.apply 메서드 혹은 스프레드 문법을 사용해야한다.
    ```javascript
    Math.min.apply(null, [1,2,3]); // 1
    Math.min(...[1,2,3]) // 1
    ```
# 30장 Date
- Date가 제공하는 현재 날짜와 시간은 자바스크립트 코드가 실행된 시스템의 시계에 의해 결정된다.
## Date 생성자 함수
-  Date 함수는 내부적으로 날짜와 시간을 나타내는 정수값을 가짐
- 예를 들어 모든 시간의 기점인 1970/01/01/0시는 Date 객체 내부적으로 정수값 0을 가짐
- 이로부터 하루가 지난다면 86,400,000을 가짐
- new Date()
    - 인수 없이 new 연산자와 함께 호출하면 현재 날짜와 시간을 가지는 Date 객체를 반환
    - 내부적으로는 정수값을 가지지만 콘솔에 출력하면 기본적으로 날짜와 시간정보를 출력한다.
    - new 연산자 없이 호출하면 객체를 반환하는 것이 아닌 문자열을 반환한다.
- new Date(milliseconds)
    - 숫자 타입의 밀리초를 인수로 전달하면 모든 시간의 기점인 1970/01/01/0시를 기점으로 인수로 전달된 밀리초만큼 경과한 날짜와 시간을 나타내는 Date 객체를 반환
- new Date(dateString)
    - 인수에 날짜와 시간을 나타내는 문자열을 전달하면 지정된 날짜와 시간을 나타내는 Date 객체를 반환
    - 인수로 전달한 문자열은 Date.parse 메서드에 의해 해석 가능한 형식이어야함
    ```javascript
    new Date('May 26, 2020 10:00:00');
    // Tue May 26 2020 10:00:00 GMT+0900
    new Date('2020/03/26/10:00:00');
    // Tue May 26 2020 10:00:00 GMT+0900
    ```
- new Date(year,month[, dat, hour, minute, second,millisecond])
    - 연, 월, 일, 시, 분, 초 밀리초를 의미하는 숫자를 인수로 넣으면 지정된 날짜와 시간을 나타내는 Date객체를 반환
    - 이때 연, 월은 반드시 지정해야함, 지정하지 않는다면 0또는 1로 초기화 
    ![](https://i.ibb.co/tYkztNz/image.png)

## Date 메서드
- Date.now
    - 1970/01/01/0시를 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환
    ```javascript
    const now = Date.now(); 
    new Date(now);
    ```
- Date.parse
    - 1970/01/01/0시를 기점으로 인수로 전달된 지정시간 (new Date(dateString)과 동일한 형식)까지의 밀리초를 숫자로 반환
- Date.UTC
    - 1970/01/01/0시를 기점으로 인수로 전달된 지정시간new Date(year,month[, dat, hour, minute, second,millisecond]과 동일한 형식)까지의 밀리초를 숫자로 반환한다.
    - Date.UTC는 KST가 아닌 UTC로 인식한다.
    - UTC는 KST와 달리 0부터 월이 시작되므로 주의해야한다.
- Date.prototype.getFullYear
    - Date 객체의 연도를 나타내는 정수를 반환한다.
    ```javascript
    new Date('2020/07/24').getFullYear(); // 2020
    ```
- Date.prototype.setFullYear
    - Date 객체에 연도를 나타내는 정수를 설정한다. 옵션으로 월, 일 지정가능
    ```javascript
    const todate = new Date();

    today.setFullYear(2000);
    today.getFullYear(); // 2000

    today.setFullYear(2000, 0, 1);
    today.getFullYear(); // 2000
    ```
- Date.prototype.getMounth
    - 객체의 월, 일을 나타내는 정수 0~11을 반환한다.
    - 0은 1월 11은 12월
    ```javascript
    new Date('2020/07/24').getMounth(); // 5
    ```
- Date.prototype.setMounth
    - Date 객체에 월을 나타내는 정수 0~11을 설정한다. 옵션으로 일 지정가능
    ```javascript
    const todate = new Date();

    today.setMounth(0); // 1월
    today.getMounth(); // 0

    today.setMounth(11, 1); //12월 1일
    today.getMounth(); // 11
    ```
- Date.prototype.getDate
    - 객체의 날짜(1 ~ 31)를 나타내는 정수를 반환한다.
    ```javascript
    new Date('2020/07/24').getDate(); // 24
    ```
- Date.prototype.setDate
    - 객체의 날짜(1 ~ 31)를 나타내는 정수를 설정한다.
    ```javascript
    const todate = new Date();

    today.setDate(1); 
    today.getDate(); // 1
    ```
- Date.prototype.getDay
    - Date 객체의 요일(0~6)을 나타내는 정수를 반환한다.
    ![](https://i.ibb.co/HnqTg5t/image.png)
    ```javascript
    new Date('2020/07/24').getDay(); // 5
    ```
- Date.prototype.getHours
    - 객체의 시간(0~23)을 나타내는 정수를 반환한다.
    ```javascript
    new Date('2020/07/24').getHours(); // 12
    ```
- Date.prototype.setHours
    - 객체의 시간(0~23)을 나타내는 정수를 설정한다. 옵션으로 분, 초 ,밀리초 설정 가능
    ```javascript
    const todate = new Date();

    today.setHours(7); 
    today.getHours(); // 7

    today.setHours(0, 0, 0, 0); // 00:00:00:00
    today.getHours(); // 0
    ```
- Date.prototype.getMinutes
    - 객체의 분을(0~59)을 나타내는 정수를 반환한다. 
    ```javascript
    new Date('2020/07/24/12:30').getMinutes(); // 30
    ```
- Date.prototype.setMinutes
    - 객체의 분을(0~59)을 나타내는 정수를 설정한다. 옵션으로 초 ,밀리초 설정 가능
    ```javascript
    const todate = new Date();

    today.setMinutes(50); 
    today.getMinutes(); // 50

    today.setMinutes(5, 10, 9999); // HH:05:10:999
    today.getMinutes(); // 5
    ```
- Date.prototype.getSeconds
    - 객체의 초(0~59)를 나타내는 정수를 반환한다.
    ```javascript
    new Date('2020/07/24/12:30:10').getSeconds(); // 10
    ```
- Date.prototype.setSeconds
    - 객체의 초(0~59)을 나타내는 정수를 설정한다. 옵션으로 밀리초 설정 가능
    ```javascript
    const todate = new Date();

    today.setSeconds(30); 
    today.getSeconds(); // 30

    today.setSeconds(10, 0); // HH:MM:10:000
    today.getSeconds(); // 10
    ```
- Date.prototype.getMilliSeconds
    - 객체의 밀리초(0~999)를 나타내는 정수를 반환한다.
    ```javascript
    new Date('2020/07/24/12:30:10:150').getMilliSeconds(); // 150
    ```
- Date.prototype.setMilliSeconds
    - 객체의 밀리초(0~999)를 나타내는 정수를 설정한다. 
    ```javascript
    const todate = new Date();

    today.setMilliSeconds(123); 
    today.getMilliSeconds(); // 123
    ```
- Date.prototype.getTime
    - 1970/01/01/0시를 기점으로 Date객체의 시간까지 경과된 밀리초를 반환한다.
    ```javascript
    new Date('2020/07/24/12:30').getTime(); // 1595561400000
    ```
- Date.prototype.setTime
    - 객체에 1970/01/01/0시를 기점으로 경과된 밀리초를 설정한다.
    ```javascript
    const todate = new Date();

    today.setTime(86400000); 
    console.log(today); // Fri Jan 02 1970 09:00:00 GMT+0900(대한민국 표준시)
    ```
- Date.prototype.getTimezoneOffset
    - UTC와 Date객체에 지정된 로컬시간과의 차이를 분 단위로 반환한다. KST는 UTC에 9시간을 더한 시간이다. (UTC = KST - 9h)
    ```javascript
    const today = new Date();
    today.getTimezoneoffset() / 60; // -9
    ```
- Date.prototype.toDateString
    - 사람이 읽을 수 있는 형태의 문자열로 날짜를 반환한다.
    ```javascript
    const todate = new Date('2020/07/24/12:30');

    today.toString(); // Fri Jul 24 2020 12:30:00 GMT+0900
    today.toDateString(); // Fri Jul 24 2020
    ```
- Date.prototype.toTimeString
    - 사람이 읽을 수 있는 형태의 문자열로 시간을 반환한다.
    ```javascript
    const todate = new Date('2020/07/24/12:30');

    today.toString(); // Fri Jul 24 2020 12:30:00 GMT+0900
    today.toTimeString(); // 12:30:00 GMT+0900 (대한민국 표준시)
    ```

- Date.prototype.toISOString
    - ISO 8601 형식으로 객체의 날짜와 시간을 표현한 문자열로 시간을 반환한다.
    ```javascript
    const todate = new Date('2020/07/24/12:30');

    today.toString(); // Fri Jul 24 2020 12:30:00 GMT+0900
    today.toISOString(); // 2020-07-24T03:30:00.000Z
    ```
- Date.prototype.toLocaleString
    - 인수로 전달한 로컬을 기준으로 Date 객체의 날짜와 시간을 표현할 문자열 반환
    - 인수를 생략하면 브라우저가 동작 중인 시스템의 로컬을 적용
    ```javascript
    const todate = new Date('2020/07/24/12:30');

    today.toString(); // Fri Jul 24 2020 12:30:00 GMT+0900
    today.toLocaleString(); // 2020. 7. 24. 오후 12:30:00
    today.toLocaleString('ko-KR'); // 2020. 7. 24. 오후 12:30:00
    today.toLocaleString('en-US'); // 7/24/2020, 12:30:00 PM
    today.toLocaleString('ja-JP'); // 2020/7/24 12:30:00
    ```
- Date.prototype.toLocaleTimeString
    - 인수로 전달한 로컬을 기준으로 Date 객체의 시간을 표현할 문자열 반환
    - 인수를 생략하면 브라우저가 동작 중인 시스템의 로컬을 적용
    ```javascript
    const todate = new Date('2020/07/24/12:30');

    today.toString(); // Fri Jul 24 2020 12:30:00 GMT+0900
    today.toLocaleString(); // 오후 12:30:00
    today.toLocaleString('ko-KR'); // 오후 12:30:00
    today.toLocaleString('en-US'); // 12:30:00 PM
    today.toLocaleString('ja-JP'); // 12:30:00
    ```
## Date를 활용한 시계 예제
- 현재 날짜와 시간을 초 단위로 반복 출력한다.
```javascript
(function printNow() {
  const today = new Date();

  const dayNames = ['(일요일)', '(월요일)', '(화요일)', '(수요일)', '(목요일)', '(금요일)', '(토요일)'];
  // getDay: 해당 요일(0 ~ 6)를 나타내는 정수를 반환한다.
  const day = dayNames[today.getDay()];

  const year = today.getFullYear();
  const month = today.getMonth() + 1;
  const date = today.getDate();
  let hour = today.getHours();
  let minute = today.getMinutes();
  let second = today.getSeconds();
  const ampm = hour >= 12 ? 'PM' : 'AM';

  // 12시간제로 변경
  hour %= 12;
  hour = hour || 12; // 0 => 12

  // 10미만인 분과 초를 2자리로 변경
  minute = minute < 10 ? '0' + minute : minute;
  second = second < 10 ? '0' + second : second;

  const now = `${year}년 ${month}월 ${date}일 ${day} ${hour}:${minute}:${second} ${ampm}`;

  console.log(now);
  setTimeout(printNow, 1000);
}());