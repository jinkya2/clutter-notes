-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
# [HEADER] Harry Potter and the repeated refusal to learn the JavaScript Objects in Deep


-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------























-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
# [HEADER] Protoypes, inheritance
------------------------------------------------------------
# Prototypal Inheritance
- If a need arises to reuse existing objects properties and methods as base for new objects, **prototypal inheritance** feature helps to achieve it!
## [[Prototype]]
- In JavaScript, objects have a hidden property [[Prototype]] that is either null or reference to another object called "prototype".
- When we read the property from an object, the object tries to find the property and missing, it tries to find it in its prototype [[Prototype]]. **prorotypal inheritance**
- `__proto__`  is the actual object that is used in the lookup chain to resolve methods, etc.  `prototype`  is the object that is used to build  `__proto__`  when you create an object with  `new`:

```javascript
( new Foo ).__proto__ === Foo.prototype;
( new Foo ).prototype === undefined;
```
- Consider prototype as *father*, in following example, animal is prototype (i.e. father) of rabbit.
- Rabbit prototypically inherits from animal. The properties are knows as inherited properties.
- example
```javascript
    let animal = {eats: true};
    let rabbit = {jumps: true};
    rabbit.__proto__=animal; // rabbit.[[Protoype]] = animal
    console.log(rabbit); // {jumps: true}
    console.log(rabbit.__proto__); // {eats: true}
    console.log(rabbit.jumps); // true
    console.log(rabbit.eats); // true 
```
- Prorotypal Chains can be longer
- example
```javascript
    let animal = {
        eats: true,
        walk: function(){
            console.log('Animal walk')
        }
    }
    let rabbit = {
        jump: true,
        __proto__: animal
    }
    let longEar = {
        earLength: 10,
        __proto__:rabbit
    }
    console.log(longEar.jump); // true
    longEar.walk(); // 'Animal walk'
```

- BUT LIMITATIONS
- - The reference can't go in circles. JavaScript will throw an error if we try to assign __proto__ in a circle.
- - The value __proto__ can either be an object or null. Others are ignored.
- - There can only be a single [[Prototype]]. An object may not inherit from two others.
- __proto__ is hostorical getter/setter for [[Prototype]], __protot is NOT same as internal [[Prototype]]
- The __proto__ property is bit outdated though AND modern JavaScript suggests to use Object.getPrototypeOf/Object.setPrototypeOf

## Writing doesn't use prototype
- Prorotype is used for **READ ONLY**. The write/delete are performed upon objects directly.
- example
```javascript
    let animal = {
        eats: true,
        walk: function(){console.log('walking')}
    }
    let rabbit = {
        __proto__: animal,
        walk: function(){console.log('jumping japang')}
    }
    rabbit.walk(); // jumping japang
```

- But accessor properties are exception
- example

```javascript
    let user = {
        fname: 'A',
        lname: 'B',
        get fullName(){
            return `${this.fname} ${this.lname}`
        },
        set fullName(value){
            [this.fname, this.lname] = value.split(" ")
        }
    };
    let admin = {
        __proto__: user,
        isAdmin: true
    }
    
    console.log(user.fullName); // 'A B'
    console.log(admin.fullName); // 'A B'
    admin.fullName = "C D"
    console.log(admin.fullName); // 'C D'
```

## The value of *this*
- *this* is not affected by the prototypes at all. No matter where the method is found, in object or prototypes, it always the object before thedot.
- SUPER IMPORTANT, try to guess following code output-
- Example
```javascript
    let animal = {
        sleep(){
            this.isSleeping=true;
        },
        walk(){
            if(!this.isSleeping){
                console.log('walking');
            }
        }
    }
    
    let rabbit = {
        __proto__:animal,
        name: "Jessica Rabbit"
    }
    
    rabbit.sleep();
    rabbit.isSleeping; // true
    animal.isSleeping; // undefined
```

- Always remember *this* in each method call would be the corresponding object, evaluated at the call time!

## for...in loop
- The simple thing is for...in loops over inherited properties too.
- exampke

```javascript
    let object1 = {
        a: 1
    }
    
    let object2 = {
        __proto__:object1,
        b: 2
    }
    Object.keys(object2); // ['b']
    for(let prop in object2){console.log(prop)}; // b a
```

- SO to iterate an object safely without inherited properties use ObjectName.hasOwnProperty(prop); if its true, then only use it.
- hasOwnProperty comes from the top parent prototype, which is enumerable: false; thus dont appear in for...in


------------------------------------------------------------
# F.prototype
- New Objects can be created with a constructor function, like new F()
- If F.prototype is an object, then new operator uses it to set [[Prototype]] for the new object.
- F.prototype is only used at the time of new F
- example
```javascript
    let animal = {eats: true};
    function Rabbit(name){
        this.name=name;
    };
    
    Rabbit.prototype = animal;
    let rabbit1 = new Rabbit("Jessica");
    console.log(rabbit1.eats); // true
```
## Default F.prototype, constructor property
- Every function have a default "prototype" property.
- example
```javascript
    function Rabbit(){}; // DEFAULT Rabbit.prototype = {constructor: Rabbit}
    console.log(Rabbit.prototype.constructor == Rabbit);
```
- If we have an existing created object and need to create a new object without knowing the constructor for the new F, then we can use the existing object constructor to create a new Object.
- example
```javascript
    function Rabbit(name){
        this.name=name;
        console.log(this)
    };
    
    let rabbit1 = new Rabbit("White");
    let rabbit2 = new rabbit1.constructor("Black");
```
- BUT javascript itself does not ensure the right constructor value. If we replace whole prototype value, then their will be no constructor value!
- example
```javascript
    function Rabbit(name){this.name=name;};
    Rabbit.prototype={jumps: true};
    let rabbit1 = new Rabbit("One");
    console.log(rabbit1.constructor == Rabbit); // false
```
## Summary 
- The value of F.prototype should always be an object or null.
- The prototype has such a special effect when set on a constructor, and invoked with new, else on regular function prototype is nothing special

------------------------------------------------------------
# Native prototypes

## Object.prototype
- lets check an example, why does it alerts [object object]
```javascript
    let obj = {};
    alert(obj); // [object object]
```
- It basically points to toString method of the Object prototype.
```javascript
	obj.__proto__ == Object.protoype; // true
	obj.toString == Object.prototype.toString; // true
```
## Other built-in prototypes
- Javascript built in Objects like Array, Date and and others also keep methods in prototype. If you create an array [1,2,3], the default new Array() constructor is used internally. Array.prototype become its prototype and provides methods. Memory efficient!
- All built-in prototypes have Object.prototype on the top. Thus "everything inherits from JavaScript"  
![imageName](https://javascript.info/article/native-prototypes/native-prototypes-classes.svg)
```javascript
	arr.__proto = Array.prototype
	arr.__proto.__proto__ = Object.prototype
	arr.__proto__.__proto__.__proto__ = null
```
## Primitives
- Strings, numbers, booleans are not objectsbut if we try to access their properties,  temporary wrapper objects are created using built-in constructors String, Number and Boolean. 
- Values null, and undefined have no Object wrapper. Thus methods and properties are not avilable to them. And their are no corresponding prototypes either.

## Changing native prototypes
- If you want to add a prototype method to a native, you can add it and it will be available to the the all instances of the native. for example
- example
```javascript
	String.prototype.show = function(){alert(this)};
	"Hello".show(); // Show
```
- But on a broader sense, its a bad idea, if two javascript libraries add same method in the prototype, then one is going to override other. 
- Thus, in modern programming, modifying native prototype is allowed only for **POLYFILLS**.
------------------------------------------------------------
# Prototype methods, objects without ```__proto__```
------------------------------------------------------------
- The ```__proto__``` method in modern JavaScript(in browser JavaScript standard) is considered as outdated and somewhat legacy.
- Modern three methods
- 1. Object.create(proto, [descriptor])
- 2. Object.getPrototypeOf(obj)
- 3. Object.setPrototypeOf(obj, proto)
- example 1
```javascript
	let animal =  { eats:  true  };  // create a new object with animal as a 	prototype
	let rabbit = Object.create(animal);_  alert(rabbit.eats);  // true
	alert(Object.getPrototypeOf(rabbit) === animal); // true 
	Object.setPrototypeOf(rabbit, {}); // change the prototype of rabbit to {}
```
- example 2
```javascript
let animal =  { eats:  true  };  
let rabbit = Object.create(animal,  { jumps:  { value:  true  }  });  alert(rabbit.jumps);  // true
```
------------------------------------------------------------
## Brief History
- The "prototype" property of a constructor function has worked from ancient times. Later in 2012, Object.create was added in the standard, but no provision for get/set. Thus brpwser implemented non standard `__proto__` for get/set the property.
- In 2015, Object.setProtottypeOf and Object.getPrototypeOf were added thus making it kind-of deprecated for non-browser environments.
------------------------------------------------------------
## Very plain objects
- [SKIP]
------------------------------------------------------------
## Summary
- The built-in `__proto__` getter/setter is unsafe, if we want to put user generated keys into an object, especially `__proto__`
------------------------------------------------------------

-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------

































-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
# [HEADER] What is "this" in JavaScript? in 100 seconds
- keywords referencing current execution context
- Either global environment,  if used in function refers to an object calling that function
- window, global, globalThis
- Mostly used in a new constructor method
- example 1

    function callThis(){console.log(this)}
        const object1 = {name: 'ajinkya', age: 28, callThis}
        callThis(); // Window{}
        object1.callThis(); // {name: 'ajinkya', age: 28, callThis: ƒ}

- example 2
    function genericFn(){
        console.log(this);
    };
    const object1 = {mood: 'happy'}
    let object1Fn = genericFn.bind(object1)
    genericFn(); // Window{}
    object1Fn(); // {mood: 'happy'}

- example 3
    function Cat(name){
    	this.name = name;
    	this.sayHello = function(){alert(this.name+" says meow")}
    }
    let meow = new Cat("meow");
- strict mode and non-strict mode! this behaves differently in both. 
- example 4
'use strict'
function thisIsFun(){
	console.log(this);	// undefined
} 
- In an arrow function, this based on the enclosing object execution context(the parent context), like in following example
- example 4

    const horse = {
        name: 'ashwa',
        fun1: function(){
            console.log(this);
        },
        fun2: ()=>{console.log(this)} // window in nonstrict AND undefined in strict mode
    };
    
    horse.fun1(); // {name: 'ashwa', fun1: f, fun2: f}
    horse.fun2(); // window in nonstrict AND undefined in strict mode

- bind, call, apply
- bind gives an explicit way to set "this" on a function. 
- example

    function greet(){
        console.log(`${this.name} says Hello`)
    }
    const user1 = {name: 'Ajinkya'}
    user1Greet = greet.bind(user1);
    greet(); //  says Hello
    user1Greet(); // Ajinkya says Hello

- In other cases, you might want to call a function with different this context  but not creating a whole new function.
- example

function greet(){
	console.log(`${this.name} says hello`)
}
const user = {name: 'Ajinkya'}
greet.call(user);	// "Ajinkya says hello"

- The difference between the call and apply is later arguments. In call COMMA separater arguments AND in apply second argument is array. REMEMBER call - COMMA and apply - ARRAY
- Thats IT!

-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------





















-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
# [HEADER] Classes
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------

# Class basic syntax


-------------------------------------------------------------------------------------------------------
# Class inheritance


-------------------------------------------------------------------------------------------------------
# Static properties and methods


-------------------------------------------------------------------------------------------------------
# Private and protected properties and methods


-------------------------------------------------------------------------------------------------------
# Extending built-in class


-------------------------------------------------------------------------------------------------------
# Class checking: "instanceof"


-------------------------------------------------------------------------------------------------------
# Mixins



-------------------------------------------------------------------------------------------------------
