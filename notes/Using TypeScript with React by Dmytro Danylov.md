<style>
img[alt=pict04] {
   width:40%;
}
img[alt=pict06] {
   width:60%;
}
img[alt=pict08] {
   width:80%;
}
</style>



# Using TypeScript with React by Dmytro Danylov

#### Section 1 Intro

<details>
<summary>Expand</summary>

1. TS compiler configuration = "tsconfig.json". 
    1. Add file to root directory with the "package.json"

1. How it works
    1. When you launch `tsc` compiler
        1, `tsc` reads the "tsconfig.json" from the folder you launched from
        1. `tsc` gets the instructions about how to compile your project 
            1. which source files to compile
            1. where to store the output
            1. etc.
    1. `tsc` tell the compiler where to look for config using "-p" option:
        `tsc -p tsconfig.server.json`
1. Structure of the "tsconfig.json" looks like this:
    ```
    {
        "compilerOptions": {
            The options from the "Compiler Options" doc(https://www.typescriptlang.org/docs/handbook/compiler-options.html), like:
            "target": "es6",
            "outDir": "dist"
        }
        Other options, like:
        "files": [],
        "include": [],
        "exclude": []
    }
    ```

 1. most common compiler/tsconfig options:
    1. `"target": "es6"`
        1. `"target"` indicates version of ECMAScript you would like to compile your code to.
            1. Choose option based on environment you would like to run your code in. 
            1. example
                1. If you are targeting very old browsers
                    1. you might want to choose “ES5”. 
                1. If you are going to run your code in Node 12.10
                    1. you can set the target to “ES2019”,
                        1. according to https://node.green website ...
                            1. ES2019 is supported by this version of Node.
        1. Common values: 
            1. ES5
            1. ES6/ES2015
            1. ES2016
            1. ES2017
            1. ES2018 
            1. ES2019 
            1. ES2020
            1. ESNext
                1. Targets latest supported ES proposed features according to the “tc39 / proposals” document: https://github.com/tc39/proposals

    1. `"lib": ["dom", "dom.iterable", "esnext"]`
        1. List of library files to include during compilation
            1. Library files tell which features available in your TypeScript code. 
            1. Example... `DOM` library file
                1. Tells `tsc` how the DOM API looks
                1. When used...
                    1. `tsc` knows how to check JS DOM API like `document.querySelector("a")`

    1. `"strict": true`
    
        1. Enables all strict type checking options
            1. example
                1. noImplicitAny
                1. noImplicitThis
                1. alwaysStrict
                
    1. `"module": "commonjs"`
        1. Sets module system to be used in compiled (.JS) files
            1. Should pick module system that is supported by the environment where your code will run. 
                1. Example
                    1. Node.js uses CommonJS.
                1. code for the browser, 
                    1. have to combine `module` option with the `outFile` option. 
                    1. `outFile` tells `tsc` to bundle all your code into a single file
                        1. can include into an HTML file using a `<script>` tag.
                            1. in order to use compiled JS file in HTML file
                                1. should set up a corresponding module loader
                                1.like https://requirejs.org/ or https://github.com/systemjs/systemjs.
                        1. can be used only with AMD or SystemJS module systems
                        1. if use `outFile` option
                            1. should set the "module" option to either...
                                1. `amd` ...or... 
                                1. `system`
                    1. instead of using the "outFile" option
                        1. recommend using TypeScript with webpack to bundle your code:                
                            1. https://webpack.js.org/guides/typescript/
                        1. For REAL projects, use a framework, like     
                            1. Create React App
                                1. https://create-react-app.dev/
                            1. Next.js
                                1. https://nextjs.org/
                            
    1. `"moduleResolution": "node"`
        1. Defines how `tsc` resolves modules
            - how `tsc` looks at module import statements 
            - how `tsc` decides what should be imported given an import statement
                - like "import a from 'moduleA'"
        1. Value `node` tells `tsc` to mimic the Node.js module resolution strategy. 
        1. Options 
            1. `node`
            1. `classic`
                1. `classic` is deprecated 
                1. only for backwards compatibility.
    1. `"esModuleInterop": true`
        1. Allows us to `import default` from commonjs modules 
            1. modules that don’t have a default export 
                1. modules which didn’t export the "default" property
                    1. like React, as if they have it. 
        1. Example React used to be imported like this:
            - `import * as React from 'react'`
        1. With this option, import React like this...
            - `import React from 'react'`
    1. `"jsx": "preserve"`
        1. tells `tsc` how to transform the JSX code
        1. `"preserve"` option leaves JSX as is in compiled files
        1. `"react"` option turns JSX into the React API calls 
            - e.g., React.createElement('div')
        1. Usually `preserve` leaves the JSX transformation to a different tool in our build chain (e.g., Babel).
    1. `"skipLibCheck": true`
        1. Tells `tsc` whether to ...
            1. type check declaration(*.d.ts) files (yours and 3rd party packages) in your proj.
        1. Purpose
            1. to reduce compile time of a project
                1. by skipping the type checking of declarations
                    1. which were tested by their authors
                    1. which are known to work correctly
            1. `tsc` won’t go deep into checking the types of the third party packages
                1. still checks our code against type definitions provided by these packages.
    1. `"files": ["./file1.ts", "./file2.d.ts", …]`
        1. Lists files which `tsc` should always include in the compilation. 
        1. files included using this option are included regardless of the `"exclude"` option.
    1. `"include": ["src/**/*"]`
        1. Lists files we’d like to be compiled
            1. `"files"` option requires relative or absolute paths to the files
            1. `"include"` option allows glob-like patterns, like:
                - "**" - any subdirectory
                - "*" - any file name
                - "?" - a character followed by question mark becomes optional (e.g., "src/*.tsx?")
                - "exclude": ["node_modules", "**/*/*.test.ts"]
    1. `"exclude": ["node_modules", "**/*/*.test.ts"]`
        1. Excludes files from compilation
            1. accepts same patterns as "include" option
        1. can use this option to filter files specified using "include" option
        1. this option doesn’t affect the "files" option.
        1. files/folders to exclude
            1. node_modules, 
            1. test files, 
            1. compilation output directory
        1. if `exclude` is never used...
            1. `tsc` will exclude folder specified using "outDir" option.
    1. If `"files"` and `"include"` are NOT specified...
        1. `tsc` will compile all TS files ...
            1. from root directory and any subdirectory ...
            1. excluding files specified using `"exclude"` option.

                    
</details>

#### Section 2 TypeScript

<details>
<summary>Expand</summary>

##### Types

<details>
<summary>Expand</summary>
1. Types
    1. Boolean
        1. ![pict04](img/c01s01.1.jpg)
    1. Number
        1. ![pict08](img/c01s01.2.jpg)
    1. String
        1. Don't forget `'` and `"` are different
        1. ![pict06](img/c01s01.3.png) 
    1. Nulls
        1. ![pict08](img/c01s01.4.png)
            1. ![pict08](img/c01s01.5.png)
        1. Strict null checks
            1. Pro
                1. It will red-check and read into code
            1. Con
                1. It will prevent you from assigning null
                    1. ![pict08](img/c01s01.6.png)
                1. Only way around it... if check
                    1. ![pict08](img/c01s01.7.png) 
    1. Primitive Types
        1. ![](img/c01s01.8.png)
    1. Objects
        1. CANNOT ASSIGN a Primative Type... must be an object
            1. ![pict08](img/c01s01.9.png)
        1. CAN ASSIGN
            1. Array
                1. ![pict06](img/c01s01.10.png)
            1. Map
            1. Object
            1. Void
    1. Void
        1. describes an absence of a value
        1. used with functions
            1. ![](img/c01s01.11.png)
            1. ![](img/c01s01.12.jpg)
    1. Tuple
        1. like an array.... but you can mix types
            1. ![](img/c01s01.13.jpg)
    1. Enum
        1. Allows you define assignable variable
            1. ![](img/c01s01.14.jpg)
            1. ![](img/c01s01.15.jpg)
        1. Why use it?
            1. Forces users/programs to use a set of values
                1. Instead of using `number` for a question
                    1. use an `enum` of `1`, `2`, `3`, or `4`
    1. Walk through
        1. abbreviate your target
            1. ![](img/c01s01.16.png)
        1. type guard your response logic
            1. ![](img/c01s01.17.png)
        1. check your type logic
            1. ![](img/c01s01.18.png)
        1. assert as HTML input element
            1. ![](img/c01s01.19.png)
        1. NOTE!!!!!
            1. `<generics>` conflict with JSX in `.tsx` files
1. Types part 2
    1. Interfaces
        1. When to use...
            1. Creating a pizza with ONLY `class`
                1. ![](img/c01s02.11.jpg)
            1. When you describe a pizza ... but not use
                1. ![](img/c01s02.10.jpg)
        1. Why does this work?
            1. ![](img/c01s02.01.png)
                1. because both variables have the same shape
                1. when you add another variable
                    1. ![](img/c01s02.02.png)
                1. you get an error
                    1. ![](img/c01s02.03.png)
        1. Make a parameter optional
            1. ![](img/c01s02.04.png)
        1. Make a parameter immutable [can't be changed once accpeted]
            1. ![](img/c01s02.05.jpg)
            1. ![](img/c01s02.06.jpg)
    1. Index Signature
        1. establishing the index's type
            1. ![](img/c01s02.07.png)
    1. Call signature
        1. establishing the return's type
            1. ![](img/c01s02.08.png)
    1. Extending Interface
        1. Similar to extending a class
            1  Can combine... but NOT MODIFY if a child
            1. ![](img/c01s02.09.png)
    1. Interface vs. Types
        1. Interface format
            1. ![](img/c01s02.12.jpg)
        1. Type format
            1. ![](img/c01s02.13.jpg)
        1. Putting it with Class
            1. ![](img/c01s02.14.jpg)
        1. Working with function
            1. How it works with `type`
                1. ![](img/c01s02.15.jpg)
            1. How it works with `interface`
                1. ![](img/c01s02.16.jpg)
                1. ![](img/c01s02.17.jpg)
        1. ![](img/c01s02.18.jpg)
        1. ![](img/c01s02.19.jpg)

</details>

##### Functions

<details>
<summary>Expand</summary>

1. Functions
    1. Basic Format for typing
        1. ![](img/c01s03.01.png)
    1. Cool tricks
        1. specifying that all arguments should be numbers.
            1. ![](img/c01s03.02.png)
        1. Specifying first to be a sting, secong boolean, and rest as numbers
            1. ![](img/c01s03.03.png)
    1. Walk Through
        1. How do you calculate area with types
            1. ![](img/c01s03.04.png)
        1. Set a conditional for two
            1. ![](img/c01s03.05.jpg)

</details>

##### Classes


<details>
<summary>Expand</summary>

1. Classes
    1. Construct a class
        1. ![](img/c01s04.01.jpg)
    1. Extend a class
        1. ![](img/c01s04.02.png)
            1. `super` refers to the parent... `Robot`
            1. `this` refers to the instance... `FlyingRobot`
    1. Implement class
        1. ![](img/c01s04.03.png)
    1. Modifying class properties
        1. ![](img/c01s04.04.jpg)
            1. ![](img/c01s04.05.jpg)
        1. ![](img/c01s04.06.jpg)
            1. Cannot be accessed OUTSIDE of class
                1. ![](img/c01s04.07.jpg)
            1. Still accessible to subclasses
                1. ![](img/c01s04.08.png)
    1. `set` methods in class
        1. ![](img/c01s04.09.png)
            1. Ensure to call properties as `this.`
                1. ensures to reference the INSTANCE... not class as a whole
        1. Example
            1. ![](img/c01s04.10.jpg)
            1. ![](img/c01s04.11.png)
            1. ![](img/c01s04.12.png)
        1. `static` variables / methods CANNOT be inherited by `super`
            1. ![](img/c01s05.11.jpg)
        1. Why use `static` variables / methods at all?
            1. Allows you to invoke within subclass without invoking parent
                1. Creating a `PizzaMaker` class
                    1. ![](img/c01s04.13.jpg)
                1. Implementing static allows you to return a new class with
                    1. ![](img/c01s04.14.jpg)
                1. Without `static`
                    1. ![](img/c01s04.15.jpg)

</details>


##### Private Fields

<details>
<summary>Expand</summary>
                    
1. ECMAscript Private Fields
    1. Annotate private fields with `#`
        1. ![](img/c01s05.01.png)
    1. So what is new?
        1. Preventes Other class from taking the name
            1. ![](img/c01s05.02.png)
        1. ![](img/c01s05.03.png)
    1. `#` vs `private`
        1. ![](img/c01s05.04.png)
        1. ![](img/c01s05.05.jpg)

</details>


##### Interfaces

<details>
<summary>Expand</summary>

1. Interfaces
    1. Creating a Shopping Cart and SpendingCalculator
        1. Calculator exports a function `calculateTotalAmount`
            1. Takes in `IOrder` and returns cost after discount and tax
            1. ![](img/c01s05.07.jpg)
        1. When you want a class to assume an interface...
            1. ![](img/c01s05.06.png)
        1. Connecting the two
            1. ![](img/c01s05.08.jpg)
            1. `Shopping-Cart.ts` implements `IOrder`, but nevers sees how `caclulateTotalAmount` works
        1. Tying code via `interface` is a solid way of privatizing data
            1. ![](img/c01s05.09.jpg)
            1. ![](img/c01s05.10.jpg)
    1. What you can use with interfaces
        1. Cannot access both `instance` within `constructor` and `static`
            1. ![](img/c01s05.12.png)
        1. ![](img/c01s05.13.png)
        1. ![](img/c01s05.14.png)
        1. If you set an interface
            1. ![](img/c01s05.15.png)
        1. And you implement a class
            1. ![](img/c01s05.16.png)
        1. And you utilize class method
            1. ![](img/c01s05.17.png)
                1. TS only sees the interface `Animal` and NOT `dog` class
        1. if you force the recognition of `Animal`'s instance of `dog` via generics
            1. ![](img/c01s05.18.jpg)
        1. you can have BOTH `Animal` methods and `dog` methods by...
            1. using generics and extending the parent class
                1. ![](img/c01s05.19.jpg)
            1. recogizes the generic
                1. ![](img/c01s05.20.png)


</details>

##### Generics

<details>
<summary>Expand</summary>

1. Generics
    1. Generics
        1. Custom Types
            1. cannot use generic type parameters to describe ...
                1. static properties 
                1. static methods.
            1. ![](img/c01s07.21.png)
    1. Importing a Generic
        1. ![](img/c01s07.01.png)
    1. Importing multiple Generic to use within a `function`
        1. ![](img/c01s07.02.png)
        1. ![](img/c01s07.03.png)
    1. Importing generic INSIDE of a function
        1. ![](img/c01s07.04.png)
    1. Big benefit of using generic
        1. use on both `instance` side and `static`/`class` side
            1. ![](img/c01s07.05.jpg)
    1. Exercise -- Cakes
        1. `any` is a problem? How would you get `items` away from `any`?
            1. ![](img/c01s07.06.jpg)
                1. Import `<Item>` to the argument and specify `items` as an array of `<Item>` type 
                1. ![](img/c01s07.07.jpg)
        1. Another problem... `<Item>` does not describe a `.expiryDate`
            1. ![](img/c01s07.08.png)
                1. `extend` the parent class that owns that property
                1. ![](img/c01s07.09.jpg)
        1. Using generics for specific cakes
            1. Define interface that extends the `expiryDate`
                1. ![](img/c01s07.11.png)
                    1. `ChocolateCake` gets its own interface
                    1. `VanillaCake` gets its own interface
            1. Assign the interface to get only ChocolateCakes / VanillaCakes
                1. ![](img/c01s07.10.jpg) 
        1. Creating a function that retrieves an array of expired cakes
            1. ![](img/c01s07.12.jpg)
                1. Functions does NOT import generic
                1. Import generic as an extended version
                    1. ![](img/c01s07.13.png)
                1. Specify that the function ...
                    1. takes in an array of said generic
                        1. ![](img/c01s07.14.png)
                    1. outputs an array of said generic
                        1. ![](img/c01s07.15.png)
    1. Exercise -- Shopping cart
        1. Create an interface for `ShoppingCart` that holds an array of `items` that allows you to add and get by id
            1. ![](img/c01s07.16.png)
                1. Two key properties
                    1. `price` and `id` of item
                1. Need to pass two important types
                    1. `<ItemId>` and `<Item>`
        1. Create `cart` that instiates an instance of `ShoppingCart` and specifies the methods
            1. ![](img/c01s07.17.png)
        1. Problem
            1. `items` is registered as `any`
            1. SOLUTION - specify `this` for `ShoppingCart`'s methods
                1. ![](img/c01s07.18.jpg)
            1. Check
                1. ![](img/c01s07.19.png)
        1. Problem
            1. ...but `getItemById` has an error
                1. ![](img/c01s07.20.jpg)
                1. `undefined` is NOT accepted within our defintion of `Item`
                    1. `.find` has the potential of returning `undefined`... `undefined` must be a returnable type if you use `.find`
            1. SOLUTION
                1. Instead of backtracting to the original `Item` type...
                1. Merely attach `undefined` to the method's return type

</details>

##### Union

<details>
<summary>Expand</summary>

1. Union
    1. Problem -- string methods with numbers
        1. Using a union when you call a string method.
            1. ![](img/c01s08.01.jpg)
        1. Solution - type guard `if`
            1. ![](img/c01s08.02.png)
        1. What if you want to use boolean?
            1. ![](img/c01s08.03.png)
    1. Applying Unions with Interfaces
        1. Scenario... Dog and Cat interfaces
            1. ![](img/c01s08.04.jpg)
        1. Problem - union - interfaces have different methods
            1. Trying to make a `Cat` `.bark()`
                1. ![](img/c01s08.05.jpg)
            1. Solution
                1. ASSERT that it is dog PRIOR TO `.bark()`
                    1. ![](img/c01s08.06.jpg)            
                1. Assertion is UGLY...
    1. Another Solution -- Custom Type Guard
        1. set a function for dog-checking
            1. ![](img/c01s08.07.jpg)
                1. `: SomeObj is Dog { /* something dog does !== undefined */}`
                    1. sets `SomeObj` to dog **ONLY IF** `{}` returns true
        1. insert type guard
            1. before
                1. ![](img/c01s08.08.jpg)
            1. after
                1. ![](img/c01s08.09.jpg)
    1. Overall
        1. no way to properly type check
            1. without checking the type methods

</details>

##### Intersection

<details>
<summary>Expand</summary>

1. Intersection Type
    1. Purpose
        1. Combines `&` types/interfaces into a singular type
    1. Example
        1. ![](img/c01s09.01.png) 
        1. ![](img/c01s09.02.jpg)
        1. PROBLEM
            1. Overview
                1. ![](img/c01s09.03.jpg)
                1. `resultObj` is an `any` type
            1. SOLUTION Part 1
                1. declaring inputs as types `ObjA` and `ObjB`
                    1. ![](img/c01s09.04.jpg)
                1. check if TS acknowledged
                    1. ![](img/c01s09.05.jpg)
            1. SOLUTION Part 2
                1. explicitly declaring type returned
                1. ![](img/c01s09.06.jpg)
        1. PROBLEM #2
            1. OVERVIEW
                1. ![](img/c01s09.07.jpg)
                1. `objA` and `objB` can be different types
            1. SOLUTION part 1
                1. Force them as objects via `extends object`
                    1. ![](img/c01s09.08.jpg)
                1. Check
                    1. ![](img/c01s09.09.jpg)
                1. Using object
                    1. ![](img/c01s09.10.jpg)
</details>

##### Type Alias

<details>
<summary>Expand</summary>


1. Type Alias
    1. Definition
        1. Create a name for a custom type
            1. Union
            1. Intersection
            1. Generic
            1. Interface
    1. When to use
        1. ![](img/c01s10.01.jpg)
    1. Quiz
        1. ![](img/c01s10.02.png)

</details>

##### External Packages / Types

<details>
<summary>Expand</summary>

1. Using External Packages and Their Types
    1. Utilize hover to check dependency
        1. ![](img/c01s11.01.jpg)
    1. To go to dependency
        1. ![](img/c01s11.02.jpg)
        1. ![](img/c01s11.03.jpg)
    1. Checking for dependencies
        1. Hovering over `'library'`
            1. ![](img/c01s11.04.jpg)

</details>

##### Declaration Merging

<details>
<summary>Expand</summary>

1. Declaration Merging
    1. You can merge
        1. Declaration
        1. Interfaces
        1. Namespaces
    1. ... but ... you can't merge
        1. Modules...
            1. just modify them
    1. Merging interfaces
        1. three different interfaces
            1. ![](img/c01s12.01.jpg)
            1. implementing a cart that allows for BOTH versions of `calculateTotal()`
                1. ![](img/c01s12.02.jpg)
    1. Merging namespaces
        1. merging two namespaces
            1. ![](img/c01s12.03.jpg)
            1. defining an interface that draws from BOTH versions of `MyNamespace`
                1. ![](img/c01s12.04.jpg)
            1. NOTE... cannot export NON-EXPORTED namespace properties/methods
                1. ![](img/c01s12.09.jpg)
                    1. ![](img/c01s12.10.jpg)
        1. Merging namespaces [similar to extending... except for everything]
            1. Allows you to add functions to types
                1. Merging functions into a namespace
                    1. ![](img/c01s12.05.jpg)
                        1. `someFunction` now has `someProperty` attached
                        1. ![](img/c01s12.06.jpg)
                1. Merging `enum` into a namespace
                    1. ![](img/c01s12.07.jpg)
                        1. utilized `namespace` to add a function to an `enum`
                    1. ![](img/c01s12.08.jpg)
    1. Augmenting a module
        1. Focusing on `React` module
            1. Checking inside module first
                1. Inside original `react` module... `Component` interface
                    1. ![](img/c01s12.11.jpg)
            1. Import... declare... type... define... use
                1. ![](img/c01s12.12.jpg)
        
        

</details>

##### Utility Types

<details>
<summary>Expand</summary>

1. Utility Types
    1. Definition
        1. Exportable modifier that can be reused to ...
            1. modify types
        1. ![](img/c01s13.01.jpg)
    1. `Partial<T>`
        1. Definition
            1. converts required properties of interface into optional
        1. Overview
            1. ![](img/c01s13.02.jpg)
        1. Example
            1. ![](img/c01s13.03.jpg)
            1. Fixed
                1. ![](img/c01s13.04.jpg)
    1. `Required<t>`
        1. Definition
            1. converted properties into REQUIRED status
        1. Overview
            1. ![](img/c01s13.05.jpg)
    1. `Readonly<t>`
        1. Definition
            1. converted properties into READONLY status
        1. Overview
            1. ![](img/c01s13.06.jpg)
    1. `Record<K,T>`
        1. Definition
            1. converts into map-like object
        1. Overview
            1. ![](img/c01s13.07.jpg)
        1. Example
            1. ![](img/c01s13.08.jpg)
    1. `Pick<T,K>`
        1. Definition
            1. Allows you to isolate certain required properties of Type
        1. Overview
            1. ![](img/c01s13.09.jpg)
        1. Example
            1. ![](img/c01s13.11.jpg)
    1. `Omit<T,K>`
        1. Definition
            1. Opposite of `Pick`, `K` are the properties to omit
        1. Overview
            1. ![](img/c01s13.10.jpg)
        1. Example
            1. ![](img/c01s13.12.jpg)
    1. `Exclude<T,U>`
        1. Definition
            1. Instead of properties, allows us to rid of a type
        1. Overview
            1. ![](img/c01s13.13.jpg)
        1. Example
            1. ![](img/c01s13.14.jpg)
            1. ![](img/c01s13.15.jpg)
    1. `Extract<T,U>`
        1. Definition
            1. Instead of properties, allows us to select specified type options
        1. Example
            1. ![](img/c01s13.16.jpg)
    1. `NonNullable<T>`
        1. Definition
            1. Allows us to select type options... getting rid of `null`/`undefined` as a type option
                1. why?
                    1. because some properties are optional
                    1. `?` optional permits `undefined` as a type option 
                        1.
                1. NOTE
                    1. only works if `strictNullChecks` is enabled in `tsconfig.json`
                        1. ![](img/c01s13.18.jpg)
        1. Overview
            1. ![](img/c01s13.17.jpg)
        1. Example
            1. ![](img/c01s13.19.jpg)
                1. BOTH `null` and `undefined` are NOT PERMITTED
    1. `ReturnType<T>`
        1. Definition
            1. Returns the type specified within `<>`
        1. Overview
            1. ![](img/c01s13.20.jpg)
        1. Example
            1. ![](img/c01s13.21.jpg)
    1. `InstanceType<T>`
        1. Definition
            1. Used on instance, returns name of class used to generate instance
        1. Overview
            1. ![](img/c01s13.22.jpg)
        1. Example
            1. ![](img/c01s13.23.jpg)
            1. ![](img/c01s13.24.jpg)
                1. we create a Profile CLass that calls delete
                    1. how do we ensure what instance it is... `car` or `user`
            1. ![](img/c01s13.25.jpg)
                1. ![](img/c01s13.26.jpg)
    1. `ThisType<T>`
        1. Deintioin
        1. Overview
            1. ![](img/c01s13.27.jpg)
                1. MUST ENABLE `noImplicitThis`
                1. ![](img/c01s13.33.jpg)
        1. Example
            1. ![](img/c01s13.28.jpg)
            1. ![](img/c01s13.29.jpg)
            1. ![](img/c01s13.30.jpg)
            1. ![](img/c01s13.31.jpg)
        1. Putting it all together
            1. ![](img/c01s13.32.jpg)
                1. ![](img/c01s13.32.1.jpg)

</details>

##### Mapped Types

<details>
<summary>Expand</summary>

1. Mapped Types
    1. 
        1. 

</details>

##### Conditional Types

<details>
<summary>Expand</summary>

1. Conditional Types
    1. 
        1. 

</details>

##### TS Tips

<details>
<summary>Expand</summary>

1. 25. TypeScript Tips
    1. 
        1. 

</details>

<hr />

</details>




<details>
<summary>Expand</summary>

#### Section 2 TypeScript With React

##### Intro

<details>
<summary>Expand</summary>

1. TypeScript with React - section Intro
    1. 
        1. 

</details>

##### What is webpack

<details>
<summary>Expand</summary>

1. What is webpack?
    1. Overall
        1. Consolidates a development for a more efficient production 
            1. ![](img/c02s02.01.jpg)
    1. configured with...
        1. `webpack.config.js`
    1. How do you fix `.ts` files prior to consolidation
        1. loaders
            1. ![](img/c02s02.02.jpg)

</details>

##### Setting up a webpack project

<details>
<summary>Expand</summary>

1. Setting up a webpack project
    1. Install webpack with its cli
        1. ![](img/c02s03.01.jpg)
    1. Add scripts to the `package.json`
        1. start with a `build` for production mode
            1. ![](img/c02s03.02.jpg)
        1. Test
            1. Run build
                1. ![](img/c02s03.03.jpg)
            1. check for build files in `dist`
                1.  ![](img/c02s03.04.jpg)
    1. incorporate html capabilities
        1. Add plugin for webpack's html
            1. ![](img/c02s03.05.jpg)
        1. configure webpack with `webpack.config.js`
            1. ![](img/c02s03.06.jpg)
                1. ![](img/c02s03.06.1.jpg)
            1. ![](img/c02s03.07.jpg)
                1. ![](img/c02s03.07.1.jpg)
                1. ![](img/c02s03.07.2.jpg)
        1. incorporate into npm scripts
            1. ![](img/c02s03.09.jpg)
            1. ![](img/c02s03.08.jpg)
 
</details>

##### TypeScript setup options

<details>
<summary>Expand</summary>

1. TypeScript setup options
    1. two ways to compile `.ts` into `.js`
        1. `ts-loader`
            1. overivew
                1. ![](img/c02s04.01.jpg)
            1. two key features
                1. type checks
                1. compiles `.ts` into `.js`
            1. can be combined with babel
                1. ![](img/c02s04.02.jpg)
        1. `babel-loader` with babel presets
            1. overview
                1. ![](img/c02s04.03.jpg)
            1. Pro's Cons
                1. Pro
                    1. efficient for emovint TS syntax
                1. Con
                    1. NO type-checking
            

</details>

##### Setting up TypeScript using ts-loader

<details>
<summary>Expand</summary>

1. Setting up TypeScript using ts-loader
    1. Install
        1. ![](img/c02s05.01.jpg)
    1. `tsconfig.json`
        1. `compilerOptions`
            1. ![](img/c02s05.02.jpg)
            1. uses `target` to set desired format
            1. use `module` to import `es6`
                1. ![](img/c02s05.05.jpg)
            1. `strict` enables more options
                1. ![](img/c02s05.06.jpg)
    1. configuring `webpack.config.js` to find `index.ts`
        1. ![](img/c02s05.03.jpg)
        1. ![](img/c02s05.07.jpg)
    1. Apply `ts-loader` locally for type-checking
        1. select `.ts` file and specify TS version
            1. ![](img/c02s05.04.jpg)
        1. Pick WorkSpace Version to generate local files
            1. ![](img/c02s05.08.jpg)
        1. Verify local files [`.vscode` folder]
            1. ![](img/c02s05.09.jpg)
         

</details>

##### Setting up TypeScript using babel-loader

<details>
<summary>Expand</summary>

1. Setting up TypeScript using babel-loader
    1. Install needed items
        1. ![](img/c02s06.01.jpg)
            1. `preset-env` brings JS syntax
            1. `preset-typescript` brings TS syntax
    1. Configure Babel with `.babelrc` with presets
        1. ![](img/c02s06.02.jpg)
    1. Install `babel-loader`
        1. ![](img/c02s06.03.jpg)
    1. Setup `webpack.config.js` with `babel-loader`
        1. ![](img/c02s06.04.jpg)
    1. Class syntax
        1. Before class typing... all classes looked like this
            1. ![](img/c02s06.05.jpg)
        1. If you use new JSX class proposal...
            1. ![](img/c02s06.07.jpg)
        1. install `plugin-propasal-class-properties`
            1. ![](img/c02s06.06.jpg)
    1. Working with typescript
        1. Will compile without errors, but still Type-Checks
            1. ![](img/c02s06.08..jpg)
        1. Configuring TypeScript to work with Babel
            1. `tsconfig.json`
                1. ![](img/c02s06.09.jpg)
            1. configure `package.json`
                1. ![](img/c02s06.11.jpg)
            1. isolated Modules
                1. ![](img/c02s06.10.jpg)
                    1. ![](img/c02s06.12.jpg)
            1. LibCheck
                1. Imported libraries can NOT be checked with `isolatedModules` enabled
                    1. ![](img/c02s06.13.jpg)
                1. Need to enable `skipLibCheck`
                    1. ![](img/c02s06.14.jpg)
        1. Overall setup
            1. Overview
                1. run `tsconfig.json`
                1. run `webpack`
                    1. webpack automatically starts babel with `babel-loader`
            1. What it looks like in `package.json`
                1. ![](img/c02s06.15.jpg)
            
</details>

##### Adding React

<details>
<summary>Expand</summary>

1. Adding React
    1. Install React development
        1. ![](img/c02s06.16.jpg)
    1. Install React type defintions
        1. ![](img/c02s06.17.jpg)
    1. Warning- JSX is NOT recognized
        1. Without proper JSX configuration...
            1. ![](img/c02s07.1.jpg)
        1. Configuring `tsconfig.json` for JSX
            1. Two Options
                1. ![](img/c02s07.2.jpg)
            1. `preserve`
                1. compiles `<div></div>`
                    1. into `<div></div>`
            1. `react`
                1. compiles `<div></div>`
                    1. into `React.createElement('div')`
    1. What it should look like
        1. `index.tsx`
            1. ![](img/c02s07.3.jpg)
        1. `index.html`
            1. ![](img/c02s07.4.jpg)
    1. Configure Babel to handle React
        1. install Babel's react preset
            1. ![](img/c02s07.5.jpg)
        1. configure `.babelrc`
            1. ![](img/c02s07.6.jpg)
                1. NOTE!!!! 
                    1. Babel presets are LOADED in REVERSE
                    1. This case
                        1. Typescript is loaded prior to react
</details>          

##### Adding a source map

<details>
<summary>Expand</summary>

1. Adding a source map
    1. Basic 
        1. a way to pack 
    1. Development 
        1. 4 ways of source map in webpack
            1. ![](img/c02s08.1.jpg)
            1. ![](img/c02s08.2.jpg)
        1. Implement choice
            1. ![](img/c02s08.3.jpg)
    1. Production
        1. 4 ways of source map 
            1. ![](img/c02s08.4.jpg)
        1. `source-map` generated reference comment
            1. ![](img/c02s08.5.jpg)
                1. NOTE - source-map will have source code
        1. `hidden-source-map`
            1. ![](img/c02s08.6.jpg)
                1. never put source-map on webserver
        1. if you are not using `babel-loader` and using `ts-loader`
            1. ![](img/c02s08.7.jpg)
1. Reference

<details>
<summary>Expand</summary>
1. ![](img/c02s08.ref.jpg)

</details>

</details>

##### Function components

<details>
<summary>Expand</summary>

1. Function components
    1. Separating code into components
        1. Inspecting `.render` 
            1. ![](img/c02s09.04.jpg)
            1. ![](img/c02s09.05.jpg)
                1. if you change type within `.render`
                    1. ![](img/c02s09.06.jpg)
                    1. ![](img/c02s09.07.jpg)
        1. Importing `.json`
            1. ![](img/c02s09.01.jpg)
        1. Fix error by configuring `tsconfig`
            1. ![](img/c02s09.03.png)
            1. ![](img/c02s09.02.jpg)
    1.  Creating a functional component
        1. Purpose
            1. receives a generic type parameter
                1. ![](img/c02s09.08.jpg)
        1. Interface
            1. stovepipes for type-checking variables within component
                1. ![](img/c02s09.09.jpg)
        1. Rendering list items
            1. ![](img/c02s09.10.jpg)
        1. Implement in `App.tsx`
            1. ![](img/c02s09.11.jpg)
    1. Outcome
        1. ![](img/c02s09.12.jpg)


</details>

##### Setting up CSS Modules

<details>
<summary>Expand</summary>

1. Setting up CSS Modules
    1. Main CSS 
        1. ![](img/c02s10.01.jpg)
    1. Import into `index.ts`
        1. ![](img/c02s10.02.jpg)
    1. Configure `webpack.config.js` to handle `.css` files
        1. ![](img/c02s10.03.jpg)
        1. **NOTE!!** Must install `css-loader` for webpack
            1. ![](img/c02s10.04.jpg)
        
</details>

##### Importing SVGs - adding a logo

<details>
<summary>Expand</summary>

1. Importing SVGs - adding a logo

</details>

##### Class components

<details>
<summary>Expand</summary>

1. Class components

</details>

##### Handling events using React

<details>
<summary>Expand</summary>

1. Handling events using React

</details>

##### Using React context and the useState

<details>
<summary>Expand</summary>

1. Using React context and the useState

</details>

##### Replacing setState with useReducer

<details>
<summary>Expand</summary>

1. Replacing setState with useReducer

</details>

##### Store cart data in local storage (useEffect hook)

<details>
<summary>Expand</summary>

1. Store cart data in local storage (useEffect hook)

</details>

##### Creating a Higher Order Component - part 1

<details>
<summary>Expand</summary>

1. Creating a Higher Order Component - part 1

</details>

##### Creating a Higher Order Component - part 2

<details>
<summary>Expand</summary>

1. Creating a Higher Order Component - part 2

</details>

##### Creating a Render Props component

<details>
<summary>Expand</summary>

1. Creating a Render Props component

</details>

##### Creating a custom hook

<details>
<summary>Expand</summary>

1. Creating a custom hook

</details>

##### Handling original DOM events

<details>
<summary>Expand</summary>

1. Handling original DOM events

</details>

</details>

<hr />






#### Section 3 LEGACY React With TypeScript

<details>
<summary>Expand</summary>

##### Introduction

<details>
<summary>Expand</summary>

1. Introduction

</details>

##### Function Components

<details>
<summary>Expand</summary>

1. Function Components

</details>

##### Class Components

<details>
<summary>Expand</summary>

1. Class Components

</details>

##### Higher Order Components - React Redux

<details>
<summary>Expand</summary>

1. Higher Order Components - React Redux

</details>

##### Higher Order Components - Creating HOCs

<details>
<summary>Expand</summary>

1. Higher Order Components - Creating HOCs

</details>

##### Render Props

<details>
<summary>Expand</summary>

1. Render Props

</details>

##### Event Handling

<details>
<summary>Expand</summary>

1. Event Handling

</details>

##### Introduction to Hooks

<details>
<summary>Expand</summary>

1. Introduction to Hooks

</details>

##### Hooks: useState

<details>
<summary>Expand</summary>

1. Hooks: useState

</details>

##### Hooks: useEffect

<details>
<summary>Expand</summary>

1. Hooks: useEffect

</details>

</details>

<hr />




#### Section 3 Building a React Redux App

<details>
<summary>Expand</summary>

##### Building a React Redux App Section Introduction

<details>
<summary>Expand</summary>

1. Building a React Redux App Section Introduction

</details>

##### What if you haven't used Redux before?

<details>
<summary>Expand</summary>

1. What if you haven't used Redux before?

</details>

##### Initial Setup

<details>
<summary>Expand</summary>

1. Initial Setup

</details>

##### Setting Up a Fake Server

<details>
<summary>Expand</summary>

1. Setting Up a Fake Server

</details>

##### Setting Up Redux

<details>
<summary>Expand</summary>

1. Setting Up Redux

</details>

##### Creating the Recorder Component

<details>
<summary>Expand</summary>

1. Creating the Recorder Component

</details>

##### Creating the Event List Component

<details>
<summary>Expand</summary>

1. Creating the Event List Component

</details>

##### Loading Events - Part 1

<details>
<summary>Expand</summary>

1. Loading Events - Part 1

</details>

##### Loading Events - Part 2 (using connect)

<details>
<summary>Expand</summary>

1. Loading Events - Part 2 (using connect)

</details>

##### Creating Events

<details>
<summary>Expand</summary>

1. Creating Events

</details>

##### Deleting Events

<details>
<summary>Expand</summary>

1. Deleting Events

</details>

##### Editing Titles

<details>
<summary>Expand</summary>

1. Editing Titles

</details>

##### Useful Links

<details>
<summary>Expand</summary>

1. Useful Links

</details>

</details>

<hr />


#### Section 6 Building the Tasks App

<details>
<summary>Expand</summary>


</details>

##### Introduction

<details>
<summary>Expand</summary>

1. Introduction

</details>

##### Resources to help you get started with Next.js (Optional)

<details>
<summary>Expand</summary>

1. Resources to help you get started with Next.js (Optional)

</details>

##### Section tips (Optional)

<details>
<summary>Expand</summary>

1. Section tips (Optional)

</details>

##### Setting up a Next.js project with TypeScript

<details>
<summary>Expand</summary>

1. Setting up a Next.js project with TypeScript

</details>

##### Explaining the tsconfig options

<details>
<summary>Expand</summary>

1. Explaining the tsconfig options

</details>

##### Explaining the esModuleInterop option

<details>
<summary>Expand</summary>

1. Explaining the esModuleInterop option

</details>

##### Setting up the GraphQL API route

<details>
<summary>Expand</summary>

1. Setting up the GraphQL API route

</details>

##### Setting up a local MySQL server

<details>
<summary>Expand</summary>

1. Setting up a local MySQL server

</details>

##### Creating resolvers for the "tasks" query and "createTask" mutation

<details>
<summary>Expand</summary>

1. Creating resolvers for the "tasks" query and "createTask" mutation

</details>

##### Adding GraphQL code generator for the back end

<details>
<summary>Expand</summary>

1. Adding GraphQL code generator for the back end

</details>

##### Finishing the GraphQL API

<details>
<summary>Expand</summary>

1. Finishing the GraphQL API

</details>

##### Setting up Apollo Client

<details>
<summary>Expand</summary>

1. Setting up Apollo Client

</details>

##### Generating types for the front end

<details>
<summary>Expand</summary>

1. Generating types for the front end

</details>

##### Adding styles

<details>
<summary>Expand</summary>

1. Adding styles

</details>

##### Adding the form for creating tasks

<details>
<summary>Expand</summary>

1. Adding the form for creating tasks

</details>

##### Running the "createTask" mutation

<details>
<summary>Expand</summary>

1. Running the "createTask" mutation

</details>

##### Adding the form for updating tasks

<details>
<summary>Expand</summary>

1. Adding the form for updating tasks

</details>

##### Running the "updateTask" mutation

<details>
<summary>Expand</summary>

1. Running the "updateTask" mutation

</details>

##### Deleting tasks

<details>
<summary>Expand</summary>

1. Deleting tasks

</details>

##### Filtering tasks by task status

<details>
<summary>Expand</summary>

1. Filtering tasks by task status

</details>

##### Marking the tasks as completed

<details>
<summary>Expand</summary>

1. Marking the tasks as completed

</details>

##### Using a single page to render the tasks

<details>
<summary>Expand</summary>

1. Using a single page to render the tasks

</details>

##### Using a single page to render the tasks list

<details>
<summary>Expand</summary>

1. Using a single page to render the tasks list

</details>

</details>

<hr />

#### Section 7 LEGACY Building the Tasks App

<details>
<summary>Expand</summary>

1. Building the Tasks App Section Introduction
1. Important - please read this before watching the videos
1. Setting Up Next.js with TypeScript
1. Explaining the "tsconfig" Options
1. Explaining the "esmoduleinterop" Option
1. Adding Apollo
1. Running the local GraphQL API server
1. Running the first GraphQL query
1. Generate types for queries and mutations using GraphQL Code Generator
1. Generate types for queries and mutations using GraphQL Code Generator
1. Adding styles
1. Moving the task list into its own component
1. Adding the "create task" form - part 1 - creating the mutation
1. Adding the "create task" form - part 2 - creating the form
1. Adding the "create task" form - part 3 - running the mutation
1. Adding the "update task" form - part 1 - preparing the queries
1. Adding the "update task" form - part 2 - creating the "update" page
1. Adding the "update task" form - part 3 - creating the "update" form
1. Adding the "update task" form - part 4 - running the "updateTask" mutation
1. Coding the "delete task" feature
1. Coding the "change task status" feature (the checkboxes)
1. Coding the task filter
1. Fix the tasks query's cache policy causing an extra request on initial page load
1. Using React Context to pass task status to child components of main page

</details>

<hr />

#### Section 8 Cheatsheets

<details>
<summary>Expand</summary>

1. Using TypeScript with React
1. Using TypeScript with Next.js

</details>

<hr />