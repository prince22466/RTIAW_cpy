# RTIAW (Raytracing In A Weekend) - Project Review

## Overview
This project is a C++20(was 17) ray tracer with an interactive UI built on the Walnut framework. It renders scenes from the "Ray Tracing in One Weekend" style setup and exposes controls for scene selection, samples per pixel, and max bounces.

## Project Structure
```
.
├─ CMakeLists.txt
├─ CMakeSettings.json
├─ cmake/
│  └─ FetchDependencies.cmake
├─ src/
│  ├─ CMakeLists.txt
│  ├─ main.cpp
│  └─ App/
│     ├─ CMakeLists.txt
│     ├─ ApplicationLayer.h/.cpp
│     └─ Renderer/
│        ├─ CMakeLists.txt
│        ├─ Camera.h/.cpp
│        ├─ Renderer.h/.cpp
│        ├─ Scenes.cpp
│        ├─ Ray.h/.cpp
│        ├─ HittableObject*.h/.cpp
│        ├─ HitRecord.h
│        ├─ ScatteringRecord.h
│        ├─ ThreadPool.h
│        ├─ Utils.h
│        ├─ Materials/
│        │  ├─ Lambertian.h/.cpp
│        │  ├─ Metal.h/.cpp
│        │  ├─ Dielectric.h/.cpp
│        │  └─ Materials.h
│        └─ Shapes/
│           ├─ Sphere.h/.cpp
│           ├─ Plane.h/.cpp
│           ├─ Rectangle.h/.cpp
│           ├─ Parallelogram.h/.cpp
│           └─ Shapes.h
└─ vendor/
   └─ Walnut/ (git submodule)
```

## Components and Responsibilities
- **Application entry (`src/main.cpp`)**
  - Creates the Walnut application, sets window size/title, and registers the main UI layer.
- **UI layer (`src/App/ApplicationLayer.*`)**
  - Manages ImGui windows, renders the current image buffer, and exposes controls for scenes, samples, bounces, and render control (start/abort).
- **Renderer core (`src/App/Renderer/Renderer.*`)**
  - Owns the render state, image buffer, camera, multithreading, and ray tracing loop.
  - Splits the image into tiles for threaded rendering and writes RGBA output to a byte buffer.
- **Scenes (`src/App/Renderer/Scenes.cpp`)**
  - Builds scene geometry and materials for selectable presets (DefaultScene, ThreeSpheres, TestScene).
- **Geometry (`src/App/Renderer/Shapes/*`)**
  - Hittable primitives such as spheres, planes, rectangles, and parallelograms.
- **Materials (`src/App/Renderer/Materials/*`)**
  - Surface scattering models: Lambertian (diffuse), Metal (reflective), Dielectric (glass).
- **Ray and hit records (`Ray.*`, `HitRecord.h`, `HittableObject*.*`)**
  - Core ray and intersection structures, and a list to aggregate hittable objects.
- **Camera (`Camera.*`)**
  - Produces rays for a pinhole/defocus camera setup.
- **Utilities (`Utils.h`, `ThreadPool.h`)**
  - Math helpers, random sampling helpers, and a thread pool for parallel rendering.

## Language and Standard
- **Programming language:** C++
- **Language standard:** C++20 (`CMAKE_CXX_STANDARD 20`)

## Dependencies / Packages
From `cmake/FetchDependencies.cmake`:
- **magic_enum** v0.7.3
- **fmt** 7.1.3 (header-only in this project)
- **spdlog** v1.8.5 (header-only in this project)

From CMake targets and includes:
- **GLM** (version not specified; used for math types and utilities)
- **Walnut** (git submodule at `vendor/Walnut`, branch `glfw_fix`)
- **ImGui** (linked via `imgui` target; typically provided by Walnut)

## Compiler / Build System
- **Build system:** CMake (minimum 3.13)
- **Generator:** Ninja (configured in `CMakeSettings.json`)
- **Compiler:** MSVC x64 environment (`msvc_x64_x64` in `CMakeSettings.json`)

## Running Environment Requirements
- Windows environment implied by `CMakeSettings.json` (MSVC x64 toolchain).
- A C++20-capable compiler and CMake >= 3.13.
- Ninja (or another CMake generator if you adjust settings).
- Git submodules must be initialized to populate `vendor/Walnut`:
  - `git submodule update --init --recursive`
- Network access is required on first configure to fetch `fmt`, `spdlog`, and `magic_enum` via CMake FetchContent.

## Build Target
- The executable target is `demo` (defined in `src/CMakeLists.txt`).
