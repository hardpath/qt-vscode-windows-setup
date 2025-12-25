# Qt 6.6+ Development Environment Setup Checklist

**Target Platform:** Windows 11 (64-bit)  
**Reference Guide:** See [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md) for detailed instructions

---

## Part 1: Environment Setup

### Phase 1: Uninstall & Clean

- [ ] 1.1 Uninstall existing Qt installation via Windows Settings or Qt Maintenance Tool
- [ ] 1.2 Remove Qt-related environment variables (PATH, Qt6_DIR, CMAKE_PREFIX_PATH)
- [ ] 1.3 Clean up leftover directories (C:\Qt, AppData folders) and registry entries
- [ ] 1.4 Restart system to ensure clean state

### Phase 2: Install Core Tools

- [ ] 2.1 Install Build Tools for Visual Studio 2022 (MSVC + Windows SDK) **[MANDATORY]**
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#build-tools-for-visual-studio-2022)
- [ ] 2.2 Verify Git installation: `git --version`
- [ ] 2.3 Verify MSVC compiler accessibility via Developer Command Prompt: `where cl`
  > ⚠️ **Can't find cl.exe?** See [troubleshooting section](DEVELOPMENT_SETUP.md#troubleshooting) for compiler issues
- [ ] 2.4 Set MSVC environment variables (User variables)
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#environment-variables)
  - **Important:** Restart terminal/VS Code after setting variables

### Phase 3: Install Qt 6.6+

- [ ] 3.1 Download Qt Online Installer from qt.io
- [ ] 3.2 Create Qt account (if needed)
- [ ] 3.3 Install Qt 6.10.1 (or latest 6.6+)
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#qt-66)
- [ ] 3.4 Add Qt directories to PATH:
  - `C:\Qt\6.10.1\msvc2022_64\bin`
  - `C:\Qt\Tools\CMake_64\bin`
  - `C:\Qt\Tools\Ninja`
- [ ] 3.5 Set CMAKE_PREFIX_PATH environment variable: `C:\Qt\6.10.1\msvc2022_64`
- [ ] 3.6 Verify installations:
  - `cmake --version`
  - `ninja --version`
  - `qmake --version`
  - `echo $env:CMAKE_PREFIX_PATH`
  > ⚠️ **Commands not found?** Restart your terminal/VS Code and check [environment variables setup](DEVELOPMENT_SETUP.md#environment-variables)

### Phase 4: Install Testing Framework

- [ ] 4.1 Install vcpkg
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#vcpkg-c-package-manager)
- [ ] 4.2 Install Google Test: `vcpkg install gtest:x64-windows`
- [ ] 4.3 Integrate vcpkg with CMake: `vcpkg integrate install`
- [ ] 4.4 Record vcpkg toolchain file path (displayed after integration)

### Phase 5: Configure VS Code

- [ ] 5.1 Install VS Code (if not already installed)
- [ ] 5.2 Install extensions:
  - C/C++ (Microsoft)
  - CMake Tools (Microsoft)
  - Qt Extension Pack (The Qt Company)
  - GitHub Copilot (if licensed)
  > 💡 **Extension configuration needed?** See [VS Code Setup](DEVELOPMENT_SETUP.md#vs-code-setup) for detailed configuration

---

## Part 2: Template Repository Setup (One-time)

> **Goal:** Obtain a reusable Qt project template

### Option A: Use Existing Template (Recommended)

- [ ] Skip to Phase 4 below to verify the template: https://github.com/hardpath/qt-project-template

### Option B: Create Template from Scratch

> **Execute only if creating a new custom template**

### Phase 1: Create Template Repository

- [ ] 1.1 Create GitHub repository: `qt-project-template`
- [ ] 1.2 Clone repository locally
- [ ] 1.3 Create directory structure: `src/`, `tests/`, `docs/`
- [ ] 1.4 Create documentation files:
  - `README.md` - Project overview
  - `REQUIREMENTS.md` - Technical specifications template
  - `CONTRIBUTING.md` - Commit message conventions
- [ ] 1.5 Create `.gitignore` file
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#project-structure)
- [ ] 1.6 Create `CMakePresets.json`
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#cmakepresetsjson)
- [ ] 1.7 Create top-level `CMakeLists.txt`
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#cmakeliststxt)
- [ ] 1.8 Create `src/main.cpp`
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#srcmaincpp)
- [ ] 1.9 Create `tests/test_main.cpp`
  - Refer to: [DEVELOPMENT_SETUP.md](DEVELOPMENT_SETUP.md#teststest_maincpp)

### Phase 2: Configure VS Code and Verify Build

- [ ] 2.1 Open template project folder in VS Code
- [ ] 2.2 Select CMake Configure Preset: "Windows Debug"
- [ ] 2.3 Configure CMake: `Ctrl+Shift+P` → "CMake: Configure"
- [ ] 2.4 Verify IntelliSense (no red squiggles in `src/main.cpp`)
- [ ] 2.5 Build project: Press `F7`
- [ ] 2.6 Run application: `.\build\debug\ProjectName.exe`
- [ ] 2.7 Run tests: `ctest --test-dir build/debug --output-on-failure`
- [ ] 2.8 Verify all tests pass

### Phase 3: Commit Template to Repository

- [ ] 3.1 Review git status: `git status`
- [ ] 3.2 Stage all files: `git add .`
- [ ] 3.3 Commit: `git commit -m "Initial Qt project template"`
- [ ] 3.4 Push to GitHub: `git push origin master`

### Phase 4: Final Verification (Both Options)

- [ ] 4.1 Clone template to test location:
  - Using existing: `git clone https://github.com/hardpath/qt-project-template.git qt-test`
  - Using custom: `git clone <your-template-url> qt-test`
- [ ] 4.2 Open `qt-test` folder in VS Code
- [ ] 4.3 Update paths in `CMakePresets.json` to match your installation
- [ ] 4.4 Configure CMake Kit (Visual Studio Build Tools 2022)
- [ ] 4.5 Select Configure Preset: "Windows Debug"
- [ ] 4.6 Configure build: `cmake --preset windows-debug`
- [ ] 4.7 Build project: `cmake --build --preset windows-debug`
- [ ] 4.8 Run tests: `ctest --test-dir build/debug --output-on-failure`
- [ ] 4.9 Verify successful build and test execution
- [ ] 4.10 Clean up test directory: `Remove-Item -Recurse -Force qt-test`

---

## Part 3: Use Template for New Projects

> **Goal:** Set up a new project from the template. Repeat for each new project.

### Phase 1: Clone Template and Create New Repository

- [ ] 1.1 Clone template: `git clone https://github.com/hardpath/qt-project-template.git <new-project-name>`
- [ ] 1.2 Navigate to project: `cd <new-project-name>`
- [ ] 1.3 Remove git history: `rm -rf .git`
- [ ] 1.4 Create new GitHub repository for the project
- [ ] 1.5 Initialize git: `git init`
- [ ] 1.6 Add remote: `git remote add origin <new-project-url>`

### Phase 2: Configure Project

- [ ] 2.1 Update `CMakeLists.txt`: Replace `ProjectName` and add required Qt modules
- [ ] 2.2 Update `README.md` with project-specific information
- [ ] 2.3 Update `REQUIREMENTS.md` with project requirements
- [ ] 2.4 Update paths in `CMakePresets.json` to match your installation

### Phase 3: Configure VS Code and Verify Build

- [ ] 3.1 Open project folder in VS Code
- [ ] 3.2 Configure CMake Kit (Visual Studio Build Tools 2022)
- [ ] 3.3 Select Configure Preset: "Windows Debug"
- [ ] 3.4 Configure CMake: `Ctrl+Shift+P` → "CMake: Configure"
- [ ] 3.5 Verify IntelliSense (no red squiggles)
- [ ] 3.6 Build project: Press `F7`
- [ ] 3.7 Run tests: `ctest --test-dir build/debug --output-on-failure`
- [ ] 3.8 Verify all tests pass

### Phase 4: Initial Commit

- [ ] 4.1 Review changes: `git status`
- [ ] 4.2 Stage files: `git add .`
- [ ] 4.3 Commit: `git commit -m "Initial project setup from qt-project-template"`
- [ ] 4.4 Push: `git push -u origin main`

---

## Final Verification

- [ ] CMake can find Qt libraries
- [ ] Google Test framework compiles and runs
- [ ] VS Code IntelliSense recognises Qt classes
- [ ] Build artifacts generate in build/ directory
- [ ] CTest executes all tests successfully
