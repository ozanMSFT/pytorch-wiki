
* If you run into errors when running `python setup.py develop`, here are some debugging steps:
  1. Run `printf '#include <stdio.h>\nint main() { printf("Hello World");}'|clang -x c -; ./a.out` to make sure
  your CMake works and can compile this simple Hello World program without errors.
  2. Nuke your `build` directory. The `setup.py` script compiles binaries into the `build` folder and caches many
  details along the way, which saves time the next time you build. If you're running into issues, you can always
  `rm -rf build` from the toplevel `pytorch` directory and start over.
  3. If you have made edits to the PyTorch repo, commit any change you'd like to keep and clean the repo with the
  following commands (note that clean _really_ removes all untracked files and changes.):
      ```bash
      git submodule deinit -f .
      git clean -xdf
      python setup.py clean
      git submodule update --init --recursive # very important to sync the submodules
      python setup.py develop                 # then try running the command again
      ```
  4. The main step within `python setup.py develop` is running `make` from the `build` directory. If you want to
    experiment with some environment variables, you can pass them into the command:
      ```bash
      ENV_KEY1=ENV_VAL1[, ENV_KEY2=ENV_VAL2]* python setup.py develop
      ```

* If you run into issue running `git submodule update --init --recursive`. Please try the following:
  - If you encounter an error such as
    ```
    error: Submodule 'third_party/pybind11' could not be updated
    ```
    check whether your Git local or global config file contains any `submodule.*` settings. If yes, remove them and try again.
    (please reference [this doc](https://git-scm.com/docs/git-config#Documentation/git-config.txt-submoduleltnamegturl) for more info).

  - If you encounter an error such as
    ```
    fatal: unable to access 'https://github.com/pybind11/pybind11.git': could not load PEM client certificate ...
    ```
    this is likely that you are using HTTP proxying and the certificate expired. To check if the certificate is valid, run
    `git config --global --list` and search for config like `http.proxysslcert=<cert_file>`. Then check certificate valid date by running
    ```bash
    openssl x509 -noout -in <cert_file> -dates
    ```

  - If you encounter an error that some third_party modules are not checked out correctly, such as
    ```
    Could not find .../pytorch/third_party/pybind11/CMakeLists.txt
    ```
    remove any `submodule.*` settings in your local git config (`.git/config` of your pytorch repo) and try again.
* If you're a Windows contributor, please check out [Best Practices](https://github.com/pytorch/pytorch/wiki/Best-Practices-to-Edit-and-Compile-Pytorch-Source-Code-On-Windows).
* For help with any part of the contributing process, please don’t hesitate to utilize our Zoom office hours! See details [here](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours)
