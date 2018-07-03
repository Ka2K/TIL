## 자바스크립트

1. 자료형

   1. 숫자 => 1, 1.25
   2. 문자 => 'string' or "string"
   3. null => 아무것도 없다는 값이 값으로 들어있음
   4. undefined => 값이 할당되어 있지 않음
   5. 불린(boolean) => true, false
   6. 객체(object) => {}
      1. 키와 값을 쌍으로 가진 자료형.
      2. var o = {key: value}
      3. 함수를 값으로 가질수 있음
      4. key : method, property
      5. value에 함수가 들어있을 경우 method(함수로 어떤 행동을 취할경우)
      6. 그외의 값은 property(값만 들어있는 경우)
      7. 배열(array) ->[]
         1. 유용한 methods: pop(맨 앞에 친구 뽑아줌), shift(맨 뒤에 친구 뽑아줌), unshift(맨 앞에 값을 넣어줌), push(맨 뒤에 값을 넣어줌), sort(오름차순으로 정렬), reverse(배열을 반대로 정렬),indexOf(어떤 값에 대해 index가 어디 있는지 뽑아줌), for each(하나씩 뽑아줌. function 사용가능)
         2. 더 유용한 methods
            1. arr.map(함수) => 배열의 모든 값에 함수를 적용 시킨 후, 새로운 배열을 반환한다. 
            2. arr.filter(함수)=> 배열의 모든 값에 함수를 적용 시킨 후, true인 값만 새로운 배열에 삽입해 반환한다.
            3. arr.reduce(함수)

2. 함수

   1. 함수 선언방식

      1. 함수 선언식

         ```javascript
         function sum(x,y) {
             return x+y
         }
         ```

      2. 함수 표현식 => 함수를 변수에 넣음

         ```javascript
         var sum = function(x,y) {
             return x+y
         }
         ```

      3. 차이점

         ```javascript
         // 함수 선언식
         sum (1,2);
         function sum(x,y) {
             console.log(x+y);
         }
         ```

         ```javascript
         // 함수 표현식
         sum (1,2);
         var sum = function sum(x,y) {
             console.log(x+y);
         }
         ```

         함수 선언식은 호이스팅이 되지만, 표현식은 안됨.

         호이스팅이란 

         ```javascript
         sum (1,2);
         function sum(x,y) {
             console.log(x+y);
         }
         에서 
         function sum(x,y) {
             console.log(x+y);
         }
         sum (1,2);
         실제로는 이런식으로 작동하는 것임. 끌어올리는 것.
         반면에 함수 표현식의 경우
         sum (1,2);
         var sum = function sum(x,y) {
             console.log(x+y);
         }
         에서
         var sum;
         sum (1,2);
         
         sum = function(x,y) {
             console.log(x+y);
         }
         실제로는 이런식으로 작동함. 끌어올려주지 않음.
         ```

      4. 함수의 다양한 용도(인자, 리턴값)

         ```javascript
         //1. 인자로 넘기기
         var arr = [1,2,3,4,5];
         var double = function(x) {return x*2};
         // var arr2 = arr.map(double);
         var arr2 = map(double, arr);
         function map(func, arr) {
             var new_arr = [];
             for (element of arr) {
             	new_arr.push(func(element));    
             }
             return new_arr;
         }
         
         //arr2 #~> [2,4,6,8,10]
         =======================================================
         var positive = function (x) {return x>0};
         //positive(1) => ture
         //positive(-1) => false
         var arr =[-1, 3, -5, 7, -9]
         
         //var arr2 = arr.filter(positive)
         var arr2 = filter(positive, arr)
         
         function filter(func, arr){
             var new_arr = [];
             for (element of arr){
                 if (func(element)){
                     new_arr.push(element);
                 }
             }
             return new_arr;
         }
         
         //arr2 #~>[3, 7]
         
         //2.함수 리턴
         function func1() {
             return function func2() {
                 console.log("I'm inner function");
             }
         }
         var test = func1(); //test == func2
         test();
         ```

      5. 함수의 인자 사용법

         ```javascript
         function sum(a,b){
             console.log(a,b);
         }
         sum(1) //~>들어오지 않은값은 undefined
         //console.log(1,undefined)로 인식하여 NAN(Not A Number)가 출력됨
         sum(1,2,3) //~>더 들어오는 인자도 arguments로 사용 가능
         //결과값은 3을 무시하고 3
         //더 들어오는 인자들까지 'arguments'에 저장됨. arguments = [1,2,3]
         function sum(){
             var total = 0;
             for(element of arguments) {
                 total += element;
             }
             console.log(total)
         }
         sum(1,2,3,4,5) //#~>15
         
         function multiple(){
             var total = 1;
             for(element of arguments) {
                 total *= element;
             }
             console.log(total)
         }
         multiple(1,2,3,4,5) //#~>120
         ```

3. 변수 스코프 

   ```javascript
   // 전역변수
   var i = 0
   
   function changei() {
       i = 10
       console.log(i);
   }
   changei()// #~>10
   console.log(i); // #~>10
   
   //지역변수
   var i = 0;
   function changeI(){
       var i = 10;
       console.log(i);
   }
   changeI() // #~>10
   console.log(i) //~>0 
   ```

   ```javascript
   //정적 유효범위
   var i = 0;
   
   function a() {
       var i = 10;
       b(); //함수 호출시 변수 참조 x
   }
   
   function b() {
       console.log(i); //함수 선언시 변수 참조 o
       //함수가 선언 될 당시 var i = 0;
   }
   a(); //콘솔에 나오는 값은? => 0
   
   i=10;
   a(); //콘솔에 나오는 값은? => 10
   
   //함수 내 지역 변수 참조하는 방법 => 함수 내에서 함수를 선언해준다.
   var i = 0;
   
   function a() {
   	var i = 10;
       function b() {
       	console.log(i); //var i = 10;
   }
       b();
   }
   a(); //콘솔에 나오는 값은? => 10
   
   //클로저
   
   var i = 0;
   function a() {
       var i = 10;
       return function b() {
           console.log(i);
       }
   }
   
   var closure = a();
   closure() //콘솔에 나오는 값은? => 10 function b가 내부에서 기억하고 있는 10을 반환을 해준다.
   ```

4. Hoisting(끌어올림)

   변수랑 함수가 메모리에 먼저 저장되는것

   함수내에 변수가 있으면 무조건 위로 끌어 올립니다.

   ```javascript
   //실제 코드
   console.log(1); 
   var i = 0;
   
   func();
   function func() {
       console.log("func!!");
   }
   ```

   ```javascript
   //실행될 때 코드
   function func() {
       console.log("func!!");
   }
   
   console.log(i); //콘솔에 찍히는 값은? undefined
   var i = 0;
   
   func(); // 콘솔에 찍히는 값은? func!!
   ```

   ```javascript
   //두번째 예시
   var i = 0
   function func2(){
       console.log (i);
       var i = 10;
   }
   
   func2() //콘솔에 찍히는 값은?? => undefined
   ```

   ```javascript
   //실행될 때 코드
   var i = 0
   function func2(){
       var i;
       console.log (i);
      	i = 10;
   }
   
   func2() //콘솔에 찍히는 값은?? => undefined
   ```

   ```javascript
   //3번째 예시, 실제 코드
   var language = 'java';
   
   function checkScript(script) {
       if (script) {
           var language = "ruby"
           console.log(language);
       } else {
           console.log(language);
       }
   }
   
   checkScript(true): //콘솔에 찍히는 값은?? => ruby
   checkScript(false): //콘솔에 찍히는 값은?? => undefined
   ```

   ```javascript
   //실행될 때 코드
   var language = 'java';
   
   function checkScript(script) {
       var language;
       if (script) {
           language = "ruby"
           console.log(language);
       } else {
           console.log(language);
       }
   }
   
   checkScript(true): //콘솔에 찍히는 값은?? => ruby
   checkScript(false): //콘솔에 찍히는 값은?? => undefined
   ```

   ```javascript
   //3번째 예시, 해결책 1: 전역변수를 참조하게 한다
   var language = 'java';
   
   function checkScript(script) {
       
       if (script) {
           language = "ruby" // 지역변수 선언을 없애 전역변수를 참조하게함
           console.log(language);
       } else {
           console.log(language);
       }
   }
   
   checkScript(true): //콘솔에 찍히는 값은?? => ruby
   checkScript(false): //콘솔에 찍히는 값은?? => java
   ```

   ```javascript
   //3번째 예시, 해결책 2: 변수를 let으로 선언한다.
   var language = 'java';
   
   function checkScript(script) {
       if (script) {
           let language = "ruby" // => let을 쓰면 된대요
           console.log(language);
       } else {
           console.log(language);
       }
   }
   
   checkScript(true): //콘솔에 찍히는 값은?? => ruby
   checkScript(false): //콘솔에 찍히는 값은?? => java
   ```

5. this

   this 는 대상이 없을 땐 window, 있을 땐 그 대상이라고 생각하면 됨

   ```javascript
   var globalThis = null;
   
   //1. 함수에서 사용되는 this
   function this1() {
   	globalThis = this //window => 현재 코드가 실행되는 브라우저의 창
   }
   this1();
   globalThis //~>window
   
   //2. method에서 사용되는 this => this는 method를 사용하는 객체
   var o ={
      	p1: 'property1',
       m1:this
   };
   
   o.m1(); // this == o
   globalthis //~>o
   
   //2-1. 예시2
   var o2 = {
       prop1: 1,
       method: function() {
           console.log(this.prop1)
       }
   };
   
   o2.method();// 콘솔에 찍히는 값은?? => 1 ->this.prop1 == o2.prop1
   
   //3. 생성자에서 사용되는 this
   function Person(name) {// 생성자 함수, 클래스와 같은 역할을 함
       this.name = name; //this는 생성된 객체를 의미함
   }
   
   var p1 = new Person("Kim");
   p1.name //~> "Kim
   ========================
   //3-1.
   var globalThis = null;
   
   function this1() {
   	globalThis = this //window => 현재 코드가 실행되는 브라우저의 창
   }
   
   var o1 = new this();
   globalThis //~> o1
   o1.name ="Kim"
   globalThis //~> Kim
   //this 는 대상이 없을 땐 window, 있을 땐 그 대상이라고 생각하면 됨
   ```

6. this와 관련된 methods(call, apply, bind)

   ```javascript
   //1. call(this에 해당하는 대상, argument1, argument2)
   function Person(name){
       this.name = name;
   }
   
   var p1 = new Person('Kim');
   var p2 = {};
   //call 예시 1
   Person.call(p2, "Kim"); //call(this, arguments)
   p2 //(name: Kim)
   
   function Person(name, age) {
       this.name = name;
       this.age = age;
   }
   var p3 = {};
   
   //call 예시2
   Person.call(p3, 'Kim', 23)
   p3 // (name Kim, age 23)
   
   var globalThis = null;
   function testFunc(a,b) {
       globalThis = this;
       console.log(a+b)
   }
   
   var testVar = 20;
   //call 예시 3
   testFunc.call(testVar, 1,2);
   
   //2. apply
   //call이랑 동일함. argument를 넣는 방식만 다름
   Person.call(p3, 'Kim', 23)// , 로 구분해서 인자들을 전해줌
   Person.apply(p3, ["Kim", 23])// 배열 안에 몽땅 넣어서 전해줌.
   
   //3. bind(this에 해당하는 대상) & 함수가 실행되진 않음
   //this에 해당하는 대상을 지정만 해주고 끝. 함수실행 X
   var globalThis = null;
   function testFunc(a,b) {
       globalThis = this;
       console.log(a+b)
   }
   
   var bindedFunc = testFunc.bind(20);
   
   bindedFunc //#~>ƒ testFunc(a,b) {
       			//	globalThis = this;
       			//	console.log(a+b)
   				//}
   // 그대로 들어가 있음.
   
   testFunc(1,2) //#~> 3
   globalThis //#~> Window. this에 아무것도 지정이 되지 않기 때문에 window.
   
   bindedFunc(1,2) //#~> 3
   globalThis //#~> 20 -> bind를 통해 this를 20으로 지정해줬기 때문에 20임.
   ```

7. 클로저 (외부함수의 변수들에 접근 가능한 내부 함수)

   ```javascript
   var arr=[];
   for(var i=0; i<10; i++) {
       arr.push(function(){
           return i * 20;
       })
   }
   
   for(j in arr) {
       console.log(arr[j]());
   }
   /*
   var i = 0;
   arr[i] = function() {return i*20}; // i==0 -> 10
   i += 1
   arr[i] = function() {return i*20}
   i += 1
   arr[i] = function() {return i*20};
   i += 1
   arr[i] = function() {return i*20}
   i += 1
   ////////////
   console.log(arr[0]()); //=> 200
   console.log(arr[1]()); //=> 200
   console.log(arr[2]()); //=> 200 -> i를 계속 tracking하다 10까지 해서 모든 값이 200?
   */
   
   //클로저함수로 바꾸기~
   // 1번 방법
   var arr=[];
   for(var i=0; i<10; i++) {
      arr[i] = function outer(i){
          function inner(){
              return i = 20;
          }
      		return inner;
      }(i); // 주석 + 참고
   }
   
   // 2번방법
   function outer(i){
       return function inner() {
           return i*20
       }
   }
   for (var i=0; i<10; i++) {
       arr[i] = outer(i);
   } 
   
   
   // 출력코드 (1번방법, 2번방법 뒤에 붙여주면 됩니당)
   for(j in arr) {
       console.log(arr[j]());
   }
   
   
   
   //클로저(외부함수 안에 있는 내부함수)
   //내부함수는 외부함수의 변수들에 접근 가능
   //외부함수는 내부함수의 변수들에 접근 불가
   var i = 0;
   function outer() {
       var i = 10;
       var j = 20;
       var k = 30;
       function inner() {
           //var InnerVar = 100;
           console.log(i);
           console.log(j);
           console.log(k)
       }
       //console.log(InnerVar) => 주석을 풀면 오류가 뜨는데, 이는 외부함수인 outer는 내부함수의 변수인 InnerVar에 접근할 수 없기 때문이다.
       return inner;
   }
   
   var closure = outer(); //변수 closure에는inner가 들어가 있음. inner에서는 외부함수의 변수인 i,j,k 에 접근이 가능함
   //클로저는 외부함수에서 내린 변수를 가지고 내부함수를 통해 아예 떠나버림. 실행 시킨후에 i,j,k 값을 따로 변화시켜도(i=20, j=30, k=12로 직접입력하심) 클로저에는 변화가 없다.
   closure() //콘솔에 나오는 값은? => 10, 20, 30
       
   //+ 함수를 호출함과 동시에 변수에 넣을 수 있다..?    
   var closure = function outer(i) {
       return function inner() {
           console.log(i);
       }
   }(1);
   ```

8. prototype (상속, 클래스 변수를 정의하는 것과 같은 역할.)

   ```javascript
   function Person() {
       this.purpose = "happiness";
   }
   
   function Adult() {
       this.age = "higher than 20"
   }
   
   function Child() {
       this.age = "lower than 20"
   }
   
   //Adult와 Child에 prototype을 해주어 Person의 purpose에 접근할 수 있게 되었다.
   Adult.prototype = new Person(); 
   Child.prototype = new Person();
   
   var a1 = new Adult();
   a1.purpose;
   
   var c1 = new Child();
   c1.puropse;
   
   var p1 = new Person();
   var p2 = new Person();
   
   Person.prototype.name = "Kim"; // Person이라는 class에 name이라는 class 변수라고 접근
   Person.prototype.printName = function() {console.log(this.name)}
   
   //1. prototype을 통해 상속을 구현할 수 있다.
   Child.prototype = new Parent();
   var c1 = new Child();
   var p1 = new Parent();
   c1.purpose == p1.purpose
   
   //2. prototype을 통해 메소드와 프로퍼티를 객체 간에 공유할 수 있다.
   Child.prototype.name = "Kim";
   var c1 = new Child();
   var c2 = new Child();
   c1.name == c2.name
   ```

   
