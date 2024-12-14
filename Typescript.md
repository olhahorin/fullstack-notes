## General
* `noImplicitAny` - видасть помилку якщо тайпскріпт не може сам визначити тип по контексту. Якщо ж це значення false то в такому випадку тип просто буде any.
* Optional Properties. `function printName(obj: { first: string; last?: string }) {`
* Type Assertions. Девелопер може сам вказати тип змінної використовуючи as але нюанс такий що не можна таким чином наприклад змінити string на number. TypeScript only allows type assertions which convert to a more specific or less specific version of a type.
* `strictNullChecks`. Якщо якесь значення передане як null і ця опція включена то потрібно додати перевірку (narrowing) на null значення
* Non-null Assertion Operator (Postfix !). Оператор ! вказує тайпскрипту що ми знаємо ще дане значення не null
``` js
function liveDangerously(x?: number | null) {
  // No error
  console.log(x!.toFixed());
}
```


## Narrowing
Допустимо ми маємо змінну об'єкт з опціональними полями або ж змінну з union типом (або ж змінну з типом unknown??).  
Як знати що можна викликати якийсь метод, використати поле цієї змінної?  
Тайпскріпт трекає банальні перевірки JS і вже не видає помилки коли бачить що така перевірка є. Ці перевірки називаються guards.
Guards можуть бути:
* typeof: `if (typeof x === 'string') x.toLowerCase()`
* is true - щоб відсіяти null i undefined
* == === != !== : ``` function example(x: string | number, y: string | boolean) {
  if (x === y) {
    // We can now call any 'string' method on 'x' or 'y'.
    x.toUpperCase();```


* in: ```if ('swim' in x) return x.swim```
* instanseof ```if (exp instanceof RegExp) exp.test(phoneNumber);```
* assignment (треба врахувати що значення яке присвоюється може мати декілька типів, і переприсвоїти змінну можна потім з цими декількома типами)
* Control flow (коли тайпстріпт здогадується наприклад якого типу змінну поверне функція методом виключення)
* кастомні перевірки типу - Type Predicates ```function isCustomType(value: unknown): value is CustomType {...}```

### Discriminated unions
Тут описано випадок коли кілька обєктів мають різні властивості, але мають і щось спільне. 
Що краще робити не окремий тип що обєднує всі ці властивості а Union цих обєктів і далі switch case де з ними буде робитись якась логіка.  
Це показано на прикладі union shapes, до якого входять типи rectangle i circle, вони обидва мають властивіть kind але інші властивості різні.  
`never` дозволяє в цьому кейсі відловити помилки якщо жоден з кейсів не виконується. Типу never не можна присвоїти будь який інший тип, тобто коли ми намагаємося в default щось присвоїти never то виникає помилка що має в теорії вказати що щось іде не так.

## Functions
* Функцію і її параметри можна теж описати як окремий тип.
* Нагадую що в js функція може мати і проперті також, їх теж можна описати, але тоді трохи інший синтаксис
``` ts
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
```
* Також можна описати окремо конструктор і звичайний колл
``` ts
type DescribableFunction = {
  description: string;
  (someArg?: number): string;
  new (someArg: string): SomeObject;
};
```
* ### Generic fucntions  
`Type` parameters are for *relating the types of multiple values*.  
Якщо потрібно щоб функція вертала значення певного типу залежно від типу який на вході або якщо тип одного аргумента залежить від типу іншого можна використати Generic.  
``` ts
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```
Також можна передати кілька значень як Type. І задати обмеження для вхідного типу, наприклад
``` ts
function map<Input extends {length: number}, Output extends keyof Input>(arr: Input[]): Output {
  ...
}
```
* Optional arguments. Задаються за домопогою оператора ? `function f(x?: number) {`
* Functions Overload
Якщо потрібно вказати різні комбінації параметрів які функція отримує і повертає то можна зробити overload
``` ts
function getLength(arr: string[]): number[];
function getLength(line: string): number;
function getLength(value: string | string[]): number | number[] {
    if (Array.isArray(value)) {
        return value.map(item => item.length);
    }
    return value.length;
}
```
* Declaring `this` in a Function. JS не дозволяє функції мати параметр з назвою `this` тому TS дозволяє мати такий параметр і в ньому можна вказати значення this для будь якої(?) функції
* Rest parameters. У функція в JS можна передати багато аргументів одним масивом через синтаксис `...`. Коли позначаємо їх тип через TS то він має бути вказаний як `T[]` що логічно.
* Parameter Destructuring синтаксис такий
``` ts
function sum({ a, b, c }: { a: number; b: number; c: number }) {
  console.log(a + b + c);
}
```
* Тип `void`. Хоча він і означає що функція нічого не поветає він не забороняє функції таки щось певернути і присвоїти це значення якійсь змінній. Але при цьому ця змінна всеодно буде мати тип `void`.

# Objects
Об'єкт можна позначити як літерал, інтерфейс або type
### Properties of the object
Можна задати три речі: її тип, чи ця пропертя обов'язкова чи опціональна (`?`), чи вона `readonly`  
* Щодо того чи ця пропертя опціональна, чи означає це що якщо засетати якусь пропертю опціональною і потім її викликати десь то TS видасть помилку, бо на практиці я її не бачу
``` ts
interface Person {  
    name: string;  
    age?: number;
}
function greet(person: Person) {
    console.log(`Hello ${person.name} ${person.age}`); // не бачу тут помилки
}
```
* `readonly` - не можна змінити значення цієї проперті напряму. Якщо ця пропертя обʼєкт то його проперті вже можна міняти.
Хитрий нюанс з `readonly`: якщо створити два аналогічні інтерфейси, один буде мати всі проперті readonly a інший такі самі звичайні. Потім створюємо об'єкт з другим інтерфейсом де проперті звичайні. Потім присвоюємо його змінній яка має тип першого інтерфейсу. І при присвоєнні і головне при зміні значень проперті помилки не буде.
* Проперті назва яких наперед не відома можна задати як шаблонну пропертю для типу чи інтерфейсу. Вона може бути задана як string, number, symbol, template string або як юніон що включає тільки ці типи
``` ts
type IConfig = {
    [version: `${number}.${number}`]: string;
}
const config: IConfig = {
    1.2: 'off',
};
```

### Обхідні шляхи якщо ми хочемо передати якісь проперті які відсутні в інтерфейсі
* вже відоме й очевидне `as`
* додати в інтерфейс шаблонну пропертю як `[propName: string]: unknown`
* передати змінну замість літералу
``` ts
interface SquareConfig {
    color: string;
    width: number;
  }
   
  function createSquare(config: SquareConfig): { area: number } {
    return { area: config.width ? config.width * config.width : 20,};
  }

  const squareOptions = { color: "red", width: 100, opacity: 0.5 }
  let mySquare = createSquare(squareOptions); // тут TS не видає помилку але якщо передати замість squareOptions аналогічний обєкт то тоді видасть 
```

## Intersection
Можна створити тип який буде Intersection кількох типів чи інтерфейсів.
Щось подібне можна зробити якщо створити кілька інтерфейсів які будуть мати одинакові назви.
Між цими двома варіантами є різниця в тому як хендляться конфлікти:
1. Якщо створюються два інтерфейси з одинаковити іменами і в них є однакова пропертя яка має різні типи то TS видасть помилку
2. Якщо зробити intersection (&) двох типів в яких є однакова пропертя з різними типами то TS спробує якось їх скомбінувати, що в результаті часто закінчиться тим що цим змінним привоється тип `never`

## Generic types

