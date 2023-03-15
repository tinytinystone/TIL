# TypeScript 클래스

## Class

1. `{ a: 1, b: 2, ... }` 객체 literal을 사용하면 객체를 쉽게 만들 수 있습니다.
2. 그런데 이런 비슷한 객체를 여러 개 만들어야 하는 경우가 생깁니다.
    - 복수의 사용자, 메뉴 다양한 아이템을 객체로 표현하는 경우
3. 이럴 때 사용하는 클래스는 특정 객체를 여러 개 생성하기 위해 사용하는 틀입니다.
4. ES2015 이전까지는 주로 **생성자**(constructor) 함수 를 사용했습니다.
    - 생성자는 새로 생성된 객체를 초기화하도록 설계된 함수입니다. new 키워드를 사용해 호출하며, 생성자의 prototype 프로퍼티가 새 객체의 프로토타입으로 사용됩니다. 생성자 함수는 대문자로 관습적으로 대문자로 시작합니다.
    - `User`라는 생성자와 user 인스턴스를 만들어 보겠습니다.
      - 생성자를 통해 생성된 객체를 그 생성자의 인스턴스라고 합니다.
        
        ```jsx
        function User(name) {
          this.name = name;
          this.isAdmin = false;
        }
        
        const user = new User('ㅈ
        
        User.prototype.introduce = function() {
        	return `안녕하세요! 저는 ${this.name}입니다.`
        }
        ```
        
    - `new User(...)` 가 실행되면 이런 일이 일어납니다.
        
        ```jsx
        function User(name) {
          // 1. 빈 객체를 만들어 this에 할당한다.
          // this = {};  
        
          // 2. 함수 본문을 실행한다. this에 새로운 프로퍼티를 추가해 this를 수정한다.
          this.name = name;
          this.isAdmin = false;
        
          // this가 암시적으로 반환된다.
          // return this;
        }
        ```
        
5. 클래스도 사실은 조금 특별한 방식의 함수입니다.
    - 클래스는 생성자 기능을 대체하며, `class` 표현식을 사용하면 깔끔한 문법으로 정의할 수 있습니다.
        
        ```jsx
        class User {
          constructor (name) {
        		this.name = name;
        		this.isAdmin = false;
          }
        	introduce() {
        		return `안녕하세요, 제 이름은 ${this.name}입니다.`;
        	}
        }
        
        let user1 = new User("유지수");
        let user2 = new User("홍지수");
        
        console.log(user1.introduce()); // 안녕하세요, 제 이름은 유지수입니다.
        console.log(typeof User); // function
        console.log(typeof User.prototype.constructor); // function
        console.log(typeof User.prototype.introduce); // function
        console.log(user1 instanceof User); // true
        ```
        

## JS 클래스 필드(Class Field)와 TS 필드 (Field)

1. JS 클래스 필드
    - 원래 JS 클래스에서 instance의 속성은 constructor(생성자) 내부에, 메서드는 class 블록에 선언해왔습니다.
        
        ```tsx
        class Person {
          constructor(name) {
            this.name = name;
          }
          printName() {
            console.log(this.name);
          }
        }
        ```
        
    - 그런데, 클래스 블록 내에서 바로 할당 연산자 (`=`) 를 이용해 인스턴스 속성을 지정하는 문법을 클래스 필드라고 합니다.
        
        ```tsx
        class Person {
          name = 'Leo';
        	printName = () => {
        		console.log(this.name);
        	}
        }
        ```
        
    - 클래스 필드는 비교적 최근에 추가된 기능이라, 구형 브라우저 등에서는 [작동](http://kangax.github.io/compat-table/esnext/)하지 않을 수 있습니다. babel 같은 트랜스파일러도 필요합니다.
    - 클래스 필드는 prototype이 아니라 개별 instance 객체에 저장됩니다.
        - 따라서 클래스 필드에 화살표함수를 사용한다면, 화살표 함수 내의 this는 호출 형태와 무관하게 **항상 instance 객체를 가리킵니다**.
            - react 클래스 컴포넌트에서 메서드를 이벤트 리스너에 등록한 경우, 메서드를 this binding을 해줘야만 제대로 동작합니다.
                
                ```tsx
                class Toggle extends React.Component {
                  constructor(props) {
                    super(props);
                    this.state = {isToggleOn: true};
                
                    // 콜백에서 `this`가 작동하려면 아래와 같이 바인딩 해주어야 합니다.
                    this.handleClick = this.handleClick.bind(this);
                  }
                
                  handleClick() {
                    this.setState(prevState => ({
                      isToggleOn: !prevState.isToggleOn
                    }));
                  }
                
                  render() {
                    return (
                      <button onClick={this.handleClick}>
                        {this.state.isToggleOn ? 'ON' : 'OFF'}
                      </button>
                    );
                  }
                }
                ```
                
            - 클래스 필드 방식으로 메서드를 선언한 경우에는 this binding이 필요 없습니다.
                
                ```tsx
                class LoggingButton extends React.Component {
                  // 이 문법은 `this`가 handleClick 내에서 바인딩되도록 합니다.
                  // 주의: 이 문법은 *실험적인* 문법입니다.
                  handleClick = () => {
                    console.log('this is:', this);
                  }
                
                  render() {
                    return (
                      <button onClick={this.handleClick}>
                        Click me
                      </button>
                    );
                  }
                }
                ```
                
        - 일반적인 메서드와 달리 클래스 필드로 정의된 메서드는 instance를 생성할 때마다 새로 생성되어 메모리를 더 차지할 수 있습니다.
2. TS 클래스 필드
    - JS에서의 클래스 필드와 TS의 클래스 필드는 다른 개념입니다.
    - TS에서는 클래스의 속성 타입을 만들 때 필드로 선언을 해줍니다.
        
        ```tsx
        class Point {
          x: number;
          y: number;
        
          constructor(x: number, y: number) {
            this.x = x
            this.y = y
          }
        }
        
        const pt = new Point(1, 2);
        ```
        
    - 필드는 readonly 지정자(modifier)를 앞에 붙일 수 있습니다. 생성자 외부에서 해당 필드에 값을 할당하는 것을 방지해줍니다.
        
        ```tsx
        class Octopus {
            readonly name: string;
            readonly numberOfLegs: number = 8;
            constructor (theName: string) {
                this.name = theName;
            }
        }
        let dad = new Octopus("Man with the 8 strong legs");
        dad.name = "Man with the 3-piece suit"; // 오류! name은 읽기전용 입니다.
        ```
        

## interface 대신 class 사용하기

- 선언된 class는 아래 2가지로 사용이 가능합니다.
    1. 생성자 함수
    2. 클래스가 생성한 인스턴스의 타입 
- 클래스는 타입을 생성할 수 있으므로, interface를 써야 하는 자리에 이 클래스가 생성한 타입을 사용할 수도 있습니다만…
- tsconfig 설정 중 `--strictPropertyInitialization`  옵션을 활성화한다면, 클래스 필드가 생성자에서 초기화되지 않은 경우에는 무조건 에러를 냅니다.
- 예시
    
    ```tsx
    class Point {
        x: number;
        y: number;
    }
    // Property 'x' has no initializer and is not definitely assigned in the constructor.
    // Property 'y' has no initializer and is not definitely assigned in the constructor.
    
    interface Point3d extends Point {
        z: number;
    }
    
    let point3d: Point3d = {x: 1, y: 2, z: 3};
    
    ====================
    
    class Point {
        x: number;
        y: number;
    
        constructor() {
          this.x = 1;
          this.y = 2;
        }
    }
    ```
    

## 상속 (inheritance)

1. 클래스에는 한 클래스 기능을 다른 클래스에서 재사용할 수 있는 **상속**(inheritance)이라는 기능이 있습니다.
- `**extends**` 키워드를 사용하면, 부모클래스(혹은 상위클래스 super class) - 자식클래스(혹은 하위클래스 sub class) 관계가 됩니다.
    - 자식클래스 Dog는 부모클래스 Animal의 메서드와 속성을 사용할 수 있습니다.
    
    ```tsx
    class Animal {
        move(distanceInMeters: number = 0) {
            console.log(`Animal moved ${distanceInMeters}m.`);
        }
    }
    
    class Dog extends Animal {
        bark() {
            console.log('Woof! Woof!');
        }
    }
    
    const dog = new Dog();
    dog.bark();
    dog.move(10);
    dog.bark();
    ```
    
- 하위클래스에서 상속받은 메서드와 같은 이름의 메서드를 정의하는 것을 **메서드 오버라이딩**(method override)라고 합니다.
    - 하위클래스에 재정의된 메서드로 덮어씌워집니다.
        
        ```jsx
        class Melon {
          getColor() {
            return '제 색깔은 초록색입니다.';
          }
        }
        
        class WaterMelon extends Melon {
          getColor() {
            return '속은 빨강색입니다.';
          }
        }
        
        const waterMelon = new WaterMelon();
        waterMelon.getColor(); // 속은 빨강색입니다.
        ```
        
    - 이때, 상속받은 메서드와 부모클래스 메서드의 타입이 다른 경우 타입 에러가 발생합니다.
        
        ```tsx
        class Melon {
          getColor() {
            return '제 색깔은 초록색입니다.';
          }
        }
        
        class WaterMelon extends Melon {
          getColor(color: string) {
            return `속은 ${color}색입니다.`;
          }
        }
        
        const waterMelon = new WaterMelon();
        waterMelon.getColor();
        // Property 'getColor' in type 'WaterMelon' is not assignable to the same property in base type 'Melon'.
        // Type '(color: string) => string' is not assignable to type '() => string'.
        
        // Expected 1 arguments, but got 0.
        ```
        
- `**super**` 키워드를 사용하면 부모 클래스 메서드에 직접 접근이 가능합니다.
    
    ```jsx
    class Melon {
      getColor() {
        return '제 색깔은 초록색입니다.';
      }
    }
    
    class WaterMelon extends Melon {
      getColor() {
        return super.getColor() + ' 하지만 속은 빨강색입니다.';
      }
    }
    
    const waterMelon = new WaterMelon();
    waterMelon.getColor(); // 제 색깔은 초록색입니다. 하지만 속은 빨강색입니다.
    ```
    
- 생성자 내부에서 super를 함수처럼 호출하면 상위클래스의 생성자가 호출됩니다.
    - 서브클래스에서 생성자를 정의하지 않으면 자동으로 생성됩니다.
    - 서브클래스 생성자를 정의하는 경우 반드시 `super(..)` 를 호출해야 합니다. 이때, `this`를 사용하기 전에 호출해야 합니다.
        - TypeScript를 사용하면, `'super' must be called before accessing 'this' in the constructor of a derived class.` 와 같은 컴파일 에러를 사전에 내뿜어서 실수할 확률을 줄여줍니다.
            
            ```tsx
            class Melon {
              color: string;
            
              constructor(color: string) { this.color = color; }
              getColor() {
                return '제 색깔은 초록색입니다.';
              }
            }
            
            class WaterMelon extends Melon {
              amount: number;
            
              constructor(color: string, amount: number) { 
            		super(color);
            		this.amount = amount; 
            	}
              getColor() {
                return super.getColor() + ' 하지만 속은 빨강색입니다.';
              }
            }
            
            const waterMelon = new WaterMelon('red', 10000);
            ```
            

## 멤버 가시성 (Member Visibility)
멤버는 클래스의 속성과 메서드를 가리킵니다. 

1. 어떤 언어(Java, C 등)에는 특정 메서드 혹은 속성이 클래스 바깥에서 접근이 가능한지 여부를 변경할 수 있는 **접근 지정자(access modifier)**가 있습니다.
2. JS에는 원래 객체 내부 멤버에 대해 외부에서 접근할 수 있는 권한을 부여하는 기능이 없었습니다.
    1. 모든 값은 default로 public으로 되어 있어 어디서든 공개되어 있습니다.
    2. 그런데 이렇게 되면 내부/외부 인터페이스를 구분하고 분리하는 캡슐화를 할 수가 없습니다.
3. 그래서 JS에서는 명시적으로 속성명에 `_` 를 붙여 **protected** 방식을 이용해 왔습니다.
    1. TS에는 protected 타입이 지원됩니다.
    - **protected**는 클래스 자신과 자식클래스에서만 접근을 허용하도록 합니다.
        
        ```tsx
        // @errors: 2445
        class Greeter {
          public greet() {
            console.log("Hello, " + this.getName());
          }
          protected getName() {
            return "hi";
          }
        }
        
        class SpecialGreeter extends Greeter {
          public howdy() {
            // OK to access protected member here
            console.log("Howdy, " + this.getName());
          }
        }
        const g = new SpecialGreeter();
        g.greet(); // OK
        g.getName(); // Property 'getName' is protected and only accessible within class 'Greeter' and its subclasses.
        ```
        
4. **private** 필드는 최신 JS에서 지원하는 문법입니다. `#` 으로 시작하며, `#` 로 시작하는 필드는 해당 필드가 정의된 클래스 내부에서만 접근 가능합니다.
    - TS에서도 private을 지원합니다. 그런데 JS의 `#` 과는 조금 다른 점들이 있습니다.
        
        ```tsx
        class Animal {
        		#name: string;
            constructor(theName: string) { this.#name = theName; }
        }
        
        new Animal("Cat").#name;
        //               ^^^^^^^
        // Property '#name' is not accessible outside class 'Animal' because it has a private identifier.
        
        =======
        
        class Animal {
            private name: string;
            constructor(theName: string) { this.name = theName; }
        }
        
        new Animal("Cat").name;
        //               ^^^^^^^
        // Property 'name' is private and only accessible within class 'Animal'.
        
        ========
        class MySafe {
          private secretKey = 12345;
        }
         
        const s = new MySafe();
         
        // Not allowed during type checking
        console.log(s.secretKey);
        // Property 'secretKey' is private and only accessible within class 'MySafe'.
         
        // OK. escape hatch(탈출구). JS에는 이러한 탈출구가 없음(대괄호 표기법이 불가능함)
        console.log(s["secretKey"]);
        ```
        
    - private 멤버가 있는 타입을 비교할 때는 비록 실제로는 같은 타입이라도 다르게 처리합니다.
        
        ```tsx
        class Animal {
            private name: string;
            constructor(theName: string) { this.name = theName; }
        }
        
        class Rhino extends Animal {
            constructor() { super("Rhino"); }
        }
        
        class Employee {
            private name: string;
            constructor(theName: string) { this.name = theName; }
        }
        
        let animal = new Animal("Goat");
        let rhino = new Rhino();
        let employee = new Employee("Bob");
        
        animal = rhino;
        animal = employee; // Type 'Employee' is not assignable to type 'Animal'. Types have separate declarations of a private property 'name'.
        ```
        
5. TS에서의 protected와 private은 타입 체킹 때만 기능하고, 자바스크립트 런타임 때는 동작하지 않습니다. 즉, 실제 JS에서는 해당 속성값에 접근이 가능합니다.
    1. JS의 private 필드 (`#`)는 컴파일 이후에도 여전히 private합니다.

## 접근자 속성 (accessor properties)

- 객체 내에서 `get` 과 `set` 키워드를 사용하면, 특정 속성을 읽어오거나 변경할 때 호출하는 속성을 만들 수 있습니다.
    
    ```jsx
    const obj = {
      get prop() {
        return this._hidden;
      },
      set prop(arg) {
        this._hidden = arg;
      }
    }
    obj.prop = 1;
    obj.prop; // 1
    ```
    
- 이렇게 getter와 setter 기능이 정의된 속성을 **접근자 속성**이라고 부릅니다.
- 클래스에서 getter 혹은 setter를 정의하고자 할 때는 메서드 이름 앞에 `get` 혹은 `set` 을 붙여주면 됩니다.
    
    ```tsx
    const fullNameMaxLength = 10;
    
    class Employee {
        private _fullName!: string;
    
        get fullName(): string {
            return this._fullName;
        }
    
        set fullName(newName: string) {
            if (newName && newName.length > fullNameMaxLength) {
                throw new Error("fullName has a max length of " + fullNameMaxLength);
            }
    
            this._fullName = newName;
        }
    }
    
    let employee = new Employee();
    employee.fullName = "Bob Smith";
    if (employee.fullName) {
        console.log(employee.fullName);
    }
    ```
    
- getter와 setter를 이용하면, 반복적인 작업을 피할 수 있습니다.

## 정적(static) 멤버 (static property & static method)

- 특정 인스턴스와 무관하게, 해당 생성자와 관련한 일반적인 값을 사용하거나, 작업을 정리하고 싶을 때 사용합니다.
    
    ```tsx
    class MyClass {
      static x = 0;
      static printX() {
        console.log(MyClass.x);
      }
    }
    console.log(MyClass.x);
    MyClass.printX();
    ```
    
- `Number.isNaN` , `Array.forEach` , `Object.getPropertyOf` 등의 함수는 모두 정적 메서드입니다.
- 클래스에서는 `static` 키워드를 프로퍼티/메서드 앞에 붙여주면 됩니다.
- 메서드를 속성 형태로 직접 할당하는 것과 정적 메서드 선언하는 것은 동일하게 동작합니다.
    
    ```jsx
    class User {
      static staticMethod() {
        alert(this === User);
      }
    }
    
    User.staticMethod = function() {
      alert(this === User);
    };
    
    // 위 두 개는 같은 User.staticMethod() 메서드입니다.
    ```
    
- 정적 멤버는 public, protected, private 접근자 사용이 가능하고, 상속도 됩니다.

## 추상 클래스 (abstract class)

- 추상 클래스는 완전히 구현되지 않는 클래스로, 추상 상위클래스가 정의는 했지만 구현은 하지 않는 추상 메서드를 하위클래스에서 구현해서 사용하도록 합니다.
    - 예를 들어, Animal 클래스의 인스턴스를 만들 일은 없고 추상적인 개념으로만 존재하기를 바랄 때, JS에 해당 기능은 없지만 아래와 같이 구현할 수 있습니다.
        
        ```tsx
        class Animal {
            constructor(name) { this.name = name; }
            getName() { return this.name; }
            makeNoise() {
                throw new Error('makeNoise() must be implement.');
            }
        }
        
        class Dog extends Animal {
            constructor(name) { super(name); this.name = name; }
            makeNoise() { return '멍멍!' }
        }
        
        class Cow extends Animal {
            constructor(name) { super(name); this.name = name; }
            makeNoise() { return '음머~~~~~~~~~`' }
        }
        ```
        
- TS에서는 추상 클래스를 제공합니다.
    
    ```tsx
    abstract class Animal {
        name: string;
        constructor(name: string) { this.name = name; }
        getName() { return this.name; }
        makeNoise() {
            throw new Error('makeNoise() must be implement.');
        }
    }
    
    class Dog extends Animal {
        constructor(name: string) { super(name); this.name = name; }
        makeNoise() { return '멍멍!' }
    }
    
    class Cow extends Animal {
        constructor(name: string) { super(name); this.name = name; }
    }
    
    const dog = new Dog('초코')
    const cow = new Cow('얼룩이')
    
    const animal = new Animal('동물')
    //              ^^^^^^^^^^^^^^
    // Cannot create an instance of an abstract class.
    
    cow.makeNoise()
    // [ERR]: "Executed JavaScript Failed:" 
    // [ERR]: makeNoise() must be implement.
    ```
    

## `implements`

- 클래스가 특정 interface를 만족하는지를 확인하기 위해서 `implements` 문을 사용할 수 있습니다.
    - 여러 개의 interface도 쓸 수 있습니다.
        
        ```tsx
        interface Pingable {
          ping(): void;
        }
        interface Pongable {
          pong(): void;
        }
        
        class Sonar implements Pingable {
          ping() {
            console.log("ping!");
          }
        }
        
        class Ball implements Pingable, Pongable {
          ping() {
            console.log("ping!");
          }
          pong() {
            console.log("pong!");
          }
        }
        ```
        
        - 참고로 상속 `extends` 는 1개 클래스가 1개의 상위클래스밖에 상속받지 못합니다.
    - `implements` 는 오로지 타입 체크만을 위해 사용될 뿐, 클래스나 내부의 메서드의 타입을 자동으로 바꿔주지 않습니다. 즉, 속성을 전부 가졌는지만 체크할 뿐 타입으로 지정한 메서드는 모두 타입 재정의가 필요합니다.
        
        ```tsx
        // @errors: 7006
        interface Checkable {
          check(name: string): boolean;
        }
        
        class NameChecker implements Checkable {
          check(s) {
            // Notice no error here
            return s.toLowercse() === "ok";
            //         ^?
          }
        }
        ```