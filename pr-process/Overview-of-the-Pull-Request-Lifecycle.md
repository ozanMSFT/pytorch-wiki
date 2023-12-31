Like many open source projects, PyTorch uses a workflow based on pull requests. You will create a copy of the repository in your Git branch, make your changes and test it, push those changes to your fork (`origin`), and create a pull request (PR) against the official repo (`upstream`).

## Prerequsities
You should have
- [setup your system](System-Setup), 
- [got the latest source](Fork-Clone-and-Checkout), and 
- [built PyTorch](Build-PyTorch).

## Making your code change

1. [Find or report](Finding-Or-Reporting-Issues) a new issue to work on. If your change is minor (like fixing a typo) feel free to skip this step. 

2. Create a new branch with `git fetch && git checkout -b <my-branch-name>` to start working on your change. 
   
3. Make changes locally to the code. Whether you're working on a new feature or a bug fix, always consider adding tests for your changes (using the `unittest` framework).

4. Test your changes locally with `python ./test/test_torch.py` ([more details](Running-and-writing-tests)).
   
5. Review other [pre-commit checks](Pre-Commit-Checks).

6. Push changes to your fork `git push`
    - If your changes are tracking an older version of PyTorch, rebase and push
     ```
     git pull --rebase upstream viable/strict 
     git push -f
     ```

7. [Create a new Github PR](Create-a-Pull-Request) to propose changes from your fork into the official PyTorch repo.

8. Review, address comments on your PR, and initiate merge along the [[pull request workflow|Typical Pull Request Workflow]].

9.  Celebrate your contributions and welcome to the PyTorch Developer community :)