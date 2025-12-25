# Development Setup Guide

**Purpose:** This guide provides detailed instructions and reference materials for setting up a Qt 6.6+ C++ development environment on Windows. Use this alongside `SETUP_CHECKLIST.md` for step-by-step execution.

**Target Platform:** Windows 11 (64-bit)  
**Companion Document:** `SETUP_CHECKLIST.md`

> 📋 **Following step-by-step?** Use [SETUP_CHECKLIST.md](SETUP_CHECKLIST.md) for a streamlined setup process

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Prerequisites](#prerequisites)
3. [Environment Variables](#environment-variables)
4. [VS Code Setup](#vs-code-setup)
5. [Project Structure](#project-structure)
6. [CMake Configuration](#cmake-configuration)
7. [Template Files](#template-files)
8. [Build and Run](#build-and-run)
9. [Testing Framework](#testing-framework)
10. [Troubleshooting](#troubleshooting)
11. [Resources](#resources)

---

## Quick Start

**Ready-Made Template Repository:** https://github.com/hardpath/qt-project-template.git

This repository contains a working Qt 6 project template with:
- Pre-configured CMake setup (CMakeLists.txt, CMakePresets.json)
- Project structure (src/, tests/, docs/)
- Template files (main.cpp, test_main.cpp)
- Documentation (README.md, REQUIREMENTS.md, CONTRIBUTING.md)
- Git ignore rules (.gitignore)

**To use this template:**
1. Complete Part 1 of `SETUP_CHECKLIST.md` (environment setup)
2. Clone the template: `git clone https://github.com/hardpath/qt-project-template.git`
3. Update paths in `CMakePresets.json` to match your installation
4. Follow Part 2, Phase 4 of `SETUP_CHECKLIST.md` to verify the template

**To create a new project from the template:**
- Follow Part 3 of `SETUP_CHECKLIST.md`

The sections below provide detailed reference for all files and configurations included in the template.

---

## Prerequisites

### Build Tools for Visual Studio 2022

**Download:** [Visual Studio 2022 Release History](https://learn.microsoft.com/en-gb/visualstudio/releases/2022/release-history)

**Installation Steps:**
1. Select **Fall 2024 LTSC (Version 17.12)** → Click **Build Tools** link
2. Run the installer
3. Select workload: **"Desktop development with C++"**
4. Ensure the following **optional components** are checked:
   - MSVC v143 - VS 2022 C++ x64/x86 build tools
   - Windows 11 SDK (10.0.22621.0)
   - C++ CMake tools for Windows
   - Testing tools core features - Build Tools
5. **Uncheck these** to keep installation lean (~6GB):
   - C++ AddressSanitizer
   - C++ ATL
   - C++ MFC
   - C++/CLI support
   - C++ Modules
   - C++ Clang tools
   - Extra SDKs
6. Complete installation

**Verification:**
```powershell
# Open "Developer Command Prompt for VS 2022"
where cl
# Should display: C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\[VERSION]\bin\Hostx64\x64\cl.exe
```

---

### Git

**Download:** [Git for Windows](https://git-scm.com/download/win)

**Verification:**
```powershell
git --version
# Should display installed Git version
```

---

### Qt 6.6+

**Download:** [Qt Online Installer](https://www.qt.io/download-qt-installer)

**Installation Steps:**
1. Run Qt Online Installer
2. Create Qt account (if needed)
3. Select **Qt 6.10.1** (or latest 6.6+)
4. Select the following components:

**Required Components:**
- **MSVC 2022 64-bit** - Qt libraries compiled for MSVC
- **CMake 3.30.5** (or latest) - Build system generator
- **Ninja 1.12.1** (or latest) - Fast build tool
- **Qt Creator 18.0.1** (or latest) - Optional IDE

**Optional Additional Libraries:**
- **Active Qt** - Required for Excel COM automation
- **Qt Network Authorization** - OAuth support for web services
- **Qt Serial Port** - Hardware serial communication
- **Qt Serial Bus** - Industrial protocols (CAN, Modbus)
- **Qt WebSockets** - Real-time bidirectional communication

**Uncheck** all components not listed above.

5. Complete installation (note installation path, typically `C:\Qt\6.10.1\msvc2022_64`)

**Verification:**
```powershell
qmake --version
# Should display Qt version information
```

---

### vcpkg (C++ Package Manager)

**Installation:**
```powershell
# Navigate to C:\ drive
cd C:\

# Clone vcpkg repository
git clone https://github.com/Microsoft/vcpkg.git

# Navigate into vcpkg directory
cd vcpkg

# Bootstrap vcpkg
.\bootstrap-vcpkg.bat

# Add C:\vcpkg to PATH environment variable (see Environment Variables section)
```

**Verification:**
```powershell
vcpkg version
# Should display vcpkg version information
```

---

### Google Test (via vcpkg)

**Installation:**
```powershell
# Install Google Test for x64 Windows
vcpkg install gtest:x64-windows

# Integrate with CMake (system-wide)
vcpkg integrate install
```

**Important:** After running `vcpkg integrate install`, the command will display the vcpkg toolchain file path. **Record this path** - you'll need it for `CMakePresets.json`.

Example output:
```
CMake projects should use: "-DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake"
```

---

## Environment Variables

Configure these **User environment variables** (not System variables):

### Qt and Build Tools Paths

| Variable            | Value                                          | Purpose                        |
| ------------------- | ---------------------------------------------- | ------------------------------ |
| `CMAKE_PREFIX_PATH` | `C:\Qt\6.10.1\msvc2022_64`                     | Qt installation for CMake      |
| `Path`              | `C:\Qt\6.10.1\msvc2022_64\bin`                 | Qt binaries                    |
| `Path`              | `C:\Qt\Tools\CMake_64\bin`                     | CMake                          |
| `Path`              | `C:\Qt\Tools\Ninja`                            | Ninja build system             |
| `Path`              | `C:\vcpkg`                                     | vcpkg package manager          |

### MSVC Compiler Paths

| Variable            | Value                                                                                                          | Purpose                          |
| ------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| `VCToolsInstallDir` | `C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.42.34433\`                   | MSVC tools location              |
| `VSINSTALLDIR`      | `C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\`                                             | Visual Studio installation       |
| `Path`              | `%VCToolsInstallDir%bin\Hostx64\x64`                                                                           | MSVC compiler binaries           |
| `Path`              | `C:\Program Files (x86)\Windows Kits\10\bin\10.0.22621.0\x64`                                                 | Windows SDK tools (rc.exe)       |

### MSVC Include and Library Paths

| Variable  | Value                                                                                                                                                                                                                           | Purpose                          |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| `INCLUDE` | `%VCToolsInstallDir%include;C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\um;C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\shared;C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\ucrt` | MSVC & Windows SDK include paths |
| `LIB`     | `%VCToolsInstallDir%lib\x64;C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22621.0\um\x64;C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22621.0\ucrt\x64`                                                                    | MSVC & Windows SDK library paths |

**Important Notes:**
- Adjust Qt version numbers (e.g., `6.10.1`) to match your installation
- Adjust MSVC version (e.g., `14.42.34433`) to match your installation
  - Find your version by running `where cl` in Developer Command Prompt
- Adjust Windows SDK version (e.g., `10.0.22621.0`) to match your installation
- **Restart terminal/VS Code** after setting environment variables

### How to Set Environment Variables

1. Press `Win + X` → Select "System"
2. Click "Advanced system settings"
3. Click "Environment Variables"
4. Under "User variables", click "New" or "Edit"
5. Add variable name and value
6. Click "OK" to save

For `Path` variable:
1. Select `Path` → Click "Edit"
2. Click "New"
3. Add the path
4. Click "OK"

---

## VS Code Setup

### Install VS Code

**Download:** [Visual Studio Code](https://code.visualstudio.com/)

### Install Required Extensions

**Method 1: Via Command Line**
```powershell
code --install-extension ms-vscode.cpptools
code --install-extension ms-vscode.cmake-tools
code --install-extension TheQtCompany.qt-tools
code --install-extension github.copilot  # Optional, if licensed
```

**Method 2: Via VS Code UI**
1. Press `Ctrl+Shift+X` to open Extensions view
2. Search for and install:
   - **C/C++** (Microsoft)
   - **CMake Tools** (Microsoft)
   - **Qt Extension Pack** (The Qt Company)
   - **GitHub Copilot** (optional, requires license)

### Configure CMake Kit

1. Open your project folder in VS Code
2. Press `Ctrl+Shift+P` → Type "CMake: Select a Kit"
3. Choose **"Visual Studio Build Tools 2022 Release - amd64"**
4. If not listed, click "Scan for kits" and retry

---

## Project Structure

Standard Qt project structure:

```
project-name/
├── CMakeLists.txt              # Top-level build configuration
├── CMakePresets.json           # CMake presets for debug/release
├── README.md                   # Project overview
├── REQUIREMENTS.md             # Technical specifications
├── DEVELOPMENT_SETUP.md        # This file
├── CONTRIBUTING.md             # Contribution guidelines
├── .gitignore                  # Git ignore rules
├── src/                        # Source code
│   └── main.cpp
├── tests/                      # Unit tests
│   └── test_main.cpp
├── docs/                       # Additional documentation
└── build/                      # Build artifacts (generated, not in git)
    ├── debug/
    └── release/
```

---

## CMake Configuration

### CMakePresets.json

Create `CMakePresets.json` in your project root:

```json
{
  "version": 3,
  "configurePresets": [
    {
      "name": "windows-debug",
      "displayName": "Windows Debug",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/debug",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug",
        "CMAKE_PREFIX_PATH": "C:/Qt/6.10.1/msvc2022_64",
        "CMAKE_TOOLCHAIN_FILE": "C:/vcpkg/scripts/buildsystems/vcpkg.cmake",
        "CMAKE_EXPORT_COMPILE_COMMANDS": "ON"
      },
      "environment": {
        "PATH": "C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/VC/Tools/MSVC/14.42.34433/bin/Hostx64/x64;$penv{PATH}"
      }
    },
    {
      "name": "windows-release",
      "displayName": "Windows Release",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/release",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release",
        "CMAKE_PREFIX_PATH": "C:/Qt/6.10.1/msvc2022_64",
        "CMAKE_TOOLCHAIN_FILE": "C:/vcpkg/scripts/buildsystems/vcpkg.cmake"
      },
      "environment": {
        "PATH": "C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/VC/Tools/MSVC/14.42.34433/bin/Hostx64/x64;$penv{PATH}"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "windows-debug",
      "configurePreset": "windows-debug"
    },
    {
      "name": "windows-release",
      "configurePreset": "windows-release"
    }
  ]
}
```

**Required Customisations:**
- **`CMAKE_PREFIX_PATH`**: Your Qt installation path
- **`CMAKE_TOOLCHAIN_FILE`**: Your vcpkg toolchain file path (from `vcpkg integrate install`)
- **`environment.PATH`**: Path to MSVC compiler binaries
  - Find your MSVC version: Open Developer Command Prompt → Run `where cl`
  - Format: `C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/VC/Tools/MSVC/[VERSION]/bin/Hostx64/x64`
  - Replace `[VERSION]` with your actual version (e.g., `14.42.34433`)

**Note:** Use forward slashes (`/`) in JSON paths, even on Windows.

---

### CMakeLists.txt

Create top-level `CMakeLists.txt` in your project root:

```cmake
cmake_minimum_required(VERSION 3.20)

# TODO: Replace 'ProjectName' with your actual project name
project(ProjectName VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Find Qt packages
# Add more components as needed for your project
find_package(Qt6 REQUIRED COMPONENTS
    Core
    # Gui              # For GUI applications
    # Widgets          # For desktop applications
    # Network          # For network functionality
    # Sql              # For database access
    # AxContainer      # For COM/Excel automation (Windows only)
)

# Find Google Test
find_package(GTest REQUIRED)

# Application executable
add_executable(${PROJECT_NAME}
    src/main.cpp
    # Add your source files here:
    # src/myclass.cpp
    # src/myclass.h
)

target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt6::Core
    # Link additional Qt modules here:
    # Qt6::Gui
    # Qt6::Widgets
    # Qt6::Network
    # Qt6::AxContainer
)

# Tests
enable_testing()

add_executable(${PROJECT_NAME}_tests
    tests/test_main.cpp
    # Add your test files here:
    # tests/test_myclass.cpp
)

target_link_libraries(${PROJECT_NAME}_tests PRIVATE
    Qt6::Core
    GTest::gtest
    GTest::gtest_main
    # Link additional Qt modules here
)

add_test(NAME ${PROJECT_NAME}_tests COMMAND ${PROJECT_NAME}_tests)
```

**Required Customisations:**
- Replace `ProjectName` with your actual project name
- Add required Qt modules in `find_package(Qt6 REQUIRED COMPONENTS ...)`
- Add your source files in `add_executable(${PROJECT_NAME} ...)`
- Link additional Qt modules in both `target_link_libraries` sections
- Add your test files in `add_executable(${PROJECT_NAME}_tests ...)`

---

## Template Files

**Note:** All template files below are available in the ready-made template repository: https://github.com/hardpath/qt-project-template.git

### src/main.cpp

Basic Qt console application:

```cpp
#include <QCoreApplication>
#include <QTextStream>

int main(int argc, char *argv[])
{
    QCoreApplication app(argc, argv);
    
    QTextStream out(stdout);
    out << "Hello Qt!" << Qt::endl;
    out << "Qt version: " << QT_VERSION_STR << Qt::endl;
    
    return 0;
}
```

**For GUI applications**, replace `QCoreApplication` with `QApplication` and link `Qt6::Widgets`.

---

### tests/test_main.cpp

Basic Google Test structure with Qt integration:

```cpp
#include <gtest/gtest.h>
#include <QCoreApplication>
#include <QString>

TEST(BasicTest, Example) {
    EXPECT_EQ(1 + 1, 2);
}

TEST(QtTest, QStringCreation) {
    QString str = "Hello Qt";
    EXPECT_EQ(str.length(), 8);
}

int main(int argc, char **argv) {
    QCoreApplication app(argc, argv);
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

**Key Points:**
- Always initialise `QCoreApplication` before running tests (required for Qt's event loop)
- Use `::testing::InitGoogleTest(&argc, argv)` to initialise Google Test
- Return `RUN_ALL_TESTS()` to execute all test cases

---

## Build and Run

### Configure and Build via Command Line

```powershell
# Configure Debug build
cmake --preset windows-debug

# Build Debug
cmake --build --preset windows-debug

# Configure Release build
cmake --preset windows-release

# Build Release
cmake --build --preset windows-release
```

### Build via VS Code

**Configure:**
1. Press `Ctrl+Shift+P`
2. Type "CMake: Configure"
3. Press Enter

**Build:**
1. Press `F7`
   
   OR
   
2. Press `Ctrl+Shift+P`
3. Type "CMake: Build"
4. Press Enter

**Select Build Type:**
- Press `Ctrl+Shift+P` → "CMake: Select Configure Preset"
- Choose "Windows Debug" or "Windows Release"

### Run Application

```powershell
# Debug build
.\build\debug\ProjectName.exe

# Release build
.\build\release\ProjectName.exe
```

### Run Tests

```powershell
# Run all tests with detailed output on failure
ctest --test-dir build/debug --output-on-failure

# Run tests in Release mode
ctest --test-dir build/release --output-on-failure

# Run specific test suite
.\build\debug\ProjectName_tests.exe --gtest_filter=TestSuiteName.*

# List all available tests
.\build\debug\ProjectName_tests.exe --gtest_list_tests
```

---

## Testing Framework

### Google Test Basics

**Test Structure:**
```cpp
TEST(TestSuiteName, TestName) {
    // Arrange
    int expected = 5;
    
    // Act
    int actual = 2 + 3;
    
    // Assert
    EXPECT_EQ(expected, actual);
}
```

**Common Assertions:**
```cpp
EXPECT_EQ(a, b);      // a == b
EXPECT_NE(a, b);      // a != b
EXPECT_LT(a, b);      // a < b
EXPECT_LE(a, b);      // a <= b
EXPECT_GT(a, b);      // a > b
EXPECT_GE(a, b);      // a >= b
EXPECT_TRUE(condition);
EXPECT_FALSE(condition);
EXPECT_STREQ(str1, str2);  // C-string equality
```

**Qt-Specific Testing:**
```cpp
TEST(QtTest, QStringComparison) {
    QString str1 = "Hello";
    QString str2 = "Hello";
    EXPECT_EQ(str1, str2);
}

TEST(QtTest, QListOperations) {
    QList<int> list = {1, 2, 3};
    EXPECT_EQ(list.size(), 3);
    EXPECT_TRUE(list.contains(2));
}
```

### Test Fixtures

For tests that share common setup:

```cpp
class MyTestFixture : public ::testing::Test {
protected:
    void SetUp() override {
        // Runs before each test
        testObject = new MyClass();
    }
    
    void TearDown() override {
        // Runs after each test
        delete testObject;
    }
    
    MyClass* testObject;
};

TEST_F(MyTestFixture, TestMethod1) {
    EXPECT_TRUE(testObject->someMethod());
}

TEST_F(MyTestFixture, TestMethod2) {
    EXPECT_EQ(testObject->getValue(), 42);
}
```

---

## Troubleshooting

### CMake can't find Qt

**Symptom:**
```
CMake Error: Could not find Qt6
```

**Solutions:**
1. Verify `CMAKE_PREFIX_PATH` environment variable:
   ```powershell
   echo $env:CMAKE_PREFIX_PATH
   # Should show: C:\Qt\6.10.1\msvc2022_64
   ```
2. Check path in `CMakePresets.json` matches your Qt installation
3. Restart VS Code after setting environment variables

---

### CMake can't find Google Test

**Symptom:**
```
CMake Error: Could not find GTest
```

**Solutions:**
1. Verify vcpkg integration:
   ```powershell
   vcpkg integrate install
   ```
2. Check `CMAKE_TOOLCHAIN_FILE` path in `CMakePresets.json`
3. Verify Google Test is installed:
   ```powershell
   vcpkg list | Select-String "gtest"
   # Should show: gtest:x64-windows
   ```

---

### Compiler not found

**Symptom:**
```
CMake Error: Could not find MSVC compiler
```

**Solutions:**
1. Verify MSVC installation:
   ```powershell
   # Open "Developer Command Prompt for VS 2022"
   where cl
   # Should display cl.exe path
   ```
2. Check environment variables `VCToolsInstallDir` and `VSINSTALLDIR`
3. Select correct CMake Kit in VS Code:
   - `Ctrl+Shift+P` → "CMake: Select a Kit"
   - Choose "Visual Studio Build Tools 2022"

---

### IntelliSense not working (red squiggles)

**Symptom:**
- Red squiggles under Qt classes
- `#include <QCoreApplication>` shows errors

**Solutions:**
1. Ensure `CMAKE_EXPORT_COMPILE_COMMANDS` is `ON` in `CMakePresets.json`
2. Configure CMake: `Ctrl+Shift+P` → "CMake: Configure"
3. Reload VS Code: `Ctrl+Shift+P` → "Developer: Reload Window"
4. Check C++ extension configuration:
   - Open `.vscode/c_cpp_properties.json`
   - Verify `compileCommands` points to `${workspaceFolder}/build/debug/compile_commands.json`

---

### Build succeeds but executable crashes

**Symptom:**
- Build completes without errors
- Application crashes immediately on launch

**Solutions:**
1. Ensure Qt DLLs are in PATH or copy to executable directory
2. For Debug builds, run from `build/debug/` directory
3. Check Qt dependencies:
   ```powershell
   # In build/debug directory
   windeployqt ProjectName.exe
   ```

---

### Tests fail to run

**Symptom:**
```
Test project build/debug
No tests were found!!!
```

**Solutions:**
1. Verify `enable_testing()` is in `CMakeLists.txt`
2. Ensure `add_test()` is called for test executable
3. Rebuild project: `cmake --build --preset windows-debug`
4. Check test executable exists: `.\build\debug\ProjectName_tests.exe`

---

## Resources

### Official Documentation

- [Qt 6 Documentation](https://doc.qt.io/qt-6/) - Complete Qt framework reference
- [Qt for Beginners](https://doc.qt.io/qt-6/qt-intro.html) - Introduction to Qt
- [CMake Documentation](https://cmake.org/documentation/) - CMake reference
- [CMake Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html) - Step-by-step CMake guide
- [Google Test Primer](https://google.github.io/googletest/primer.html) - Google Test basics
- [Google Test Advanced](https://google.github.io/googletest/advanced.html) - Advanced testing features

### Tools Documentation

- [Qt VS Code Extension](https://doc.qt.io/vscodeext/) - VS Code integration guide
- [vcpkg Documentation](https://vcpkg.io/en/docs/) - Package manager reference
- [Ninja Build](https://ninja-build.org/manual.html) - Build system documentation

### Qt Modules

- [Qt Core](https://doc.qt.io/qt-6/qtcore-index.html) - Core non-GUI functionality
- [Qt Widgets](https://doc.qt.io/qt-6/qtwidgets-index.html) - Desktop UI widgets
- [Qt Network](https://doc.qt.io/qt-6/qtnetwork-index.html) - Network programming
- [ActiveQt](https://doc.qt.io/qt-6/activeqt-index.html) - COM/OLE automation (Excel)

### Community

- [Qt Forum](https://forum.qt.io/) - Community support
- [Stack Overflow - Qt](https://stackoverflow.com/questions/tagged/qt) - Q&A
- [r/QtFramework](https://www.reddit.com/r/QtFramework/) - Reddit community
- **Companion:** `SETUP_CHECKLIST.md`
