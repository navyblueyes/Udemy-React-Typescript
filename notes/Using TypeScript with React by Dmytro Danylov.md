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

#### Section 1 TypeScript

<details>
<summary>Expand</summary>

1. Common TypeScript compiler and tsconfig options

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
            

The idea behind this option is to reduce the compile time of a project, by skipping the type checking of the declarations which are already tested by their authors and are known to work correctly.

Also, it might happen that you use a few packages whose type definitions are incompatible. Or, you’re importing a package that was built using a tsconfig which is less strict than yours (e.g., your config has the "strict" option enabled, while the other config has it disabled). In these cases the compiler will produce errors while type checking those packages.

With this option enabled the compiler won’t go deep into checking the types of the third party packages, but it will still check our code against the type definitions provided by these packages.

I have published a detailed article about this option on my website: TypeScript: the skipLibCheck Option Explained. Please have a look at it for a more detailed explanation and a few examples.


"files": ["./file1.ts", "./file2.d.ts", …]

We use this option to list the files which the compiler should always include in the compilation. The files included using this option are included regardless of the "exclude" option.


"include": ["src/**/*"]

We use this option to list the files we’d like to be compiled. While the "files" option requires relative or absolute paths to the files, the "include" option allows glob-like patterns, like:

"**" - any subdirectory

"*" - any file name

"?" - a character followed by the question mark becomes optional (e.g., "src/*.tsx?")


"exclude": ["node_modules", "**/*/*.test.ts"]

This option excludes the files from the compilation. It accepts the same patterns as the "include" option. You can use this option to filter the files specified using the "include" option. The "exclude" option doesn’t affect the "files" option.

Usually, you’d like to exclude node_modules, test files, and the compilation output directory.

If you omit this option, the compiler will exclude the folder specified using the "outDir" option.

If you won’t specify both options, "files" and "include", the compiler will compile all the TS files from the root directory and any subdirectory excluding the files specified using the "exclude" option.

                    
</details>

1. TypeScript Section Introduction
1. A note regarding the next lecture for
students who use Windows
1. Setting Up a Simple TypeScript Project
1. Modules
1. Quiz 1: Modules - Quiz
1. Types
1. Quiz 2: Types - Quiz
1. Interfaces
1. Functions
1. Classes
1. Quiz 3: Classes - Quiz
1. ECMAScript Private Fields
1. Implementing Interfaces
1. Describing Classes Using Interfaces
1. Generics
1. Quiz 4: Generics - Quiz
1. Union Type
1. Quiz 5: Union Type - Quiz
1. Intersection Type
1. Quiz 6: Intersection Type - Quiz
1. Type Alias
1. Quiz 7: Type Alias - Quiz
1. Using External Packages and Their Types
1. Declaration Merging
1. Utility Types
1. Mapped Types
1. Conditional Types
1. 25. TypeScript Tips

</details>

<hr />




#### Section 2 TypeScript With React

<details>
<summary>Expand</summary>

1. Section info
1. TypeScript with React - section Intro
1. What is webpack?
1. Setting up a webpack project
1. TypeScript setup options
1. Setting up TypeScript using ts-loader
1. Setting up TypeScript using babel-loader
1. Adding React
1. Adding a source map
1. Function components
1. Setting up CSS Modules
1. Importing SVGs - adding a logo
1. Class components
1. Handling events using React
1. Using React context and the useState
1. Replacing setState with useReducer
1. Store cart data in local storage (useEffect hook)
1. Creating a Higher Order Component - part 1
1. Creating a Higher Order Component - part 2
1. Creating a Render Props component
1. Creating a custom hook
1. Handling original DOM events

</details>

<hr />


#### Section 3 LEGACY React With TypeScript

<details>
<summary>Expand</summary>

1. Introduction
1. Function Components
1. Quiz 8: Function Components - Quiz
1. Class Components
1. Quiz 9: Class Components - Quiz
1. Higher Order Components - React Redux
1. Higher Order Components - Creating HOCs
1. Render Props
1. Event Handling
1. Introduction to Hooks
1. Hooks: useState
1. Hooks: useEffect

</details>

<hr />

#### Section 3 Building a React Redux App

<details>
<summary>Expand</summary>

1. Building a React Redux App Section Introduction
1. What if you haven't used Redux before?
1. Initial Setup
1. Setting Up a Fake Server
1. Setting Up Redux
1. Creating the Recorder Component
1. Creating the Event List Component
1. Loading Events - Part 1
1. Loading Events - Part 2 (using connect)
1. Creating Events
1. Deleting Events
1. Editing Titles
1. Useful Links

</details>

<hr />


#### Section 6 Building the Tasks App

<details>
<summary>Expand</summary>

1. Introduction
1. Resources to help you get started with Next.js (Optional)
1. Section tips (Optional)
1. Setting up a Next.js project with TypeScript
1. Explaining the tsconfig options
1. Explaining the esModuleInterop option
1. Setting up the GraphQL API route
1. Setting up a local MySQL server
1. Creating resolvers for the "tasks" query and "createTask" mutation
1. Adding GraphQL code generator for the back end
1. Finishing the GraphQL API
1. Setting up Apollo Client
1. Generating types for the front end
1. Adding styles
1. Adding the form for creating tasks
1. Running the "createTask" mutation
1. Adding the form for updating tasks
1. Running the "updateTask" mutation
1. Deleting tasks
1. Filtering tasks by task status
1. Marking the tasks as completed
1. Using a single page to render the tasks
1. Using a single page to render the tasks list

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