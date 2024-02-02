## Steps to set up jest in vite react project

1. Packages to install
    ```
    yarn add --dev jest babel-jest @babel/preset-env @babel/preset-react @babel/core react-test-renderer
    yarn add --dev @testing-library/react @types/jest
    yarn add jest-environment-jsdom
    ```

2. Optional: If use Fetch API in the project
    ```
    yarn add --dev whatwg-fetch
    ```

3. Update the scripts __package.json__
    ```
    "scripts: {
        ...
        "test": "jest --watchAll"
    ```

4. Create configuration babel __babel.config.js__
    ```
    export default {
        presets: [
            ['@babel/preset-env', {targets: {esmodules: true, node: 'current'}}],
            ['@babel/preset-react', {runtime: 'automatic'}],
        ],
    };
    ```

5. Optional, but if you going to work with syntaxis DOM Navigator as .toBeInTheDocument(), .toBeDisabled(), you should create config Jest and setup

    __jest.config.js__
    ```
    export default {
        testEnvironment: 'jest-environment-jsdom',
        
        //this line works me to make a global configurtaion before run the tests
        setupFiles: ['./jest.setup.js']
    }
    ```

    __jest.setup.js__
    ```
    //we need to install some polyfills for the fetch method, because the fetch method is not recognized in the versions below 18 of nodejs, so, we need to install the polyfill whatwg-fetch, and import it in the file jest.setup.js

    import 'whatwg-fetch';
    ```

