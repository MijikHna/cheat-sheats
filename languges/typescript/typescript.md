### TypeScript

TypeScirpt Resources

* [Matt Pocock](https://totaltypescript.com/)
* [Matt Pocock Youtube](https://youtube.com/@mattpocockuk)

##### Descriminated Union

```typescript
type User = {
    
    kind: "user";
} | {
    kind: "admin";
    adminPassword: string;
} | {
    kind: "superAdmin";
    superAdminPassword: string;
}
type UserRole = User["kind"];
```

##### Generics

```typescript
type FetchData<T> = {
    data: T;
    loading: boolean;
    error: boolean;
}

const fetchData = <T>(url: string): Promise<T> => {
    return fetch(url).then((response) => response.json())
}

const user = await fetchData<{id: string; name: string}>("SOME_URL");
```
##### Satisfies

```typescript

type RouteConfig = Record<string, fetch: () => Promise<any> }>

const routes: RouteConfig = { // overriing the infer
    "/users": {
        fetch: () => {},
    },
    "/admin": {
        fetch: () => {},
    },"   
    "/admin/settings": {
        fetch: () => {},
    }
}

// typescript loses autocomplete
routes["/admin/settings"].fetch();
routes["asfasf"].fetch(); // typescript will not mark as error

// FIXES ABOVE
const routes = {
    "/users": {
        fetch: () => {},
    },
    "/admin": {
        fetch: () => {},
    },"   
    "/admin/settings": {
        fetch: () => {},
    }
} satisfies RouteConfig; // infer + satisfy Type

routes["/admin/settings"].fetch();
routes["asfasf"].fetch(); // typescript will not mark as error
```

##### Dependent Props 

> Discriminated Union

```typescript
type State = { count: number }

type Action = {
    type: "increment_by"
    amount: number;
} | 
{ 
    type: "decrement"
}

const reducer = (state: State, action: Action) => {
    switch(action.type) {
        case "increment_by": {
            return { count: state.count + aciton.amount }
        }
        case "decrement": {
            return { count: state.count - 1}
        }
        default: {
            return state;
        }
    }
}

const newState = reducer({count: 0}, {type: "increment", amount: 5});
```

##### Exhaustive Check for Default Case in Switch Case

```typescript
type CarBrand = 'bwm' | 'audi' | 'toyota'

interface CarBase {
    brand: CarBrand;
    year: number;
};

interface Bmw extends CarBase {
    brand: 'bwm';
    climateControl: boolean;
}

interface Audi extends CarBrand {
    brand: 'audi';
    conditioner: string;
}

interface Toyota extends CarBrand {
    brand: 'toyota';
    model: string;
}

type Car = Bmw | Audi | Toyota;

switch(car.brand) {
    case 'bwm': {
        // ts supports Bwm
        break;
    }
    case 'audi': {
        // ts suports Audi
        break;
    }
    default: {
        // ts supports never => create helper for this
        exhaustiveCheck(car);
    }
}

function exhaustiveCheck(x: never): never {
    throw new Error("exhaustiveCheck: should not reach here" + x);
}
```

##### Type Guards

```typescript
type CarBrand = 'bwm' | 'audi' | 'toyota'

interface CarBase {
    brand: CarBrand;
    year: number;
};

interface BmwBase extends CarBase {
    brand: 'bwm';
    climateControl: boolean;
    model: 'x5' | 'x7';
}

interface BmwX5 extends BmwBase {
    model: 'x5';
    color: 'red' | 'blue';
}

interface BmwX7 extends BmwBase {
    model: 'x7';
    color: 'red' | 'blue' | 'black';
}
type Bmw = BmwX5 | BmwX7;

interface AudiBase extends CarBrand {
    brand: 'audi';
    conditioner: string;
    model: 'a4' | 'a6';
}

interface AudiA4 extends AudiBase {
    brand: 'toyota';
    model: 'a4';
    color: 'red' | 'blue';
}

interface AudiA6 extends AudiBase {
    brand: 'toyota';
    model: 'a6';
    color: 'red' | 'blue' | 'black';
}

type Audi = AudiA4 | AudiA6;

function fn(car: Car) {
    //BAD: inside if ts doesn't support types
    if (car.brand === 'bwm') {
        // do something
    }
    else if (car.brand === 'audi') {
        // do something
    }

    // GOOD
    if (isBmw(car)) {
        // ts supports Bwm
    }
    else if (isAudi(car)) {
        // ts suports Audi
    }
}

function isBmw(car: Car): car is Bmw {
    return car.brand === 'bwm';
}

function isAudi(car: Car): car is Audi {
    return car.brand === 'audi';
}

```

##### Enums vs Const Enums vs Const Objects

```typescript

enum UserRole {
    User = "user",
    Admin = "admin",
}

enum UserRoles {
    User = "user",
    Admin = "admin",
}

const enum UserRole2 {
    User = "user",
    Admin = "admin",
}

console.log(typeof UserRole); // object
console.log(typeof UserRole2); // error const enum does not exist at runtime

function fn(role: UserRole) {
    // do something
}

fn(UserRoles.User); // NOT OK

for (const role in UserRole2) { // NO keys
    // do something
}

const UserRole3 = {
    User: "user",
    Admin: "admin",
}

function fn2(role: UserRole3) { // ERROR: UserRole3 is is const
    // do something
}

const UserRole4 = {
    User: "user",
    Admin: "admin",
} as const; // ts makes its attributes readonly
 
type UserRoleKeys = keyof typeof UserRole4; // "User" | "Admin",

fn('user'); // OK

// Generics for cosnt objects

type ValueOf<T> = T[keyof T];
type UserRoleValues = ValueOf<typeof UserRole4>; // "user" | "admin"

```
##### Return Type Inference

```typescript
// fn is JS Library function without typings

// ReturnType
function fn(arg1: string, arg2: number) {
    return 5;
}

type FnReturnType = ReturnType<typeof fn>; // number

const result = fn(); // number

// Example return type of setTimeout
const FnReturnType2 = ReturnType<setTimeout> // void

const result2: FnReturnType2 = 'sss' // error
const result3: FnReturnType2 = 5 // OK

// Param Type 
type FnRestParams = Parameters<typeof fn>; // [string, number]
const args: FnRestParams = ['sss', 5]; // OK

// Contitional Types

type ConditionalType<T> = T extends string ? number : boolean;

const value: ConditionalType<string> = 5  // OK
const value: ConditionalType<string> = true; // error
const value: ConditionalType<number> = true; // OK

type Data = { value: string }
type Car = {model: string}
type DataOrCar<T> = T extends Data ? Data : Car;

function toDo<T> (arg: T): DataOrCar<T> {
    throw Error("Not implemented");
}

const value1 = todo('123')
value1. // ts suggests Data

const value2 = todo(123)
value2. // ts suggests Car
```

##### Tuples

```typescript

const [state, setState] = useState({count: 0, name: 'test'}); // React uses this approach

type Tuple = [string , number, boolean]; // define Tuple = array of size 3 which has string, number and boolean values

const tuple: Tuple = ['test', 5, true]; // OK
const tuple2: Tuple = [5, 5, true]; // Bad

type Tuple2 = [string, number, boolean, ...number[]]; // define Tuple = array of size 1 which has string and any number of numbers
const tuple3: Tuple2 = ['test', 5, true, 5, 6, 7, 8]; // OK
```

##### Immutable, Readonly

```typescript

interface User {
    name: string;
}

const user: User = {
    name: 'test'
}

function uesrFn(user: Readonly<User>) {
    user.name = 'test2'; // error
}

```

##### Utility Types

```typescript

intrface User {
    name: string;
    age: number;
    birthday: {
        day: number;
        month: number;
        year: number;
    }
}

type NewUser = Ommit<User, 'birthday'>; // {name: string, age: number}
type NewUser2 = Pick<User, 'name' | 'age'>; // {name: string, age: number}

type NewUser3 = Pick<User, 'name' | 'age'>; // {name: string, age: number}

type CarBrand = 'bwm' | 'audi' | 'toyota'

type GermanCar = Exclude<CarBrand, 'toyota'>; // 'bwm' | 'audi' //
type GermanCar2 = Extract<CarBrand, 'bwm' | 'audi'>; // 'bwm' | 'audi'

type Birthday = Pick<User, 'birthday'>; // {birthday: {day: number, month: number, year: number}}

const birthday: Birthday = {
    birthday: {
        day: 1,
        month: 1,
        year: 1990
    }
}

type ValueOf<T> = T[keyof T];
type BirthdayType = ValueOf<Pick<Birthday, 'birthday'>>; // {day: number, month: number, year: number}

const birthday: BirthdayType = {
    day: 1,
    month: 1,
    year: 1990
}

type NewUser = Partial<User>; // {name?: string, age?: number, birthday?: {day: number, month: number, year: number}}

type DeepPartial<T> = {
    [P in keyof T]?: T[P] extends DeepPartial<T[P]> ;
};

type NewUser = DeepPartial<User>; // {name?: string, age?: number, birthday?: {day?: number, month?: number, year?: number}}
```

##### Indexed Access Types

```typescript

type ContactStatus = "active" | "inactive" | "new";

interface Address {
    street: string;
    province: string;
    postalCode: string;
}

interface Contact {
    id: number;
    name: string;
    status: ContactStatus;
    address: Address;
}

type Awesome = Contact["address"]["postalCode"] //

interface ContactEvent {
    contactId: Contact["id"];
}

interface ContactDeletedEvent extends ContactEvent {}

interface ContactStatusChangedEvent extends ContactEvent {
    oldStatus: Contact["status"];
    newStatus: Contact["status"];
}

```
> Advanced usage of above example (pretty interesting)

```typescript
interface ContactEvents {
    deleted: ContactDeletedEvent;
    statusChanged: ContactStatusChangedEvent;
}

function getValue<T, U extends keyof T>(source: T, propertyName: U) {
    return source[propertyName];
}

function handleEvent<T extends keyof ContactEvents>(
    eventName: T,
    handler: (evt: ContactEvents[T]) => void
) {
    if (eventName === "statusChanged") {
        handler({ contactId: 1, oldStatus: "active", newStatus: "inactive" })
    }
}

handleEvent("statusChanged", evt => evt)
```

##### Dynamic, Limited Types

```typescript
let x: Record<string, string | number | boolean | Function> = { name: "Dave" }
x.number = 1234
x.active = true
x.log = () => console.log("Hello, world!")
```

> Interesting example

```typescript
type ContactQuery = 
    Partial<
        Pick<
            Record<keyof Contact, Query>,
            "id" | "name"
        >
    >

type RequiredContactQuery = Required<ContactQuery>

type ContactQuery = Partial<Record<keyof Contact, Query>>;
type ContactQuery = {
    [TProp in keyof Contact]?: Query<Contact[TProp]>
}
```

##### Method Decorators

* `target` - Object to which decorator is applied to. In case of method-decorator Instance of the Class to which the method belongs
* `property` - Name of the property to which decorator is applied to
* `descriptor` - Object containing the meta data of the property. In case of method-decorator `value` contains the method itself

*
```typescript
function authorize(target: any, property: string, descriptor: PropertyDescriptor) {
    return { 
        //...
    } as PropertyDescriptor; // return new descriptor => method-descriptor will be replaced with new one
}


function authorize(target: any, property: string, descriptor: PropertyDescriptor) {
    const wrapped = descriptor.value

    descriptor.value = function () {
        if (!currentUser.isAuthenticated()) {
            throw Error("User is not authenticated");
        }

        try {
            return wrapped.apply(this, arguments);
        } catch (ex) {
            throw ex;
        }
    }
}
```

##### Decorator Factories

Idea: Wrap actual decorator into another function which will accept some parameters and return actual decorator

```typescript
function authorize(role: string) {
    return function authorizeDecorator(target: any, property: string, descriptor: PropertyDescriptor) {
        const wrapped = descriptor.value

        descriptor.value = function () {
            if (!currentUser.isAuthenticated()) {
                throw Error("User is not authenticated");
            }
            if (!currentUser.isInRole(role)) {
                throw Error(`User not in role ${role}`);
            }

            return wrapped.apply(this, arguments);
        }
    }
}
```

Decorator has now attributes: `@authorizer('admin')`

##### Class Decorators

* `target` - is class-constructor to which decorator is applied to

```typescript
// Modify class-constructor. Actually not really useful
function freeze(constructor: Function) {
    Object.freeze(constructor)
    Object.freeze(constructor.prototype)
}

// Most decorators are used to dynamically add some properties or behavior to the class. Decorator here returns new class
//function singleton(constructor: any) { 
function singleton<T extends { new(...args: any[]): {} }>(constructor: T) { // T derives from class which uses this decorator, Generic describes constructor
    return class Singleton extends constructor {
        static _instance = null;

        constructor(...args) {
            super(...args);
            if (Singleton._instance) {
                throw Error("Duplicate instance")
            }

            Singleton._instance = this
        }
    }
}

@freeze
@singleton
class {...}
```


##### Property Decorators

* `target` - Instance of the class which property is decorated
* `key` - name of the property which is decorated

```typescript
function auditable(target: object, key: string | symbol) {
    // get the initial value, before the decorator is applied
    let val = target[key];

    // then overwrite the property with a custom getter and setter
    Object.defineProperty(target, key, {
        get: () => val,
        set: (newVal) => {
            console.log(`${key.toString()} changed: `, newVal);
            val = newVal;
        },
        enumerable: true,
        configurable: true
    })
}

```

##### Define Global Types

```typescript
// global.d.ts
declare global {
    /** this formats a date value to a human-readable format */
    function formatDate(date: Date): string
}

export {}

```

##### Declaration Merging

```typescript

class Customer {
    //...
}

interface Customer {
    /** saves the customer somewhere */
    save(): void
}

// ts merges these two declarations into one

const customer = new Customer()
customer.save = function() {}

// in case of global window object extend window within global.d.ts

declare global {
    interface Window {
        MY_VAR: string
    }
}

export {}

const myVar = window.MY_VAR
```
