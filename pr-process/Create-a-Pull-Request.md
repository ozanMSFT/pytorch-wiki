Once you have conducted all the [pre-commit checks](Pre-Commit-Checks) and pushed your changes to your fork, you are ready to submit it to the PyTorch codebase as a pull request.

## New PR Checklist
- [ ] If your change is non-trivial, please ensure there is an open issue to link your PR to or [[create a new issue|Finding-Or-Reporting-Issues]].

- [ ] Create a [new PR](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request) on Github.

- [ ] Ensure that your Pull Request has a clear title, a detailed description of what the PR is doing, and any additional information that is not already in the issue.
    - If your Pull Request introduces backwards-incompatible changes, please include details about that as well.

- [ ] Ensure the PR has a “release notes:” or “topic: not user facing” label. Some bots may come and add these within 30 minutes of submitting the PR, but if there are none, then please use [[pytorchbot|Bot-commands]] to add one (for example, comment `@pytorchbot label "release notes: autograd"` on your PR to add the “release notes: autograd” label). 
    - If you don’t know what label to add, then please wait until the PR is triaged and ask the reviewer what to apply.


## Common Mistakes to Avoid

<!-- From https://github.com/pytorch/pytorch/blob/main/docs/source/community/contribution_guide.rst#common-mistakes-to-avoid -->

-  **Did you add tests?** (Or if the change is hard to test, did you
   describe how you tested your change?)

   -  We have a few motivations for why we ask for tests:

      1. to help us tell if we break it later
      2. to help us tell if the patch is correct in the first place
         (yes, we did review it, but as Knuth says, “beware of the
         following code, for I have not run it, merely proven it
         correct”)

   -  When is it OK not to add a test? Sometimes a change can't be
      conveniently tested, or the change is so obviously correct (and
      unlikely to be broken) that it's OK not to test it. On the
      contrary, if a change seems likely (or is known to be likely)
      to be accidentally broken, it's important to put in the time to
      work out a testing strategy.

-  **Is your PR too long?**

   -  It's easier for us to review and merge small PRs. The difficulty of
      reviewing a PR scales nonlinearly with its size.
   -  When is it OK to submit a large PR? It helps a lot if there was a
      corresponding design discussion in an issue, with sign off from
      the people who are going to review your diff. We can also help
      give advice about how to split up a large change into individually
      shippable parts. Similarly, it helps if there is a complete
      description of the contents of the PR: it's easier to review code
      if we know what's inside!

-  **Comments for subtle things?** In cases where the behavior of your code
   is nuanced, please include extra comments and documentation to allow
   us to better understand the intention of your code.
-  **Did you add a hack?** Sometimes, the right answer is a hack. But
   usually, we will have to discuss it.
-  **Do you want to touch a very core component?** To prevent
   major regressions, pull requests that touch core components receive
   extra scrutiny. Make sure you've discussed your changes with the team
   before undertaking major changes.
-  **Want to add a new feature?** If you want to add new features,
   comment your intention on the related issue. Our team tries to
   comment on and provide feedback to the community. It's better to have
   an open discussion with the team and the rest of the community before
   building new features. This helps us stay aware of what you're
   working on and increases the chance that it'll be merged.
-  **Did you touch code unrelated to the PR?** To aid in code review,
   please only include files in your pull request that are directly
   related to your changes.

