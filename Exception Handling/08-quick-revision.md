# Exception Handling Quick Revision

# 30. Quick Revision Table

  -----------------------------------------------------------------------
  Concept                             Meaning
  ----------------------------------- -----------------------------------
  Exception                           Abnormal event that disrupts normal
                                      execution

  Checked Exception                   Checked by compiler

  Unchecked Exception                 `RuntimeException` and its
                                      subclasses

  `try`                               Contains risky code

  `catch`                             Handles exception

  `finally`                           Cleanup code

  `throw`                             Explicitly throws an exception
                                      object

  `throws`                            Declares exception in method
                                      signature

  Exception Propagation               Passing an unhandled exception
                                      toward callers

  Runtime Stack                       Stores method-call stack frames for
                                      a thread

  `toString()`                        Exception name + message

  `getMessage()`                      Exception message

  `printStackTrace()`                 Full stack trace

  Multi-Catch                         Handle multiple exception types in
                                      one catch

  Custom Exception                    Programmer-defined exception

  Fully Checked                       All subclasses are checked

  Partially Checked                   Contains both checked and unchecked
                                      subclasses
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 31. One-Line Memory Tricks

``` text
try     → Try risky code
catch   → Catch the exception
finally → Finally do cleanup
throw   → Throw an exception
throws  → Tell caller about the exception
```

``` text
throw  = actually throw
throws = declare / pass responsibility
```

``` text
getMessage()   → Message
toString()     → Name + Message
printStackTrace() → Full path
```

``` text
Checked   → Compiler checks
Unchecked → RuntimeException hierarchy
```

---

[Previous: Multi-Catch and Exception Handling Rules](./07-multi-catch-and-rules.md) · [Back to Index](./README.md) · [Root README](../README.md)
