# Gradient

Image and shader editor written in C++20, using OpenGL 3.3, GLFW, and Dear ImGui.

## Requirements

- Windows, Linux, or macOS (64-bit).
- CMake 3.21 or later, Git, and a C++20 compiler.
- vcpkg. The repository manifest installs GLFW, GLEW, GLM, stb, and Dear ImGui with its GLFW/OpenGL3 backends.
- A graphics driver that supports OpenGL 3.3.
- Docker Engine or Docker Desktop with Linux containers, for the container build.

## Set Up vcpkg

Install and bootstrap vcpkg once. On Windows, run in PowerShell:

```powershell
git clone https://github.com/microsoft/vcpkg "$env:USERPROFILE\vcpkg"
& "$env:USERPROFILE\vcpkg\bootstrap-vcpkg.bat"
$env:VCPKG_ROOT = "$env:USERPROFILE\vcpkg"
```

On Linux or macOS, run in a shell:

```sh
git clone https://github.com/microsoft/vcpkg "$HOME/vcpkg"
"$HOME/vcpkg/bootstrap-vcpkg.sh"
export VCPKG_ROOT="$HOME/vcpkg"
```

Set `VCPKG_ROOT` in each new terminal, or add it to your environment configuration.

## Native Builds

### Windows

Install Visual Studio 2022 or later with the **Desktop development with C++** workload and a Windows SDK. From PowerShell at the repository root:

```powershell
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE="$env:VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake" -DVCPKG_TARGET_TRIPLET=x64-windows
cmake --build build --config Release
.\build\Release\Gradient.exe
```

### Linux

On Debian or Ubuntu, install the compiler, CMake, and OpenGL/GLFW system development libraries:

```sh
sudo apt-get update
sudo apt-get install -y build-essential cmake git curl zip unzip tar pkg-config libx11-dev libxrandr-dev libxinerama-dev libxcursor-dev libxi-dev libxxf86vm-dev libgl1-mesa-dev
```

From the repository root, configure, build, and run:

```sh
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE="$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake" -DVCPKG_TARGET_TRIPLET=x64-linux -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel
./build/Gradient
```

### macOS

Install the Xcode Command Line Tools (`xcode-select --install`), CMake, and Git. Use `arm64-osx` on Apple Silicon or `x64-osx` on Intel Macs:

```sh
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE="$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake" -DVCPKG_TARGET_TRIPLET=arm64-osx -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel
./build/Gradient
```

For an Intel Mac, replace `arm64-osx` with `x64-osx`. The first configure downloads and builds the vcpkg dependencies and may take several minutes. CMake copies `images` beside the executable so the sample image can be loaded at runtime.

## Docker

Build the Linux container image from the repository root:

```sh
docker build -t gradient .
```

The image contains the compiled app and its runtime libraries. Since Gradient opens a GLFW/OpenGL window, a display server is required to run it interactively. On a Linux host using X11, allow the container's local root user to access the display, then run:

```sh
xhost +SI:localuser:root
docker run --rm -e DISPLAY="$DISPLAY" -v /tmp/.X11-unix:/tmp/.X11-unix:ro gradient
xhost -SI:localuser:root
```

This display-forwarding example is for Linux with X11. On Windows or macOS, Docker can build the image, but displaying the GUI requires a separately configured X server; a native build is usually simpler for interactive use.

## Open in Visual Studio

Open the repository folder as a CMake folder in Visual Studio. It will detect `CMakeLists.txt`; set `CMAKE_TOOLCHAIN_FILE` to `$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake` in the CMake configuration, then select the desired x64 configuration and build or run the `Gradient` target. The existing `Gradient.slnx` solution and `.vcxproj` project are legacy files and do not configure the vcpkg dependencies on their own.

## Project Structure

- `main.cpp`: GLFW/OpenGL initialization, ImGui interface, and shader.
- `images/femto.jpg`: image loaded by the application.
- `vcpkg.json`: dependencies and vcpkg registry baseline.
- `CMakeLists.txt`: build and linking configuration.
- `Dockerfile`: Linux container build.
