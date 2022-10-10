
# Simple Trace Tool [EXPERIMENTAL]

## Introduction

Simple Trace is a built-in tool for user to dump out the calling stack for a piece of code. User can enable this tool and then automatically print out the verbose messages of called operators in a stack format with different indent to distinguish the context. After using, user is also able to disable this tool by a simple method.

## Use Case

In Intel® Extension for PyTorch\*, user needs to build from source and enable the simple trace tool explicitly in their model script to use it. And user can disable it after using. The dumped message will be printed to screen as default like verbose while this tool was enabled.

### Build Tool

Simple Trace tool should be built into Intel® Extension for PyTorch\* from source. To build this tool in, user should turn on the build option `BUILD_SIMPLE_TRACE=ON` then build Intel® Extension for PyTorch\*.

```unix
BUILD_SIMPLE_TRACE=ON python setup.py install
```

### Enable and Disable Tool

Simple Trace tool should be enabled explicitly in the model script before using, and disabled explicitly after using. Please refer to the example shown as below:

```python
# import all necessary libraries
import torch
import intel_extension_for_pytorch

print(torch.xpu.using_simple_trace())   # False
a = torch.randn(100).xpu()              # this line won't be traced

torch.xpu.enable_simple_trace()         # to enable simple trace tool
print(torch.xpu.using_simple_trace())   # True

# test code begin from here
b = torch.randn(100).xpu()
c = torch.unique(b)
# test code end to here

torch.xpu.disable_simple_trace()        # to disable simple trace tool
print(torch.xpu.using_simple_trace())   # False
```

### Results

Take the script shown as above, user will see these messages printed out to console:

```text
[262618.262618]  Call  into  OP: wrapper__empty_strided -> at::AtenIpexTypeXPU::empty_strided (#0)
[262618.262618]  Step out of OP: wrapper__empty_strided -> at::AtenIpexTypeXPU::empty_strided (#0)
[262618.262618]  Call  into  OP: wrapper__copy_ -> at::AtenIpexTypeXPU::copy_ (#1)
[262618.262618]  Step out of OP: wrapper__copy_ -> at::AtenIpexTypeXPU::copy_ (#1)
[262618.262618]  Call  into  OP: wrapper___unique2 -> at::AtenIpexTypeXPU::_unique2 (#2)
[262618.262618]    Call  into  OP: wrapper__clone -> at::AtenIpexTypeXPU::clone (#3)
[262618.262618]      Call  into  OP: wrapper__empty_strided -> at::AtenIpexTypeXPU::empty_strided (#4)
[262618.262618]      Step out of OP: wrapper__empty_strided -> at::AtenIpexTypeXPU::empty_strided (#4)
[262618.262618]      Call  into  OP: wrapper__copy_ -> at::AtenIpexTypeXPU::copy_ (#5)
[262618.262618]      Step out of OP: wrapper__copy_ -> at::AtenIpexTypeXPU::copy_ (#5)
[262618.262618]    Step out of OP: wrapper__clone -> at::AtenIpexTypeXPU::clone (#3)
[262618.262618]    Call  into  OP: wrapper___reshape_alias -> at::AtenIpexTypeXPU::_reshape_alias (#6)
[262618.262618]    Step out of OP: wrapper___reshape_alias -> at::AtenIpexTypeXPU::_reshape_alias (#6)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#7)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#7)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#8)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#8)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#9)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#9)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#10)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#10)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#11)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#11)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#12)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#12)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#13)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#13)
[262618.262618]    Call  into  OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#14)
[262618.262618]    Step out of OP: wrapper_memory_format_empty -> at::AtenIpexTypeXPU::empty (#14)
[262618.262618]    Call  into  OP: wrapper__as_strided -> at::AtenIpexTypeXPU::as_strided (#15)
[262618.262618]    Step out of OP: wrapper__as_strided -> at::AtenIpexTypeXPU::as_strided (#15)
[262618.262618]    Call  into  OP: wrapper___local_scalar_dense -> at::AtenIpexTypeXPU::_local_scalar_dense (#16)
[262618.262618]    Step out of OP: wrapper___local_scalar_dense -> at::AtenIpexTypeXPU::_local_scalar_dense (#16)
[262618.262618]    Call  into  OP: wrapper__as_strided -> at::AtenIpexTypeXPU::as_strided (#17)
[262618.262618]    Step out of OP: wrapper__as_strided -> at::AtenIpexTypeXPU::as_strided (#17)
[262618.262618]    Call  into  OP: wrapper___local_scalar_dense -> at::AtenIpexTypeXPU::_local_scalar_dense (#18)
[262618.262618]    Step out of OP: wrapper___local_scalar_dense -> at::AtenIpexTypeXPU::_local_scalar_dense (#18)
[262618.262618]    Call  into  OP: wrapper__resize_ -> at::AtenIpexTypeXPU::resize_ (#19)
[262618.262618]    Step out of OP: wrapper__resize_ -> at::AtenIpexTypeXPU::resize_ (#19)
[262618.262618]  Step out of OP: wrapper___unique2 -> at::AtenIpexTypeXPU::_unique2 (#2)
[262618.262618]  Call  into  OP: wrapper__copy_ -> at::AtenIpexTypeXPU::copy_ (#20)
[262618.262618]  Step out of OP: wrapper__copy_ -> at::AtenIpexTypeXPU::copy_ (#20)
```

The meanings of each field are shown as below:

- `pid.tid`, `[262618.262618]`: the process id and the thread id responsible to the printed-out line.
- `behavior`, `Call into OP`, `Step out of OP`: call-in or step-out behaviour of the operators in a run.
- `name1 -> name2`, `wrapper__empty_strided -> at::AtenIpexTypeXPU::empty_strided`: the calling operator for the current step. The name1 before the arrow shows the wrapper from PyTorch. The name2 after the arrow shows the function name of which was called in or steped out in Intel® Extension for PyTorch\* at the current step.
- `(#No.)`, `(#0)`: index of the called operators. This index was numbered from 0 in the order of each operator to be called.
- `indent`: the indent ahead of every behavior shows the nested relationship between operators. The operator call-in line with more indent should be a child of what was called above it.

With this result, user could tell the calling stack of traced script without involving complicated debug tools such as gdb.