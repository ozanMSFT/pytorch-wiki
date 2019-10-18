The preferred way of writing memory format aware operators is to use the `switch` operator. This approach allows us to expand memory formats support in the future.

```cpp
auto memory_format = input_tensor.suggest_memory_format();
auto output_tensor = at::empty(output_shape, memory_format);

switch (memory_format) {
  case MemoryFormat::ChannelsLast: {
    input_cl_contiguous = input_tensor.contiguous(
        MemoryFormat::ChannelsLast); // if kernel requires memory dense
                                     // tensor
    // .... kernel code
    break;
  }
  case MemoryFormat::Contiguous: {
    // .... standard kernel
    break;
  }
  default:
    TORCH_CHECK(
        false,
        "Unsupported memory format. Supports only ChannelsLast, Contiguous");
}
```