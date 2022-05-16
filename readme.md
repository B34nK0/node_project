Prerequisites
Before you begin this guide, you will need Node.js installed on your system. You can accomplish this by following the How to Install Node.js and Create a Local Development Environment guide for your operating system.

Step 1 — Initializing the Project
To get started, create a new folder named node_project and move into that directory:

mkdir node_project
cd node_project
Next, initialize it as an npm project:

npm init -y
The -y flag tells npm init to automatically say “yes” to the defaults. You can always update this information later in your package.json file.

Step 2 — Configuring the TypeScript Compiler
Now that your npm project is initialized, you are ready to install and set up TypeScript.

Run the following command from inside your project directory to install the TypeScript:

npm install --save-dev typescript
Output
added 1 package, and audited 2 packages in 1s

found 0 vulnerabilities
TypeScript uses a file called tsconfig.json to configure the compiler options for a project. Create a tsconfig.json file in the root of the project directory:

nano tsconfig.json
Then paste in the following JSON:

tsconfig.json
{
  "compilerOptions": {
    "module": "commonjs",
    "esModuleInterop": true,
    "target": "es6",
    "moduleResolution": "node",
    "sourceMap": true,
    "outDir": "dist"
  },
  "lib": ["es2015"]
}
Let’s go over some of the keys in the JSON snippet above:

module: Specifies the module code generation method. Node uses commonjs.
target: Specifies the output language level.
moduleResolution: This helps the compiler figure out what an import refers to. The value node mimics the Node module resolution mechanism.
outDir: This is the location to output .js files after transpilation. In this tutorial you will save it as dist.
To learn more about the key value options available, the official TypeScript documentation offers explanations of every option.

Step 3 — Creating a Minimal TypeScript Express Server
Now, it is time to install the Express framework and create a minimal server:

npm install --save express@4.17.1
npm install -save-dev @types/express@4.17.1
The second command installs the Express types for TypeScript support. Types in TypeScript are files, normally with an extension of .d.ts. The files are used to provide type information about an API, in this case the Express framework.

This package is required because TypeScript and Express are independent packages. Without the @types/express package, there is no way for TypeScript to know about the types of Express classes.

Next, create a src folder in the root of your project directory:

mkdir src
Then create a TypeScript file named app.ts within it:

nano src/app.ts
Open up the app.ts file with a text editor of your choice and paste in the following code snippet:

src/app.ts
import express from 'express';
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  return console.log(`Express is listening at http://localhost:${port}`);
});
The code above creates Node Server that listens on the port 3000 for requests. To run the app, you first need to compile it to JavaScript using the following command:

npx tsc
This uses the configuration file we created in the previous step to determine how to compile the code and where to place the result. In our case, the JavaScript is output to the dist directory.

Run the JavaScript output with node:

node dist/app.js
If it runs successfully, a message will be logged to the terminal:

Output
Express is listening at http://localhost:3000
Now, you can visit http://localhost:3000 in your browser and you should see the message:

Output
Hello World!
Open the dist/app.js file and you will find the transpiled version of the TypeScript code:

dist/app.js
"use strict";
var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
};
Object.defineProperty(exports, "__esModule", { value: true });
const express_1 = __importDefault(require("express"));
const app = (0, express_1.default)();
const port = 3000;
app.get('/', (req, res) => {
    res.send('Hello World!');
});
app.listen(port, () => {
    return console.log(`Express is listening at http://localhost:${port}`);
});
//# sourceMappingURL=app.js.map
At this point you have successfully set up your Node project to use TypeScript. Next you’ll set up the eslint linter to check your TypeScript code for errors.

Step 4 — Configuring Typescript Linting with eslint
Now you can configure TypeScript linting for the project. First, we install eslint using npm:

npm install --save-dev eslint
Then, run eslint’s initialization command to interactively set up the project:

npx eslint --init
This will ask you a series of questions. For this project we’ll answer the following:

How would you like to use ESLint?: To check syntax and find problems
What type of modules does your project use?: JavaScript modules (import/export)
Which framework does your project use?: None of these
Does your project use TypeScript?: Yes
Where does your code run?: Node
What format do you want your config file to be in?: JavaScript
Finally, you will be prompted to install some additioanl eslint libraries. Choose Yes. The process will finish and you’ll be left with the following configuration file:

eslintrc.js
module.exports = {
  env: {
    es2021: true,
    node: true,
  },
  extends: ['eslint:recommended', 'plugin:@typescript-eslint/recommended'],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 13,
    sourceType: 'module',
  },
  plugins: ['@typescript-eslint'],
  rules: {},
}
Run the linter to check all files with the .ts TypeScript extension:

npx eslint . --ext .ts
You’ve now set up the eslint linter to check your TypeScript syntax. Next you’ll update your npm configuration to add some convenient scripts for linting and running your project.

Step 5 — Updating the package.json File
It can be useful to put your commonly run command line tasks into npm scripts. npm scripts are defined in your package.json file and can be run with the command npm run your_script_name.

In this step you will add a start script that will transpile the TypeScript code then run the resulting .js application.

You will also add a lint script to run the eslint linter on your TypeScript files.

Open the package.json file and update it accordingly:

package.json
{
  "name": "node_project",
  "version": "1.0.0",
  "description": "",
  "main": "dist/app.js",
  "scripts": {
    "start": "tsc && node dist/app.js",
    "lint": "eslint . --ext .ts",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/express": "^4.17.1",
    "@typescript-eslint/eslint-plugin": "^5.4.0",
    "@typescript-eslint/parser": "^5.4.0",
    "eslint": "^8.3.0",
    "typescript": "^4.5.2"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}
In the snippet above, you updated the main path to be the compiled app output, and added the start and lint commands to the scripts section.

When looking at the start command, you’ll see that first the tsc command is run, and then the node command. This will compile and then run the generated output with node.

The lint command is the same as we ran in the previous step, minus the use of the npx prefix which is not needed in this context.