If you arrived here due to a lint error, running the command given in the message will fix your problem. 

This wiki covers how to set up clang-format as part of your workflow.
1. Add `tools/git-clang-format` to your `$PATH`. This is a modified version of the default git integration that fetches a pinned binary guaranteed to work with the CI and other PyTorch developers. Once its added to your `$PATH`, running `git clang-format` will invoke it.
2. To ensure your changes are always formatted, you can add `git clang-format` to your a pre-commit hook. PyTorch provides a pre-commit hook ([instructions](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#pre-commit-tidylinting-hook)) that runs our standard linters, or you can make your own that just format:
```
# run in pytorch root
cat <<\EOF > .git/hooks/pre-commit
#!/bin/bash
set -e
git clang-format
EOF

chmod 755 .git/hooks/pre-commit
```