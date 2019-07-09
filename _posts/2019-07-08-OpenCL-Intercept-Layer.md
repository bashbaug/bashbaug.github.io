---
layout: post
title:  "OpenCL Intercept Layer"
date:   2019-07-08
categories: [OpenCL]
tags: [OpenCL, Linux, Debugging, Performance Analysis, CLIntercept]
---

# Intercept Layer for OpenCL Applications

This post is a brief introduction to the Intercept Layer for OpenCL Applications (informally, "CLIntercept").
I suspect I'll do a fair amount of writing about the CLIntercept, its features, and how I use it, so it seems worthwhile to describe what it is and how it works.

The Intercept Layer for OpenCL Applications is a tool we developed internally within Intel when we first started working on OpenCL in 2009 (!).
The basic idea was to shift various functionality for debugging and performance analysis out of drivers as much as possible and into a middleware layer that could be selectively enabled and disabled, and that could be updated asynchronously from the drivers.
When we open-sourced our GPU driver early in 2019, we also open-sourced the Intercept Layer for OpenCL Applications.
It can now be found on github, here:

[https://github.com/intel/opencl-intercept-layer](https://github.com/intel/opencl-intercept-layer)

## How it Works

The Intercept Layer for OpenCL Applications works by acting as an [OpenCL ICD loader](https://github.com/KhronosGroup/OpenCL-ICD-Loader) (usually) or as an OpenCL implementation (rarely) and causing itself to be loaded when an application intends to load the "real" OpenCL ICD loader or OpenCL implementation.
As part of its implementation, the Intercept Layer loads the "real" OpenCL ICD loader or OpenCL implementation and dynamically constructs a dispatch table of pointers to the "real" OpenCL APIs.
Then, when the application makes an OpenCL API call, the Intercept Layer can choose to pass it along with or without changes.
It can even omit API calls entirely!

![Intercept Layer Architecture](/images/OpenCL-InterceptLayer.png)

Things it can do include:

* Logging, Tracing, or Timing API Calls
* Parameter Modification (e.g. Forcing In-order Queues)
* Return Value Modification (e.g. Device Queries)
* Insert API Calls (e.g. Flush After Enqueue)
* Remove API Calls (e.g. Null Enqueue)
* Much, much more!

## For More Information

I wrote a paper and gave a talk about the Intercept Layer for OpenCL Applications at [IWOCL](https://www.iwocl.org/) in 2018.

My paper can be found [here](https://dl.acm.org/citation.cfm?doid=3204919.3204933).

The slides from my talk can be found [here](https://www.iwocl.org/wp-content/uploads/iwocl-2018-intel-debug-analyze-intercept-layer.pdf).

## Notes

* We use the Intercept Layer for OpenCL Applications regularly at Intel, on both Windows and Linux.
* The Intercept Layer for OpenCL Applications also supports Android and OSX, but we don't use these configurations as frequently.
* The Intercept Layer for OpenCL Applications is designed to work with any OpenCL implementation, not just those from Intel.  We use it for competitive analysis on non-Intel implementations as well!
* With very few exceptions (e.g. internal features still in development), we use the public Intercept Layer for OpenCL Applications from github.  Any internal forks are short-lived and deleted once the changes are upstreamed.
* Please feel free to report bugs and file feature requests via github issues.
* Community contributions are welcome and encouraged.
* If you find the Intercept Layer for OpenCL Applications useful please drop me a line - I'd love to hear from you!