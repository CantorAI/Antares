# Antares

**An AI-native cross-platform application runtime combining Web UI with Python, XLang, and C++ for desktop and mobile.**

Antares explores a different application architecture for the GenAI era: use modern Web technologies for the user interface while making Python, XLang, and C++ the native application runtime.

Traditional Web-based desktop frameworks such as Electron combine Chromium with Node.js. This model made sense when JavaScript was the natural common language between Web UI and application logic.

The application landscape is changing with generative AI. Python has become a primary language for AI models, agents, data processing, automation, and LLM-generated application logic. Antares is designed around this new environment.

## Architecture

Antares separates the **UI engine** from the **application runtime**.

```text
                  Web UI
          HTML / CSS / JavaScript
          React / Vue / etc.
                     │
                     │
              Antares Bridge
                     │
                     ▼
               XLang Runtime
              /      |       \
             /       |        \
        Python      XLang      C++
           │                    │
      AI / Agents          Native / GPU
           │                    │
           └─────────┬──────────┘
                     │
              Platform APIs
```

The Web engine is responsible for presentation and interaction. It is not the application's backend runtime.

Python, XLang, and C++ provide application logic, AI integration, native capabilities, and high-performance system functionality.

## Desktop and Mobile

Antares uses the most appropriate Web rendering engine for each platform rather than requiring the same browser binary everywhere.

```text
                         Antares
                            │
             ┌──────────────┴──────────────┐
             │                             │
          Desktop                        Mobile
             │                             │
     Chromium / CEF                 Native OS WebView
             │                      /              \
             │              Android WebView      WKWebView
             │
      Windows / macOS / Linux       Android / iOS
```

On desktop, Chromium provides a consistent modern Web rendering environment.

On mobile, Antares uses the WebView supplied by the operating system instead of packaging another complete Chromium runtime. This keeps mobile applications smaller while preserving the same Antares application model.

The application should not need to know which Web engine is underneath it.

## Runtime

The core runtime is native C++ with XLang providing the common object and interoperability layer.

```text
                 XLang Runtime
                      │
          ┌───────────┼───────────┐
          │           │           │
        Python       XLang       C++
          │                       │
      AI / Apps              Native System
```

Python is a first-class application language rather than an external service.

C++ provides the native foundation for platform integration, performance-sensitive operations, GPU access, multimedia, networking, and other system capabilities.

XLang connects these environments through a common runtime and object model.

Python is therefore not intended to replace C++ as the system layer. Instead, Python and C++ complement each other:

```text
Python
  ├── AI and ML
  ├── LLM applications
  ├── Agents
  ├── Automation
  ├── Data processing
  └── Generated application logic

C++
  ├── Native OS integration
  ├── Window management
  ├── Filesystem
  ├── Networking
  ├── Audio / Video
  ├── GPU
  └── Performance-critical components
```

## Web UI ↔ Native Objects

Antares is not intended to require an HTTP server between the Web UI and application logic.

Instead:

```text
JavaScript
     │
     ▼
Antares / XLang Object Bridge
     │
     ├──────── Python
     │
     ├──────── XLang
     │
     └──────── C++
```

A Python object or function can be exported into the runtime:

```python
@xlang.export
async def summarize(text):
    return await model.generate(text)
```

and exposed naturally to the Web UI:

```javascript
const result = await xlang.summarize(text);
```

The goal is to avoid manually creating REST endpoints, local HTTP servers, or application-specific IPC protocols for ordinary UI-to-runtime communication.

## Objects, Events and Async

The bridge should support more than simple function calls.

Native objects can be represented in JavaScript:

```javascript
const camera = await xlang.device.camera();

camera.on("frame", frame => {
    // process frame
});

const image = await camera.capture();
```

The same object may be implemented in Python, XLang, or C++.

Antares should provide first-class support for:

- asynchronous calls
- promises/futures
- events
- callbacks
- object lifetime management
- native object handles
- exceptions
- structured values

This allows the Web UI to interact with the native runtime as an object system rather than as a collection of HTTP endpoints.

## High-Performance Data

Serialization is appropriate for small application values but not for large AI and multimedia data.

Antares should support two data paths:

```text
Small objects
    │
    └── structured serialization

Large objects
    │
    └── shared/native buffers
```

Large objects may include:

- images
- video frames
- audio
- tensors
- model data
- GPU resources

Instead of:

```text
C++ buffer
   ↓
copy
   ↓
Base64
   ↓
JSON
   ↓
JavaScript
```

Antares should allow:

```text
Native Buffer
      │
      ▼
 XLang Handle
   /       \
Python      JavaScript
   \       /
    Native/GPU
```

This is particularly important for AI, robotics, vision, and multimedia applications.

## Designed for GenAI

Antares is motivated in part by a change in how applications can be created.

An LLM can naturally generate:

```text
Application
   │
   ├── Web UI
   │     HTML / CSS / JavaScript
   │
   └── Application Logic
         Python / XLang
```

Python is already the dominant environment around many AI models, agent frameworks, data libraries, and AI development tools.

Instead of forcing generated application logic through Node.js, an Antares application can execute Python directly inside its native runtime.

```text
                 LLM
                  │
            generates code
              /        \
             /          \
         Web UI        Python
            │             │
            └──────┬──────┘
                   │
             Antares Runtime
```

Generated Python code can expose functions and objects to the UI through the same bridge used by handwritten application code.

This makes dynamically generated application functionality a natural part of the architecture rather than an additional service layer.

## One Application Model

The long-term goal is for application code to see one consistent environment:

```text
                         Application
                              │
                       Antares Runtime
                              │
             ┌────────────────┼────────────────┐
             │                │                │
          Desktop           Mobile          Edge
             │                │                │
      Win/Mac/Linux      Android/iOS      Native C++
```

Platform-specific implementation remains underneath the runtime.

An application should be able to use capabilities such as:

```text
filesystem
clipboard
camera
audio
network
notifications
database
AI models
GPU
devices
```

through a common object model.

## Relationship to Electron

Conceptually:

```text
Electron

    Chromium
        │
      Node.js
        │
    Native APIs
```

Antares takes a different approach:

```text
Antares

      Web Engine
          │
     XLang Bridge
          │
     XLang Runtime
       /       \
   Python      C++
       \       /
      Native APIs
```

Electron made JavaScript the application runtime.

Antares makes the native runtime language-independent, with Python, XLang, and C++ as first-class participants.

The objective is not simply to create "Electron with Python."

The broader goal is an application runtime designed around native performance, Web UI, AI-native development, and code increasingly written or generated by AI.

## Vision

Antares aims to make this architecture simple:

**Web technologies for UI. Python for AI and application logic. C++ for native performance. XLang connecting them into one runtime.**

```text
                Antares

        Web UI / JavaScript
                │
                ▼
         XLang Object Model
          /      │       \
         /       │        \
     Python     XLang      C++
        │                   │
       AI               Native/GPU
        │                   │
        └─────────┬─────────┘
                  │
        Desktop / Mobile / Edge
```

The result is a cross-platform application model intended not only for today's applications, but for software increasingly created, extended, and operated by generative AI.
