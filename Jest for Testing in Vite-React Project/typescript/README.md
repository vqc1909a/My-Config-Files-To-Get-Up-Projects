## Steps to set up jest in vite react project with typescript

1. Packages to install

   ```
   yarn add --dev jest jest-css-modules jest-transform-stub @types/jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
   yarn add jest-environment-jsdom
   ```

2. Packages additional for typescript
   ```
   yarn add -D ts-jest ts-node
   ```
3. If use msw package for mock services from until v2 to up

   ```
   yarn add -D msw undici@5.0.0
   ```

4. Optional: If use Fetch API, install this polyfill for it

   ```
   yarn add --dev whatwg-fetch
   ```

5. Update the scripts

   **package.json**

   ```
   "scripts: {
       ...
       "test": "jest --watchAll --detectOpenHandles"
   ```

6. Create config Jest && Setup && Polyfill

   **jest.config.ts**

   ```
   export default {
       preset: 'ts-jest',
       testEnvironment: 'jest-environment-jsdom',

       // To fix "Cannot find module ‘msw/node’ (JSDOM), set the testEnvironmentOptions.customExportConditions option in your jest.config.js to [''] => https://mswjs.io/docs/migrations/1.x-to-2.x#requestresponsetextencoder-is-not-defined-jest
       testEnvironmentOptions: {
           customExportConditions: [''],
       },

       // To process `*.tsx` files with `ts-jest`
       transform: {
           "^.+\\.tsx?$": "ts-jest"
       },

       // This specifies a list of files that should be run after setting up the testing framework but before running the tests.
       setupFilesAfterEnv: ['<rootDir>/jest.setup.ts'],

       // To fix R"equest/Response/TextEncoder is not defined (Jest)"
       setupFiles: ['<rootDir>/jest.polyfills.ts'],

       // This configuration tells Jest to use jest-transform-stub for SVG (and other static file) imports. Now, when you import an SVG file in your tests, Jest will replace it with a stub and won't throw an error.
       moduleNameMapper: {
           "\\.(css|less|scss|sss|styl)$": "jest-css-modules",
           "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "jest-transform-stub"
       },

       testPathIgnorePatterns: ['/node_modules/', '/dist/', '/.yarn/'],
   }
   ```

   **jest.setup.ts**

   ```
   //If will install the polyfill Fetch API, to will need to import this package
   import 'whatwg-fetch';
   ```

   **jest.polyfills.ts**

   ```
   // If use msw package for mock services from until v2 to up, you need this polyfill

   /\*\*

   - @note The block below contains polyfills for Node.js globals
   - required for Jest to function when running JSDOM tests.
   - These HAVE to be require's and HAVE to be in this exact
   - order, since "undici" depends on the "TextEncoder" global API.
   -
   - Consider migrating to a more modern test runner if
   - you don't want to deal with this.
   \*/

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
   ```

7. Update the file tsconfig.json for avoid syntax mistakes jest and node not recognized for typescript

   ```
   {
       "compilerOptions": {
           ...
           /* Jest */
           "esModuleInterop": true,
           "types": ["jest", "node"],
       },
       // The include property in the tsconfig.json file is used to specify a list of glob patterns for files that should be included in your TypeScript project.
       "include": ["src", "jest.polyfills.ts"],
       "references": [{ "path": "./tsconfig.node.json" }]
   }

   ```

8. For avoid some mistakes jest DOM syntax in file tests, you need to import the following line in the top in each file

   ```
   import '@testing-library/jest-dom'
   ```

9. More info
   *https://mswjs.io/docs/migrations/1.x-to-2.x#requestresponsetextencoder-is-not-defined-jest*
   *https://github.com/mswjs/msw/discussions/1934*

10. \***\*Now you are ready to test without problems with typescript\*\***
