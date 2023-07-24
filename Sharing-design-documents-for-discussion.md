In PyTorch, there are 4 avenues to engage in developer discussions:

1. PyTorch RFCs
1. Design Docs Google Drive
1. PyTorch Developer Discussion forum
1. PyTorch Slack


## Request for Comments (RFCs)
To propose a new feature, you'll submit a Request For Comments (RFC).  This RFC is basically a design proposal where you can share a detailed description of what change you want to make, why it's needed, and how you propose to implement it.

It's easier to make changes while your feature is in the ideation phase vs the PR phase, and this doc gives core maintainers an opportunity to suggest refinements before you start code.  For example, they may know of other planned efforts that your work would otherwise collide with, or they may suggest implementation changes that make your feature more broadly usable.

Here are [detailed instructions on how to submit an RFC](https://github.com/pytorch/rfcs/blob/master/README.md)

## Design Docs Google Drive

When proposing a complex new feature or analyzing some tradeoff, plain GitHub issues might be too restrictive for the discussion. In such situations the inline commenting and rich formatting are preferred. PyTorch has a shared google drive for design documents called 'PyTorch Design Docs'. It's owned by `pytorch.org` domain and thus has a long term ownership not tied to the individual account.

The public folder on the google drive is [accessible to anyone on the internet](https://drive.google.com/drive/folders/14ZtBcdSJZTmNEIeTgGffZDb8ia0D7Kbr?usp=sharing) with permission to comment.

### Adding new document

PyTorch manages `contributors@pytorch.org` mailing list that has write access to the gdrive. If you'd like to be added ping @dzhulgakov, @ezyang or @alband on Slack.

Additionally, any existing member of `contributors@pytorch.org` can add new people to the gdrive by clicking the 'Manage members'. So you can ping any existing contributor with write access to add you.

Once you have the write access, you should see 'PyTorch Design Docs' at https://drive.google.com for your account under 'Shared drives'. You can create a new document in the public folder or move an existing one. Moving the document to the drive changes its ownership and makes it visible to the world.

### Sharing the document

Obtain the public link to the doc by clicking the 'Share' button. If the doc was moved to the 'PyTorch Design Docs (public)' folder, the permission should be already set to 'Anyone on the internet can comment'. You can share the link in GitHub issue, dev-discuss forum or elsewhere.


## PyTorch Developer Discussions forum
The [dev-discuss](dev-discuss.pytorch.org) forum is for anyone who is making contributions to PyTorch. Maintainers and developers often share designs, notes and their knowledge about the codebase. It is also a place to ask for help and share your ideas as a contributor.

## PyTorch Slack
For power users who have made multiple contributions or have already participated significantly on the forums, we have a Slack channel to discuss advanced topics in PyTorch. If you'd like to join, you can [file a request](https://docs.google.com/forms/d/e/1FAIpQLSeADnUNW36fjKjYzyHDOzEB_abKQE9b6gqqW9NXse6O0MWh0A/viewform).