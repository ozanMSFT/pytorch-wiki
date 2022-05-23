This document lists conventions on namespacing in the `torch.onnx` module.

## Create private namespaces by default

Make sure new functions and classes are **private to the module by default.** New modules should be **private to `torch.onnx` by default.**

> Only "promote" them to public names when needed, by removing the prefix `_`

### Rationale

1. We don’t want users to depend on interfaces meant for internal use. This makes it hard for the code and architecture to evolve.
2. We want to quickly understand the visibility and origin of a name when reading the code, both to reduce mental load and to make refactoring easier.
3. Happy type checkers -> happy engineers

## Naming and usage convention

1. Import only modules (in most cases).
    - **Import only the _module_ instead of classes and functions** to keep the namespace clean and provide readers with more context on where its members come from. For example, readers don’t need to backtrack to see `sleep` is a function from `time`, as opposed to a function defined in the file. https://google.github.io/styleguide/pyguide.html#22-imports
    - As another example,

        ![image](https://user-images.githubusercontent.com/11205048/169882197-f0a2ff8f-ec58-4610-a3c7-137ac68b3e53.png)

        Having the module name “opset9” clearly signals to the reader where the op comes from. This helps reduce errors caused by confusion when different versions of a function are defined in different modules and used together.
2. `_module` is private to `torch.onnx`
    - Not for external consumption
    - If a module is inside a folder which has a `_` name. It is ok for the module to not be prefixed by `_` for simplicity.
        - E.g. `torch.onnx._symbolic_opsets.symbolic_opset9` is good.
3. `_function` is private to its module, this means
    - We **should not use it in other modules** (in most cases)
    - Example:

        ```python
        from torch.onnx.symbolic_helper import _unimplemented
        ```

        should become something like

        ```python
        from torch.onnx._symbolic_helper import unimplemented
        ```

        for it to be consumed internally.

        **Explanation:** `_symbolic_helper` (as an example name; doesn’t mean we should hide what is already exposed) is a private module to `torch.onnx` so we can import it in other modules. `unimplemented` is not private to its own module, so we can use it in another module.
    - This provides clarity to readers and consistency in code structure, reinforcing the convention that private functions should not be used outside of their modules.
    - Additionally, a function not prefixed by `_` signals that we should create a unit test for it. A function prefixed with `_` may be implementation details not worthy of unit tests.
    - It keeps type checkers happy by only using non-private functions in a module.
4. When a name is aliased in `torch.onnx.__init__`, import them from where they are defined. **Do not import the alias for internal use.**
    - **Why?** It can reduce the possibility of loading a _partially initialized_ `torch.onnx` (which causes errors). It also makes it clear to readers where the names come from.
    - Example:

        In `__init__.py` we alias

        ```python
        TensorProtoDataType = _C._onnx.TensorProtoDataType
        ```

        In symbolic_helper.py, instead of using ~`torch.onnx.TensorProtoDataType.UINT8`~ internally, we should use `_C_onnx.TensorProtoDataType.UINT8`.

    - Alias defined in `__init__.py` should only be used outside the module (by users).
