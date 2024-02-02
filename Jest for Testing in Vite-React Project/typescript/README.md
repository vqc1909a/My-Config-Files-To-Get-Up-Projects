## Steps to set up jest in vite react project with javascript

1. Packages to install
    ```
    yarn add --dev jest @types/jest babel-jest @babel/core @babel/preset-env @babel/preset-react @testing-library/react @testing-library/jest-dom @testing-library/user-event 
    yarn add jest-environment-jsdom
    ```

2. Packages additional for typescript
    ```
    yarn add -D ts-jest ts-node undici@5.0.0
    ```

3. Optional: If use Fetch API in the project
    ```
    yarn add --dev whatwg-fetch
    ```

4. Update the scripts 

    __package.json__
    ```
    "scripts: {
        ...
        "test": "jest --watchAll"
    ```

5. Create config Jest && Setup && Polyfill

    __jest.config.js__
    ```
    export default {
        preset: 'ts-jest',
        testEnvironment: 'jest-environment-jsdom',
        
        // To fix "Cannot find module ‘msw/node’ (JSDOM)"
        testEnvironmentOptions: {
            customExportConditions: [''],
        },

        // To process `*.tsx` files with `ts-jest`
        transform: {
            "^.+\\.tsx?$": "ts-jest" 
        },
        
        // Run after setting up the testing framework but before running the tests.
        setupFilesAfterEnv: ['<rootDir>/jest.setup.ts'],

        // To fix R"equest/Response/TextEncoder is not defined (Jest)"
        setupFiles: ['<rootDir>/jest.polyfills.ts'],

        testPathIgnorePatterns: ['/node_modules/', '/dist/', '/.yarn/'],
    }
  
    ```

    __jest.setup.js__
    ```
    //we need to install some polyfills for the fetch method
    import 'whatwg-fetch';
    ```

    __jest.polyfills.ts__
    /**
    * @note The block below contains polyfills for Node.js globals
    * required for Jest to function when running JSDOM tests.
    * These HAVE to be require's and HAVE to be in this exact
    * order, since "undici" depends on the "TextEncoder" global API.
    *
    * Consider migrating to a more modern test runner if
    * you don't want to deal with this.
    */
    
    import { TextDecoder, TextEncoder } from 'node:util'
    
    Object.defineProperties(globalThis, {
        TextDecoder: { value: TextDecoder },
        TextEncoder: { value: TextEncoder },
    })
    
    import { Blob, File } from 'node:buffer'
    import { fetch, Headers, FormData, Request, Response } from 'undici'
    
    Object.defineProperties(globalThis, {
        fetch: { value: fetch, writable: true },
        Blob: { value: Blob },
        File: { value: File },
        Headers: { value: Headers },
        FormData: { value: FormData },
        Request: { value: Request },
        Response: { value: Response },
    })

7. More info
*https://mswjs.io/docs/migrations/1.x-to-2.x#requestresponsetextencoder-is-not-defined-jest*
*https://github.com/mswjs/msw/discussions/1934*

