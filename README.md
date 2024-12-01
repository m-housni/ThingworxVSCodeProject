**🖐 NOTE: This is still a work in progress.**

# Intro

A project template that allows the development of Thingworx models in a TypeScript IDE like Visual Studio Code.

# Who is it for

This template is primarily aimed at Thingworx developer who are already very familiar with the composer but are also used to working in a regular IDE. To be able to succesfully use this, you should at least have an understanding of how the modelling environment works in Thingworx and some basic knowledge of TypeScript.

# Why use it

There are many advantages to this, and here are some of them:

 * **Git friendly**: In this project, thingworx entities are really just typescript source files (with some restrictions) so they can be easily used with git clients.
 * **Collaboration**: Collaboration in thingworx can be difficult. If developers use the same instance they can overwrite each other's changes by mistake; if they use different instances it can be difficult to merge the work. Using a classic file-based project instead makes it easier for multiple developers to work on the same project against the same git repository.
 * **Version management**: The entities are deployed as an extension instead of a regular entity export. Because of this, the entities themselves "gain" the version of the extension they are part of. Additionally, upgrading the extension will cause Thingworx to automatically remove old entities, whereas with a regular export, leftover entities must be found and removed manually.
 * **Developer friendly**: Creating entities in thingworx isn't very comfortable for developers. Common features found in IDEs, such as finding references, project-wide searching for code and even having multiple services open at the same time are not really possible. Since this project is just a typescript project, you can make use of all of your IDE's features.
 * **Typescript features**: You can also make use of typescript features that don't exist in thingworx, such as creating and using interfaces, marking methods and properties as private and using newer javascript features that can be transpiled. Note that some of these are erased on the Thingworx side, but they can nevertheless be useful while developing.
 * **Separate development and runtime**: In thingworx, any change happens immediately and can affect the system. When using this project template, you have to first build & publish your updated code for it to take effect.
 * **Debug support**: By using the `ThingworxVSCodeDebugger` visual studio code extension and the `BMDebugServer` thingworx extension, developers can also debug their thingworx typescript projects directly from visual studio code. The debugger supports most common features including setting breakpoints, exception breakpoints, stepping, evaluating expressions and changing variable values. For more information about setting up debugging, see [Usage Guide](https://github.com/BogdanMihaiciuc/ThingworxVSCodeDebugger/wiki/Usage-Guide).
 * **Profiling support**: By using the `BMProfiler` thingworx extension, developers can also profile their thingworx typescript projects to quickly identify performance bottlenecks. The profiler is a simple tracing profiler that shows the amount of time spent executing each service or function within a service. The profiler generates reports that can be viewed in the chrome performance inspector. For more information about setting up profiling, see [BMProfiler](https://github.com/BogdanMihaiciuc/BMProfiler).
 * **Additional features**: The transformer also supports some additional features not directly available in Thingworx that make development easier such as providing useful constants like `METHOD_NAME`, giving developers the ability to declare and use global functions, writing inline SQL queries on Database things and extending data shapes.

# Development

## Pre-Requisites

The following software is required:

* [NodeJS](https://nodejs.org/en/): needs to be installed and added to the `PATH`. You should use the LTS version (v14+).

The following software is recommended:

* [Visual Studio Code](https://code.visualstudio.com/): An integrated developer enviroment with great javascript and typescript support. You can also use any IDE of your liking, it just that most of the testing was done using VSCode.

## Development Environment

In order to develop with this project you need to do the following:
1. Clone this repository
2. Copy `.env.sample` to `.env` and configure the `THINGWORX_SERVER` and either `THINGWORX_USER` and `THINGWORX_PASSWORD` or `THINGWORX_APP_KEY` and other fields as needed.
3. Open `package.json` and `twconfig.json` and change the fields as needed.
4. Run `npm install`. This will install the development dependencies for the project.
5. Run `npm run watch:declarations`. This will start a background process that is needed for generating certain files.
6. Start working on the project.

## Entity Dependencies

By default, the project contains the declarations for all the out of the box entities available in a fresh installation of Thingworx, however there may be the case that you need to use some entities that had been previously created on the Thingworx instance using the composer. To handle this, it is possible to define entity dependencies in `twconfig.json`. There are two types of dependencies that can be specified there:
 - `projectDependencies`: An array where you specify the Thingworx project names on which your local projects depends. This will download the declaration for all of the entities that are included in the project.
 - `entityDependencies`: An array where you specify entity types and names (e.g. `"Things/MyThing"`). This can be used to download specific entities, such as extension templates.

Whenever you change `twconfig.json`, run `npx twc install` to pull the declarations from the Thingworx server specified in `package.json`.

Whenever entities are downloaded in this way, all other dependencies are downloaded as well, so if your `MyThing` thing depends on the `MyDataShape` data shape, you don't need to specify both in `twconfig.json`.

## File Structure
```
ThingworxVSCodeProject
│   README.md              // This file
│   package.json           // Standard npm package details; the package name is also used as the extension name
│   tsconfig.json          // Standard typescript configuration file
│   twconfig.json          // Thingworx-specific configuration
│   metadata.xml           // Thingworx metadata file for this extension; This is automatically updated based on your package.json and build settings
│   LICENSE                // License file
│   .env                   // Contains environment variables and thingworx connection details
└───src                    // Main folder where your development will take place
│   └───file1.ts           // Thingworx entity file
│   │   ...
│   └───[Project1]         // In multi-project mode, each subfolder represents a thingworx project
│   │   └───src            // Project's source folder
│   │   │   └───file2.ts   // Thingworx entity file
│   │   │   ...
│   │   └───tsconfig.json  // Project's typescript configuration file; Project dependencies are extracted from this
└───static                 // Supporting files required for compilation; Don't change these
└───tw_imports             // Entity dependencies downloaded from a Thingworx server
└───build                  // Temporary folder used during compilation
└───zip                    // Location of the built extension
```

## Source Files

Any TypeScript file you add to the `src` folder will be compiled into a Thingworx entity. There are some restrictions on what is allowed to be included in these files, namely:

 - There may only be a single class definition per file - each file must correspond to a single entity
 - The root of the file may only contain:
    - One class definition
    - Interface definitions
    - Enums, but only if they are declared `const` since these are erased at runtime
    - Comments
 - Property, method argument and return types can use either Thingworx base types or TypeScript standard types such as `string`, `number`. 
 - Imports and modules are ignored; these will lead to runtime errors if used


## Current Limitations

 - Only Things, ThingTemplates, ThingShapes, DataShapes, Users, Groups, Organizations, Mashups, StyleDefinitions, StateDefinitions and Projects are supported currently. For other types of entities you will still need to use the composer.
 - Flow services and other service types except javascript and SQL are not supported.
 - Tags cannot be specified currently.
 - XML handling via E4X is not and will not be supported. Using E4X syntax will lead to a compilation error. You should use the various XML methods to work around this limitation.
 - Session variables and user extensions are not currently supported as data sources in mashups. You should instead obtain this information via services if needed.

## Build

To build the extension, run `npm run build` in the root of the project. This will generate an extension .zip file in the zip folder in the root of the project.

To build the extension and upload it to Thingworx, run `npm run upload` in the root of the project. The details of the Thingworx server to which the script will upload the extension are declared in the project's environment or `package.json` file. These are:
 * `thingworxServer` - The server to which the extension will be uploaded.
 * `thingworxAppKey` or `thingworxUser` and `thingworxPassword` - The credentials used for uploading. This should be a user that has permission to install extensions.

To create a debug build that can be used for debugging, add the `--debug` argument to any task. For example, to create and upload a debug build, run `npx twc upload --debug`. For more information about debugging and debug builds, see [BMDebugServer](https://github.com/BogdanMihaiciuc/BMDebugServer) and [ThingworxVSCodeDebugger](https://github.com/BogdanMihaiciuc/ThingworxVSCodeDebugger).

## Deployment

Deployment to Thingworx is part of the build process as explained above. Alternatively, you can manually install the extension that is generated in the zip folder in the root of the project.

# Recent Changes

For a complete changelog see [CHANGELOG.md](CHANGELOG.md).

## 31 Mar 2024

 - Support for mashups, Core UI mashups and CSS
 - Support for style definitions
 - Support for state definitions

## 28 Mar 2023
 - Support conditionally compiled branches
 - Support for calling superclass implementations
 - Support for inline SQL on non-database entities

## 11 Dec 2022
- Support for trace builds
- Support for XML entities

## 2 May 2022

- Support for inline SQL
- Support for generating `fieldDefinitions` exports
- Support for the `override` keyword

# Credit/Acknowledgment

[Petrisor Lacatus](https://github.com/stefan-lacatus) - had the original idea for this, inferred property types and bug fixes, support for method helpers, API generation, data shape inheritance, `declare` on class members.

[Bogdan Mihaiciuc](https://github.com/BogdanMihaiciuc) - main developer.

[thomas-thomas-tfs](https://github.com/thomas-thomas-tfs) - deployment using app keys.

[carlo-quinonez](https://github.com/carlo-quinonez) - bug fixes and documentation improvements.

[dwil618](https://github.com/dwil618) - support for min/max aspects and date initializers.

[CozminM](https://github.com/CozminM), [elena-bi](https://github.com/elena-bi) - bug fixes

[kklorenzotesta](https://github.com/kklorenzotesta) - thingworx API typing updates

# Disclaimer

The Thingworx VSCode Project is not an official Thingworx product. It is something developed to improve the life of a Thingworx developer and they need to understand that it is not supported and any issues encountered are their own responsibility.

# License

[MIT License](LICENSE)

---

### Prerequisites for Using ThingworxVSCodeProject

To successfully use this project template, you should have:  

1. **Thingworx Expertise**  
   - A solid understanding of the Thingworx modeling environment and how it works.  

2. **TypeScript Knowledge**  
   - Basic familiarity with TypeScript, including concepts like interfaces, private methods, and modern JavaScript features.  

3. **IDE Experience**  
   - Comfort with working in Visual Studio Code or a similar TypeScript-supported IDE.  

4. **Git Basics**  
   - Knowledge of Git for version control and collaboration.  

5. **Extensions Setup** (Optional for Advanced Features)  
   - **ThingworxVSCodeDebugger**: For debugging support.  
   - **BMDebugServer**: Required for debugging.  
   - **BMProfiler**: For performance profiling of Thingworx services and functions.  

Let me know if you need additional clarification!

### Overview of the First Prerequisite: **Thingworx Expertise**

To effectively use this project template, you must have a **solid understanding of the Thingworx modeling environment**, which includes:  

1. **Thingworx Composer**:  
   - Proficiency in using Composer to create and manage entities such as Things, Data Shapes, Services, and Mashups.  
   - Familiarity with the UI and workflows for defining relationships, permissions, and configurations.  

2. **Modeling Concepts**:  
   - Knowledge of the foundational elements of Thingworx, including Things, Thing Templates, Thing Shapes, Data Shapes, and Services.  
   - Understanding how these components interact within the Thingworx platform.  

3. **Service Development**:  
   - Experience writing services using the built-in scripting environment (usually JavaScript or Groovy).  

4. **Thingworx Deployment**:  
   - Familiarity with exporting and importing entities or extensions for deployment in different environments.  

5. **Thingworx Runtime**:  
   - Awareness of how runtime changes impact the system and the best practices for safely updating live systems.  

This expertise ensures a smoother transition to developing Thingworx models in a TypeScript-based IDE like Visual Studio Code.

### Overview of the Second Prerequisite: **TypeScript Knowledge**

To effectively use this project template, you should have a **basic understanding of TypeScript**, which includes:  

1. **Core TypeScript Features**:  
   - **Types and Interfaces**: Understanding how to define and use types (`string`, `number`, etc.) and interfaces to enforce structure and consistency in your code.  
   - **Classes and Inheritance**: Familiarity with object-oriented programming concepts such as classes, constructors, inheritance, and method overriding.  

2. **Access Modifiers**:  
   - Using `public`, `private`, and `protected` to control access to class members, even though Thingworx may not preserve these at runtime.  

3. **Advanced JavaScript Features**:  
   - Knowledge of modern JavaScript (ES6+), such as arrow functions, destructuring, template literals, and modules, which can be transpiled to Thingworx-compatible code.  

4. **Transpilation**:  
   - Understanding how TypeScript is transpiled to JavaScript and its implications for runtime compatibility in Thingworx.  

5. **Error Checking and Debugging**:  
   - Leveraging TypeScript’s static type-checking to identify potential errors during development, making your code more robust and maintainable.  

6. **IDE Integration**:  
   - Using TypeScript tools and features in Visual Studio Code, such as IntelliSense, code navigation, and refactoring tools, to enhance productivity.  

This foundation allows you to utilize TypeScript’s capabilities effectively while working within the constraints of Thingworx.

### Overview of the Third Prerequisite: **IDE Experience**

To effectively use this project template, you should be **comfortable working in a TypeScript-supported Integrated Development Environment (IDE)** like Visual Studio Code, including:  

1. **Project Navigation**:  
   - Familiarity with exploring file structures, opening multiple files, and managing workspace settings.  

2. **Code Editing Features**:  
   - Using IDE features like syntax highlighting, IntelliSense (code autocompletion and suggestions), and quick fixes to streamline coding.  

3. **Search and Refactoring**:  
   - Leveraging tools for project-wide searches, replacing text, renaming symbols, and refactoring code efficiently.  

4. **Debugging Tools**:  
   - Setting breakpoints, stepping through code, inspecting variables, and monitoring execution flows using the built-in debugger or extensions like **ThingworxVSCodeDebugger**.  

5. **Extensions and Plugins**:  
   - Installing and configuring IDE extensions such as TypeScript language support, Git integration, and specialized tools like the Thingworx debugger or profiler.  

6. **Command Line Integration**:  
   - Running terminal commands directly from the IDE for tasks like building, publishing, or deploying extensions.  

This experience ensures that you can fully utilize the IDE’s capabilities to enhance productivity and streamline Thingworx development.

### Overview of the Fourth Prerequisite: **Git Basics**

To use this project template effectively, you should have a **working knowledge of Git for version control**, including:  

1. **Core Git Concepts**:  
   - Understanding repositories, commits, branches, and merges.  
   - Familiarity with initializing a repository and managing changes.  

2. **Version History**:  
   - Viewing commit history and changes to track project evolution.  
   - Using Git commands like `git log` and `git diff` to inspect modifications.  

3. **Branching and Collaboration**:  
   - Creating and managing branches to work on features or fixes independently.  
   - Merging branches and resolving conflicts when changes overlap.  

4. **Remote Repositories**:  
   - Cloning, pushing, and pulling changes from remote repositories like GitHub, GitLab, or Bitbucket.  
   - Managing remotes for team collaboration.  

5. **Basic Workflows**:  
   - Staging changes with `git add`, committing with `git commit`, and undoing mistakes with commands like `git checkout` or `git revert`.  

6. **Tool Integration**:  
   - Using Git integration features within IDEs like Visual Studio Code for easier commit, push, pull, and merge operations.  

This foundation allows for smooth collaboration, version tracking, and maintaining a clean and organized codebase in Thingworx development projects.

### Overview of the Fifth Prerequisite: **Extensions Setup (Optional for Advanced Features)**

To leverage advanced debugging and profiling capabilities in this project template, you should be familiar with setting up the following extensions:  

1. **ThingworxVSCodeDebugger**:  
   - A Visual Studio Code extension that enables direct debugging of Thingworx TypeScript projects.  
   - Features include setting breakpoints, exception handling, stepping through code, and inspecting or modifying variable values.  

2. **BMDebugServer**:  
   - A Thingworx extension required for the debugger to connect to the Thingworx server.  
   - You should know how to install and configure this extension within the Thingworx platform.  

3. **BMProfiler**:  
   - A Thingworx extension used for performance profiling.  
   - Allows tracing service execution, identifying bottlenecks, and generating reports compatible with Chrome's performance inspector.  

4. **Installation and Configuration**:  
   - Installing these extensions in their respective environments (IDE or Thingworx).  
   - Configuring them for compatibility with your TypeScript project and Thingworx instance.  

5. **Usage**:  
   - Following the relevant guides (e.g., Usage Guide or extension documentation) to integrate debugging and profiling seamlessly into your workflow.  

These tools are optional but significantly enhance the development process by adding debugging precision and performance insights.