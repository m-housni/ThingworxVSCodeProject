Dependencies in a JavaScript package are installed using a package manager like **npm** (Node Package Manager) or **yarn**, which reads the `package.json` file in the project directory to determine the required packages and their versions.

---

### Steps for Installing Dependencies

1. **Create or Update `package.json`**  
   The `package.json` file lists all the dependencies required for the project under `dependencies` and `devDependencies`.

   Example:
   ```json
   {
     "dependencies": {
       "express": "^4.18.0",
       "lodash": "^4.17.21"
     },
     "devDependencies": {
       "jest": "^29.0.1",
       "typescript": "^5.2.0"
     }
   }
   ```

---

2. **Install Dependencies**  

   #### Using `npm`:
   - To install all listed dependencies:
     ```bash
     npm install
     ```
   - This command:
     - Reads the `package.json` file.
     - Installs the required dependencies into the `node_modules` directory.
     - Updates or creates a `package-lock.json` file, locking exact versions of the installed dependencies.

   #### Using `yarn`:
   - To install all dependencies:
     ```bash
     yarn install
     ```
   - Works similarly to `npm install`, creating or updating a `yarn.lock` file for dependency version locking.

---

3. **Install Specific Dependencies**  
   - Install a single dependency (e.g., `axios`):
     ```bash
     npm install axios
     ```
   - For a development-only dependency:
     ```bash
     npm install --save-dev jest
     ```
   - Using yarn:
     ```bash
     yarn add axios        # Regular dependency
     yarn add --dev jest   # Development dependency
     ```

---

4. **Automatic Dependency Installation**  
   When you clone a repository or start working on an existing project:
   - Run `npm install` or `yarn install` to download all the dependencies defined in `package.json`.

---

### How Dependencies Are Resolved

- **Direct Dependencies**: Installed in the `node_modules` folder of the project. These are specified explicitly in `package.json`.
- **Transitive Dependencies**: Dependencies of the installed packages. These are resolved automatically by the package manager.

---

### Lock Files

- **`package-lock.json` (npm)** or **`yarn.lock` (yarn)** ensures:
  - Consistent dependency versions across different environments.
  - Faster installations by using the resolved versions.

---

### Summary

Dependencies are managed via `package.json` and installed using commands like `npm install` or `yarn install`. The process involves resolving dependencies, downloading them, and saving exact versions in a lock file to ensure reproducible builds.