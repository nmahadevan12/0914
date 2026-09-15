# 0914 — STM32 CMake build fix (VS Code)

Copy these files if `Cmd+Shift+B` fails with:

```text
Executing task: CMake: build
build failed.
The terminal process failed to launch (exit code: -1)
```

That usually means VS Code is pointed at the wrong folder, or `cmake` / `ninja` / `arm-none-eabi-gcc` aren’t on PATH.

## What to copy

From this repo, copy the whole `.vscode` folder into your project workspace (the folder you open in VS Code — the one that **contains** your CubeMX project folder, e.g. `test_0914`).

```text
your_project/
├── .vscode/
│   ├── settings.json
│   └── tasks.json
└── test_0914/          ← CubeMX-generated CMake project
    ├── CMakeLists.txt
    ├── CMakePresets.json
    └── ...
```

If your CubeMX folder is **not** named `test_0914`, edit both files and replace `test_0914` with your folder name.

## Prerequisites (macOS)

```bash
brew install cmake ninja arm-none-eabi-gcc
```

Also useful:
- [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html)
- VS Code extension: **STM32CubeIDE for Visual Studio Code** (STMicroelectronics)

## After copying

1. Open the **parent** folder in VS Code (the one with `.vscode` + your CubeMX project folder).
2. `Cmd+Shift+P` → **Developer: Reload Window**
3. `Cmd+Shift+B`

## Portable `.vscode` (no personal paths)

Use these instead of copying absolute `/Users/<name>/...` paths.

### `.vscode/settings.json`

```json
{
  "cmake.sourceDirectory": "${workspaceFolder}/test_0914",
  "cmake.useCMakePresets": "always",
  "cmake.configureOnOpen": true,
  "cmake.preferredGenerators": ["Ninja"],
  "terminal.integrated.inheritEnv": true
}
```

### `.vscode/tasks.json`

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "CMake: build Debug",
      "type": "shell",
      "command": "cmake --build --preset Debug",
      "options": {
        "cwd": "${workspaceFolder}/test_0914",
        "env": {
          "PATH": "/opt/homebrew/bin:/usr/bin:/bin:${env:PATH}"
        }
      },
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": ["$gcc"]
    },
    {
      "label": "CMake: configure Debug",
      "type": "shell",
      "command": "cmake --preset Debug",
      "options": {
        "cwd": "${workspaceFolder}/test_0914",
        "env": {
          "PATH": "/opt/homebrew/bin:/usr/bin:/bin:${env:PATH}"
        }
      },
      "problemMatcher": []
    }
  ]
}
```

> Intel Mac Homebrew path is often `/usr/local/bin` instead of `/opt/homebrew/bin`.

## Build from terminal

```bash
cd test_0914
cmake --preset Debug
cmake --build --preset Debug
```

Firmware output:

```text
test_0914/build/Debug/*.elf
```