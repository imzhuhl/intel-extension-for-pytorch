Examples
========

**_NOTE:_** Check individual feature page for examples of feature usage. All features are listed in the [feature page](./features.rst).

**_NOTE:_** Feature examples and examples below are available at Github source tree, under `examples` directory.

## Training

### Single-instance Training

#### Code Changes Highlight

There is only a line of code change required to use Intel® Extension for PyTorch\* on training, as shown:
1. `ipex.optimize` function applies optimizations against the model object, as well as an optimizer object.

```
...
import torch
import intel_extension_for_pytorch as ipex
...
model = Model()
criterion = ...
optimizer = ...
model.train()
# For Float32
model, optimizer = ipex.optimize(model, optimizer=optimizer)
# For BFloat16
model, optimizer = ipex.optimize(model, optimizer=optimizer, dtype=torch.bfloat16)
...
optimizer.zero_grad()
output = model(data)
...
```

#### Complete - Float32

[//]: # (marker_train_single_fp32_complete)
[//]: # (marker_train_single_fp32_complete)

#### Complete - BFloat16

[//]: # (marker_train_single_bf16_complete)
[//]: # (marker_train_single_bf16_complete)

### Distributed Training

Distributed training with PyTorch DDP is accelerated by oneAPI Collective Communications Library Bindings for Pytorch\* (oneCCL Bindings for Pytorch\*). The extension supports FP32 and BF16 data types. More detailed information and examples are available at its [Github repo](https://github.com/intel/torch-ccl).

**Note:** When performing distributed training with BF16 data type, use oneCCL Bindings for Pytorch\*. Due to a PyTorch limitation, distributed training with BF16 data type with Intel® Extension for PyTorch\* is not supported.

[//]: # (marker_train_ddp_complete)
```
import os
import torch
import torch.distributed as dist
import torchvision
import oneccl_bindings_for_pytorch as torch_ccl
import intel_extension_for_pytorch as ipex

LR = 0.001
DOWNLOAD = True
DATA = 'datasets/cifar10/'

transform = torchvision.transforms.Compose([
    torchvision.transforms.Resize((224, 224)),
    torchvision.transforms.ToTensor(),
    torchvision.transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
])
train_dataset = torchvision.datasets.CIFAR10(
        root=DATA,
        train=True,
        transform=transform,
        download=DOWNLOAD,
)
train_loader = torch.utils.data.DataLoader(
        dataset=train_dataset,
        batch_size=128
)

os.environ['MASTER_ADDR'] = '127.0.0.1'
os.environ['MASTER_PORT'] = '29500'
os.environ['RANK'] = os.environ.get('PMI_RANK', 0)
os.environ['WORLD_SIZE'] = os.environ.get('PMI_SIZE', 1)
dist.init_process_group(
backend='ccl',
init_method='env://'
)

model = torchvision.models.resnet50()
criterion = torch.nn.CrossEntropyLoss()
optimizer = torch.optim.SGD(model.parameters(), lr = LR, momentum=0.9)
model.train()
model, optimizer = ipex.optimize(model, optimizer=optimizer)

model = torch.nn.parallel.DistributedDataParallel(model)

for batch_idx, (data, target) in enumerate(train_loader):
    optimizer.zero_grad()
    output = model(data)
    loss = criterion(output, target)
    loss.backward()
    optimizer.step()
    print('batch_id: {}'.format(batch_idx))
torch.save({
     'model_state_dict': model.state_dict(),
     'optimizer_state_dict': optimizer.state_dict(),
     }, 'checkpoint.pth')
```
[//]: # (marker_train_ddp_complete)

## Inference

The `optimize` function of Intel® Extension for PyTorch\* applies optimizations to the model, bringing additional performance boosts. For both computer vision workloads and NLP workloads, we recommend applying the `optimize` function against the model object.

### Float32

#### Imperative Mode

##### Resnet50

[//]: # (marker_inf_rn50_imp_fp32)
[//]: # (marker_inf_rn50_imp_fp32)

##### BERT

[//]: # (marker_inf_bert_imp_fp32)
[//]: # (marker_inf_bert_imp_fp32)

#### TorchScript Mode

We recommend you take advantage of Intel® Extension for PyTorch\* with [TorchScript](https://pytorch.org/docs/stable/jit.html) for further optimizations.

##### Resnet50

[//]: # (marker_inf_rn50_ts_fp32)
[//]: # (marker_inf_rn50_ts_fp32)

##### BERT

[//]: # (marker_inf_bert_ts_fp32)
[//]: # (marker_inf_bert_ts_fp32)

#### TorchDynamo Mode (Experimental, _NEW feature from 2.0.0_)

##### Resnet50

[//]: # (marker_inf_rn50_dynamo_fp32)
[//]: # (marker_inf_rn50_dynamo_fp32)

##### BERT

[//]: # (marker_inf_bert_dynamo_fp32)
[//]: # (marker_inf_bert_dynamo_fp32)

### BFloat16

Similar to running with FP32, the `optimize` function also works for BFloat16 data type. The only difference is setting `dtype` parameter to `torch.bfloat16`.
We recommend using Auto Mixed Precision (AMP) with BFloat16 data type.

#### Imperative Mode

##### Resnet50

[//]: # (marker_inf_rn50_imp_bf16)
[//]: # (marker_inf_rn50_imp_bf16)

##### BERT

[//]: # (marker_inf_bert_imp_bf16)
[//]: # (marker_inf_bert_imp_bf16)

#### TorchScript Mode

We recommend you take advantage of Intel® Extension for PyTorch\* with [TorchScript](https://pytorch.org/docs/stable/jit.html) for further optimizations.

##### Resnet50

[//]: # (marker_inf_rn50_ts_bf16)
[//]: # (marker_inf_rn50_ts_bf16)

##### BERT

[//]: # (marker_inf_bert_ts_bf16)
[//]: # (marker_inf_bert_ts_bf16)

### Fast Bert (*Experimental*)

[//]: # (marker_inf_bert_fast_bf16)
[//]: # (marker_inf_bert_fast_bf16)

### INT8

Starting from Intel® Extension for PyTorch\* 1.12.0, quantization feature supports both static and dynamic modes.

#### Calibration

##### Static Quantization

Please follow the steps below to perform static calibration:

1. Import `intel_extension_for_pytorch` as `ipex`.
2. Import `prepare` and `convert` from `intel_extension_for_pytorch.quantization`.
3. Instantiate a config object from `torch.ao.quantization.QConfig` to save configuration data during calibration.
4. Prepare model for calibration.
5. Perform calibration against dataset.
6. Invoke `ipex.quantization.convert` function to apply the calibration configure object to the fp32 model object to get an INT8 model.
7. Save the INT8 model into a `pt` file.


[//]: # (marker_int8_static)
[//]: # (marker_int8_static)

##### Dynamic Quantization

Please follow the steps below to perform static calibration:

1. Import `intel_extension_for_pytorch` as `ipex`.
2. Import `prepare` and `convert` from `intel_extension_for_pytorch.quantization`.
3. Instantiate a config object from `torch.ao.quantization.QConfig` to save configuration data during calibration.
4. Prepare model for quantization.
5. Convert the model.
6. Run inference to perform dynamic quantization.
7. Save the INT8 model into a `pt` file.

[//]: # (marker_int8_dynamic)
[//]: # (marker_int8_dynamic)

#### Deployment

For deployment, the INT8 model is loaded from the local file and can be used directly on the inference.

Follow the steps below:

1. Import `intel_extension_for_pytorch` as `ipex`.
2. Load the INT8 model from the saved file.
3. Run inference.

[//]: # (marker_int8_deploy)
[//]: # (marker_int8_deploy)

oneDNN provides [oneDNN Graph Compiler](https://github.com/oneapi-src/oneDNN/tree/dev-graph-preview4/doc#onednn-graph-compiler) as a prototype feature that could boost performance for selective topologies. No code change is required. Install <a class="reference external" href="installation.md#installation_onednn_graph_compiler">a binary</a> with this feature enabled. We verified this feature with `Bert-large`, `bert-base-cased`, `roberta-base`, `xlm-roberta-base`, `google-electra-base-generator` and `google-electra-base-discriminator`.

## C++

To work with libtorch, C++ library of PyTorch, Intel® Extension for PyTorch\* provides its C++ dynamic library as well. The C++ library is supposed to handle inference workload only, such as service deployment. For regular development, use the Python interface. Unlike using libtorch, no specific code changes are required. Compilation follows the recommended methodology with CMake. Detailed instructions can be found in [PyTorch tutorial](https://pytorch.org/tutorials/advanced/cpp_export.html#depending-on-libtorch-and-building-the-application).

During compilation, Intel optimizations will be activated automatically once C++ dynamic library of Intel® Extension for PyTorch\* is linked.

The example code below works for all data types.

**example-app.cpp**

[//]: # (marker_cppsdk_sample)
[//]: # (marker_cppsdk_sample)

**CMakeLists.txt**

[//]: # (marker_cppsdk_cmake)
[//]: # (marker_cppsdk_cmake)

**Command for compilation**

```bash
$ cd examples/cpu/inference/cpp
$ mkdir build
$ cd build
$ cmake -DCMAKE_PREFIX_PATH=<LIBPYTORCH_PATH> ..
$ make
```

If *Found IPEX* is shown as with a dynamic library path, the extension had been linked into the binary. This can be verified with Linux command *ldd*.

```bash
$ cmake -DCMAKE_PREFIX_PATH=/workspace/libtorch ..
-- The C compiler identification is GNU 11.2.1
-- The CXX compiler identification is GNU 11.2.1
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Warning at /workspace/libtorch/share/cmake/Torch/TorchConfig.cmake:22 (message):
  static library kineto_LIBRARY-NOTFOUND not found.
Call Stack (most recent call first):
  /workspace/libtorch/share/cmake/Torch/TorchConfig.cmake:127 (append_torchlib_if_found)
  /workspace/libtorch/share/cmake/IPEX/IPEXConfig.cmake:84 (FIND_PACKAGE)
  CMakeLists.txt:4 (find_package)


-- Found Torch: /workspace/libtorch/lib/libtorch.so
-- Found IPEX: /workspace/libtorch/lib/libintel-ext-pt-cpu.so
-- Configuring done
-- Generating done
-- Build files have been written to: examples/cpu/inference/cpp/build

$ ldd example-app
        ...
        libtorch.so => /workspace/libtorch/lib/libtorch.so (0x00007f3cf98e0000)
        libc10.so => /workspace/libtorch/lib/libc10.so (0x00007f3cf985a000)
        libintel-ext-pt-cpu.so => /workspace/libtorch/lib/libintel-ext-pt-cpu.so (0x00007f3cf70fc000)
        libtorch_cpu.so => /workspace/libtorch/lib/libtorch_cpu.so (0x00007f3ce16ac000)
        ...
        libdnnl_graph.so.0 => /workspace/libtorch/lib/libdnnl_graph.so.0 (0x00007f3cde954000)
        ...
```

## Model Zoo

Use cases that had already been optimized by Intel engineers are available at [Model Zoo for Intel® Architecture](https://github.com/IntelAI/models/tree/pytorch-r2.0-models). A bunch of PyTorch use cases for benchmarking are also available on the [GitHub page](https://github.com/IntelAI/models/tree/pytorch-r2.0-models/benchmarks#pytorch-use-cases). You can get performance benefits out-of-box by simply running scipts in the Model Zoo.
