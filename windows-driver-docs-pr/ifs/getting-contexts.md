---
title: Getting Contexts
description: Getting Contexts
keywords:
- contexts WDK file system minifilter , getting
ms.date: 04/20/2017
ms.localizationpriority: medium
---

# Getting Contexts


## <span id="ddk_registering_the_minifilter_if"></span><span id="DDK_REGISTERING_THE_MINIFILTER_IF"></span>


Once a minifilter driver has set a context for an object, it can get the context by calling **FltGet***Xxx***Context**, where *Xxx* is the context type.

In the following code example, taken from the SwapBuffers sample minifilter driver, the minifilter driver calls [**FltGetVolumeContext**](/windows-hardware/drivers/ddi/fltkernel/nf-fltkernel-fltgetvolumecontext) to get a volume context:

```cpp
status = FltGetVolumeContext(
 FltObjects->Filter,    //Filter
 FltObjects->Volume,    //Volume
                &volCtx);              //Context
...
if (volCtx != NULL) {
 FltReleaseContext(volCtx);
}
```

If the call to [**FltGetVolumeContext**](/windows-hardware/drivers/ddi/fltkernel/nf-fltkernel-fltgetvolumecontext) is successful, the *Context* parameter receives the address of the caller's volume context. **FltGetVolumeContext** increments the reference count on the *Context* pointer. Thus, when this pointer is no longer needed, the minifilter driver must release it by calling [**FltReleaseContext**](/windows-hardware/drivers/ddi/fltkernel/nf-fltkernel-fltreleasecontext).

 

