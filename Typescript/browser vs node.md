Node is an open source and cross-platform runtime environment built on Chrom's V8 javascript engine. Node is similiar to a browser in some aspects:
- js language support
- event-driven architecture where asynchronous programming is naturally supported
- there are a lot of common JavaScript API (JSON, string manipulation, timers)

Main differences
- browser executes JS in the client side and provides a DOM to intercat with the webpage while node is running on server side without DOM (no document and window global objects).
- Architecture difference: Node is made of V8 engine, C++ bindings and addons, javascript modules on top. Browsers has a JS interpreter and engine executed inside a rendering engine, it is runned in a cycle continuously.
- node support both import and require to import modules while browser only use imports. because node supports both CommonJS and ES modules.