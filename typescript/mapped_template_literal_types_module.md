# TypeScript - Mapped/Template Literal Types, Modules, Utility Types

# 매핑된 타입 (Mapped Types)

- 매핑된 타입은 인덱스 시그니처 구문을 이용해 객체의 키와 값 타입을 매핑하는 수단을 제공합니다.
    
    ```tsx
    type OnlyBoolsAndHorses = {
      [key: string]: boolean | Horse;
    };
    const conforms: OnlyBoolsAndHorses = {
      del: true,
      rodney: false,
    };
    ```
    
- 이를 통해 반복되는 타입을 안전하게 선언할 수 있습니다.
    - 객체의 키와 값에 타입을 제공합니다.
    - keyof 타입 연산자와 조합하면 키 이름별로 매핑할 수 있는 값 타입을 제한할 수 있습니다.
    
    ```tsx
    type OptionsFlags<Type> = {
      [Property in keyof Type]: boolean;
    };
    
    type FeatureFlags = {
      darkMode: () => void;
      newUserProfile: () => void;
    };
     
    type FeatureOptions = OptionsFlags<FeatureFlags>;
    
    const featureOptions: FeatureOptions = {
        darkMode: true,
        newUserProfile: false
    }
    ```
    

### 매핑 지정자 (mapping modifier)

- 필드를 선택형 `?` 이나 `readonly` 로 표시할 수 있습니다.

```tsx
type Account = {
    id: number;
    isEmploy: boolean;
    notes: string[];
}

// 모든 필드를 선택형으로 만듭니다
type OptionalAccount = {
    [K in keyof Account]?: Account[K]
}
// 모든 필드를 읽기전용으로 만듭니다
type ReadonlyAccount = {
    readonly [K in keyof Account]: Account[K];
}
```

- 매핑된 타입에서만 사용할 수 있는 특별 타입 연산자인 `-` 로 그 표시를 제거할 수도 있습니다.

```tsx
// 모든 필드를 다시 writable하게 만듭니다
type Account2 = {
    -readonly [K in keyof Account]: Account[K];
}
// 모든 필드를 다시 필수형으로 만듭니다
type Account3 = {
    [K in keyof Account]-?: Account[K];
}
```

### `as` 사용해서 재매핑하기

- TypeScript 4.1 부터 `as` 를 매핑된 타입에 사용해서 재매핑할 수 있습니다.
    
    ```tsx
    type MappedTypeWithNewProperties<Type> = {
        [Properties in keyof Type as NewKeyType]: Type[Properties]
    }
    ```
    
- 템플릿 리터럴 타입과 함께 사용하면 새로운 속성명을 만들 수 있습니다.
    
    ```tsx
    type Getters<Type> = {
        [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
    };
     
    interface Person {
        name: string;
        age: number;
        location: string;
    }
     
    type LazyPerson = Getters<Person>;
    
    const lazyPerson: LazyPerson = {
        getName: () => 'jeesoo';
        getAge: () => 34;
        getLocation: () => 'Kwanak-gu';
    }
    ```
    

- 내장된 utility 타입에서는 유용한 [매핑된 타입들](https://www.notion.so/TypeScript-Mapped-Template-Literal-Types-Modules-Utility-Types-8dc818ab907245b0944cf50d22203fbb)을 제공하고 있습니다.

- 퀴즈
    
    ```tsx
    type WeekDay = 'MON' | 'TUE' | 'WED' | 'THU' | 'FRI'
    type Day = WeekDay | 'SAT' | 'SUN'
    
    let nextDay = { MON: 'TUE', TUE: 'WED', WED: 'THU', THU: 'FRI', FRI: 'SAT' }
    
    // nextDay의 타입을 mapped type으로 표시해 보세요.
    ```
    

---

# 템플릿 리터럴 타입 (Template Literal Types)

- 탬플릿 리터럴 타입은 2020년 말 TS 4.1 출시되며 추가된 기능입니다.
- 기존 TS의 문자열 리터럴 타입을 기반으로, 새로운 타입을 만드는 도구입니다. 특히 유니온 타입과 자주 사용될 수 있습니다.
    
    ```tsx
    // 예시 1. 간단한 형태
    type World = "world";
    type Greeting = `hello ${World}`;
    
    // 예시 2. 하나의 union 타입
    type Lyrics = "I got a boy"
    type Adjective = "멋진" | "착한" | "핸썸" | "내 맘 다 가져간"
    type SNSDIGotABoyLyrics = `${Lyrics} ${Adjective}`;
    // ?
    
    // 예시 3. 여러 개의 union 타입
    type EmailLocaleIDs = "welcome_email" | "email_heading";
    type FooterLocaleIDs = "footer_title" | "footer_sendoff"; 
    type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
    // ?
    
    type Lang = "en" | "ja" | "pt";
    type LocaleMessageIDs = `${Lang}_${AllLocaleIDs}`;
    // ?
    
    ```
    
    ```tsx
    // 예시 4. 반복되는 타입 정의 없애기
    
    // 문제상황
    // 이벤트 이름이 하나 추가될 때마다....
    type EventNames = 'click' | 'doubleClick' | 'mouseDown' | 'mouseUp';
    
    type MyElement = {
        addEventListener(eventName: EventNames, handler: (e: Event) => void): void;
    
        // onEvent() 도 하나씩 추가해줘야 한다
        onClick(e: Event): void;
        onDoubleClick(e: Event): void;
        onMouseDown(e: Event): void;
        onMouseUp(e: Event): void;
    };
    ```
    
- 예시 4의 해결 방법
    
    ```tsx
    // CapitalizedEventNames = 'Click' | 'DoubleClick' | ...;
    type CapitalizedEventNames = ?
    
    // type HandlerNames = 'onClick' | 'onDoubleClick' | 'onMouseDown' | 'onMouseUp';
    type HandlerNames = ?
    
    type Handlers = {
     // ?  
    };
    
    type MyElement = Handlers & {
      addEventListener: (eventName: EventNames, handler: (event: Event) => void) => void;
    };
    ```
    
- `Uppercase<StringType>` , `Lowercase<StringType>` , `Capitalize<SringType>` , `Uncapitalize<StringType>` 을 사용하면 문자열을 좀 더 쉽게 조작할 수 있습니다.

# Modules

## JavaScript module의 흐름

- 원래 JS가 처음 생겼을 때는 모듈 시스템을 지원하지 않았습니다. 그래서 모든 것이 전역에 정의되었기에, 프로그램을 만들고 확장하기가 어려웠습니다.
    - 사용할 수 있는 변수명이 고갈됨
    - 외부에서 사용가능한 기능과 사용하면 안되는 기능을 구분할 수가 없음
    - 캡슐화를 적절하게 할 수 없음
- 즉시실행함수로 모듈을 흉내내기도 했고, 이후 자바스크립트를 레이지로딩하는 모듈 로더가 하나둘씩 개발되었습니다.
- 2009년 node.js가 개발되면서 모듈의 필요성이 생겨났고, 당시 CommonJS 모듈 표준을 적용했습니다.
    - CommonJS는 `require` , `module.exports` syntax를
- 비슷한 시점에 웹에서는 AMD 모듈 표준이 인기를 얻고 있었습니다.
- 2011년 Browserify가 출시되면서 프론트엔드에서도 CommonJS를 사용할 수 있게 되면서 CommonJS가 표준처럼 자리잡았습니다.
- ES2015에 와서 `import` , `export` syntax를 사용하는 새로운 표준 모듈 문법이 소개되었습니다.
    - 다만 표준이 제정된 시점에는 브라우저에서 적용하지 않는 경우가 있어, 브라우저에 맞게 컴파일하는 것이 필요했습니다.
    - 2020년에 들어, 대부분의 웹 브라우저와 JS 런타임에서 ES 모듈을 사용할 수 있게 되었습니다.

## TypeScript에서 module

- TS에서 모듈 기반의 코드를 작성할 때 고려해야 할 점 3가지
    1. 구문 (Syntax) → 무언가를 import/export 하고 싶을 때 어떤 구문을 써야하나?
    2. 모듈 해석(Module Resolution) → 모듈 이름(혹은 모듈 경로)와 디스크 내 파일 간에 어떤 관계를 가지나?
    3. Module Output Target → 컴파일 후 생성된 자바스크립트 모듈은 어떻게 생겨야하나?
    

### 구문 (Syntax)

- ES module
    
    ```tsx
    // @filename: hello.ts
    export const hi = 'hi!'
    export default function hello() { console.log('hello!') }
    
    ---
    
    import hello, { hi } from './hello.js'
    hello()
    console.log(hi)
    ```
    
    ```tsx
    // @filename: maths.ts
    export var pi = 3.14;
    export let squareTwo = 1.41;
    export const phi = 1.61;
     
    export class RandomNumberGenerator {}
     
    export function absolute(num: number) {
      if (num < 0) return num * -1;
      return num;
    }
    
    ---
    
    import { pi, phi, absolute } from "./maths.js";
     
    console.log(pi);
    const absPhi = absolute(phi);
    ```
    
    ```tsx
    import { pi as π } from "./maths.js";
    import * as math from "./maths.js";
    ```
    
    ```tsx
    import "./maths.js";
    ```
    
- type/interface를 import/export 하기
    - type은 JS value와 마찬가지로 import/export가 가능합니다.
        
        ```tsx
        // @filename: animal.ts
        export type Cat = { breed: string; yearOfBirth: number };
         
        export interface Dog {
          breeds: string[];
          yearOfBirth: number;
        }
         
        // @filename: app.ts
        import { Cat, Dog } from "./animal.js";
        type Animals = Cat | Dog;
        ```
        
    - type을 명시해서 import 하는 방법은 아래와 같이 2가지가 있습니다.
        
        ```tsx
        // @filename: animal.ts
        export type Cat = { breed: string; yearOfBirth: number };
        export type Dog = { breeds: string[]; yearOfBirth: number };
         
        // @filename: valid.ts
        import **type** { Cat, Dog } from "./animal.js";
        export type Animals = Cat | Dog;
        
        ---
        
        // @filename: app.ts
        import { createCatName, type Cat, type Dog } from "./animal.js";
         
        export type Animals = Cat | Dog;
        const name = createCatName();
        ```
        
        - JS value도 import하기를 원한다면, 후자의 방식을 사용하면 됩니다.

## esModuleInterop

- CommonJS와 ES module 간의 호환 문제가 있어, CommonJS로 되어 있는 npm 라이브러리(ex. lodash, moment.js 등)를 ES module을 사용하는 프로젝트에서 사용하려고 할 때 문제가 생깁니다.
    - default import
        
        ```tsx
        import moment from 'moment';
        moment();
        
        // node_modules/moment/ts3.1-typings/moment.d.ts:784:1
        // export = moment;
        
        // This module is declared with using 'export =', and can only be used with a default import when using the 'esModuleInterop' flag.
        ```
        
    - namespace를 포함시킬 때
- 이때, tsconfig의 flag로 `esModuleInterop` 을 `true` 로 바꿔주면 해결됩니다.

## 모듈 해석 (module resolution)

- TS에서는 모듈명 (ex. `import { func } from './file` 의 경로명 등) 을 해석하는 알고리즘을 제어할 수 있습니다.
    - tsconfig의 flag로 moduleResolution가 있고, 아래 2가지 모드를 제공합니다.
        - **node**
            - node.js와 같은 알고리즘을 사용해 모듈을 해석
                - 접두어로 `.`, `/`, `~` 중 하나를 사용하면, 현재 파일로부터의 상대경로와 절대경로 해석
                - 접두어가 없으면 `node_modules` 에서 경로를 로드
            - 항상 이 모드를 씁시다!
        - **classic**
            - 상대경로는 node 모드처럼 동작하지만, 접두어를 사용하지 않으면 `node_modules` 가 아니고 상위 디렉터리로 올라가며 검색
            - 절대 사용하지 맙시다!

## 모듈 output 옵션

- TS 파일을 어떤 버전의 JS 파일로 변환할 것인지 지정이 가능합니다.
    1. target
        1. JS를 특정 JS 버전 (ex. ES5, ESNext 등) 으로 변환
    2. module
        1. 어떤 모듈 시스템을 쓸 것인지 (ex. CommonJS, UMD, ES2015, ESNext 등)
        2. 모듈끼리 상호작용하는 방식이 달라지게 됨

---

# Utility Types

## `Partial<Type>` , `Required<Type>`

- `Partial`은 객체의 모든 속성을 선택형으로 만듭니다.
- `Required`는 객체의 모든 속성을 필수로 만듭니다. `Partial` 과는 반대입니다.
- 객체 타입에 사용할 수 있습니다.

```tsx
interface Todo {
  title: string;
  description: string;
}
 
function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}
 
const todo1 = {
  title: "organize desk",
  description: "clear clutter",
};
 
const todo2 = updateTodo(todo1, {
  description: "throw out trash",
});

---

interface Props {
  a?: number;
  b?: string;
}
 
const obj: Props = { a: 5 };
 
const obj2: Required<Props> = { a: 5 };
// Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'.
```

## `Readonly<Type>`

- 객체 모든 속성을 읽기전용으로 만들거나, 배열이나 튜플을 읽기전용으로 만듭니다.
- 배열, 객체, 튜플 타입에 사용할 수 있습니다.

```tsx
interface Todo {
  title: string;
}
 
const todo: Readonly<Todo> = {
  title: "Delete inactive users",
};
 
todo.title = "Hello";
// Cannot assign to 'title' because it is a read-only property.
```

## `Record<Keys, Type>`

- 키 타입과 값 타입을 연결하여 매핑합니다.
- 객체 타입에 사용할 수 있습니다.

```tsx
interface CatInfo {
  age: number;
  breed: string;
}
 
type CatName = "miffy" | "boris" | "mordred";

const cats: Record<CatName, CatInfo> = {
 // ? 어떻게 만들어야 해당 타입에 맞는 객체일까요?
}
```

## `Pick<Type, Keys>` , `Omit<Type>`

- `Pick` 은 다른 객체의 키 중 일부를 가지는, 해당 객체의 서브 타입입니다.
- `Omit` 은 다른 객체의 키 중 명시된 키를 제외하고 가지는, 해당 객체의 서브 타입입니다.
- 객체 타입에 사용할 수 있습니다.
    
    ```tsx
    interface Todo {
      title: string;
      description: string;
      completed: boolean;
    }
     
    type Todo1Preview = Pick<Todo, "title" | "completed">;
     
    // ? Todo1Preview 타입의 객체가 가지고 있는 속성은?
    
    type Todo2Preview = Omit<Todo, "description">;
     
    // ? Todo2Preview 타입의 객체가 가지고 있는 속성은?
    ```
    

---

## `Exclude<UnionType, ExcludedMembers>` , `Extract<Type, Union>`

- `Exclude` 는 다른 타입에서 한 타입을 제외합니다.
- `Extract` 는 다른 타입에 할당할 수 있는 서브타입을 선택합니다.
- 유니온 타입에 사용할 수 있습니다.
    
    ```tsx
    type T0 = Exclude<"a" | "b" | "c", "a">;
    //    ^? typeT0 = "b" | "c"
    type T1 = Exclude<"a" | "b" | "c", "a" | "b">;
    //    ^? typeT1 = "c"
    type T2 = Exclude<string | number | (() => void), Function>;
    //  ?
    
    ---
    
    type T0 = Extract<"a" | "b" | "c", "a" | "f">;
    //    ^? type T0 = "a"
    type T1 = Extract<string | number | (() => void), Function>;
    // ?
    ```
    

## `NonNullable<Type>`

- null, undefined를 타입에서 제외합니다.
- nullable 타입에서 사용가능합니다.

```tsx
type T0 = NonNullable<string | number | undefined>;
//    ^? type T0 = string | number
type T1 = NonNullable<string[] | null | undefined>;
//    ^? type T1 = string[]
```

## `Parameters<Type>` , `ReturnType<Type>`

- `Parameters` 는 함수 매개변수 타입으로 구성된 튜플을 생성합니다.
- `ReturnType` 은 함수의 반환 타입으로 구성된 타입을 생성합니다.
- 함
    
    ```tsx
    declare function f1(arg: { a: number; b: string }): void;
     
    type T0 = Parameters<() => string>;
         // type T0 = []
    type T1 = Parameters<(s: string) => void>;
         // type T1 = [s: string]
    type T3 = Parameters<typeof f1>;
    		/* type T3 = [arg: {
    	    a: number;
    	    b: string;
    		}] */
    
    ---
    
    type T0 = ReturnType<() => string>;
         // type T0 = string
    type T1 = ReturnType<(s: string) => void>;
         // type T1 = void
    type T3 = ReturnType<<T extends U, U extends number[]>() => T>;
         // type T3 = number[]
    type T4 = ReturnType<typeof f1>;
         /* type T4 = {
    	    a: number;
    	    b: string;
    		} */
    ```
    
    ## `InstanceType<Type>`
    
    - 클래스 생성자로 인스턴스화 (new) 해서 얻을 수 있는 인스턴트 타입을 생성합니다.
    - 클래스 생성자 타입에서 사용 가능합니다.
    
    ```tsx
    class C {
      x = 0;
      y = 0;
    }
     
    type T0 = InstanceType<typeof C>;
         // type T0 = C
    ```