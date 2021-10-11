When proposing a complex new feature or analyzing some tradeoff, plain GitHub issues might be too restrictive for the discussion. In such situations the inline commenting and rich formatting are preferred. We offer two options for that.

## Google docs

PyTorch has a shared google drive for design documents called 'PyTorch Design Docs'. It's owned by `pytorch.org` domain and thus has a long term ownership not tied to the individual account.

The public folder on the google drive is [accessible to anyone on the internet](https://drive.google.com/drive/folders/14ZtBcdSJZTmNEIeTgGffZDb8ia0D7Kbr?usp=sharing) with permission to comment.

### Adding new document

PyTorch manages `contributors@pytorch.org` mailing list that has write access to the gdrive. If you'd like to be added ping @dzhulgakov, @ezyang or @alband on Slack.

Additionally, any existing member of `contributors@pytorch.org` can add new people to the gdrive by clicking the 'Manage members'. So you can ping any existing contributor with write access to add you.

Once you have the write access, you should see 'PyTorch Design Docs' at https://drive.google.com for your account under 'Shared drives'. You can create a new document in the public folder or move an existing one. Moving the document to the drive changes its ownership and makes it visible to the world.

### Sharing the document

Obtain the public link to the doc by clicking the 'Share' button. If the doc was moved to the 'PyTorch Design Docs (public)' folder, the permission should be already set to 'Anyone on the internet can comment'. You can share the link in GitHub issue, dev-discuss forum or elsewhere.

## RFC repo

Create a new `.md` file and send a PR to https://github.com/pytorch/rfcs . Discussion can happen as inline comments in the PR's review.