## Steps to set up jest in vite react project with javascript

1. Packages to install
    ```
    yarn add --dev @types/jest jest babel-jest @babel/core @babel/preset-env @babel/preset-react @testing-library/react @testing-library/jest-dom @testing-library/user-event 
    yarn add jest-environment-jsdom
    ```

2. Optional: If use Fetch API in the project
    ```
    yarn add --dev whatwg-fetch
    ```

3. Update the scripts 

    __package.json__
    ```
    "scripts: {
        ...
        "test": "jest --watchAll"
    ```

4. Create config babel

     __babel.config.js__

    ```
    export default {
        presets: [
            ['@babel/preset-env', {targets: {esmodules: true, node: 'current'}}],
            ['@babel/preset-react', {runtime: 'automatic'}],
        ],
    };
    ```

5. Create config Jest and Setup

    __jest.config.js__
    ```
    export default {
        testEnvironment: 'jest-environment-jsdom',
        
        //This line works making a global configurtaion before run the tests
        setupFiles: ['./jest.setup.js']
    }
    ```

    __jest.setup.js__
    ```
    //we need to install some polyfills for the fetch method
    import 'whatwg-fetch';
    ```

