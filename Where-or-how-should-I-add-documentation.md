[[Page Maintainers|Where or how should I add documentation?]]: @zou3519

## Goals

The [PyTorch Developer Wiki](https://github.com/pytorch/pytorch/wiki/) is the home for developer-facing PyTorch documentation. User-facing documentation goes on [pytorch.org/docs](https://pytorch.org/docs/stable/index.html).

Documentation can easily go out of date. This page is meant to be a guide to best practices for where to add pages, how to add pages, editing a page, or suggesting edits to a page to help keep the documents up-to-date.

## Where should I put developer-facing docs?

Please add developer docs to the Developer Wiki! There are a number of places where developer documentation exists today:
- in README files of subdirectories in pytorch/pytorch
- in CONTRIBUTING.MD

Adding your docs to the Developer Wiki has the following benefits:
- It's easy to update the documentation -- no need to sit through a land cycle
- It's easier to find the information: READMEs are not very discoverable
- All the information is in one place for searching (and you can use GitHub search to search only the wiki!)

Our recommendation is to add a Wiki page and link to it with a quick blurb in some README file or CONTRIBUTING.MD.

## Adding a new page to the Developer Wiki

Each page should have a "Page Maintainers: " section at a very top that lists the GitHub handles of folks who are maintaining the page. If you're an expert on the page, please put your name down so that people know where to bring questions! Maintainers are responsible for keeping the pages up to date.

When you add a page, please make sure to pick a good title! Renaming the title breaks any past hyperlinks to the page. For example, you should not use the PyTorch version number in the title name (e.g. "Benchmarking in PyTorch 1.9" should just be "Benchmarking")

## A page looks out of date, what do I do?

* For small changes, feel free to fix it yourself.
* For larger changes, or if you're not sure how to correct something, open a GitHub issue and assign it to the maintainers.

NOTE: Please do NOT rename pages without updating all the links to it. The page title is unfortunately what gets used as the URL.

## Where do I actually write user-facing docs?

See [CONTRIBUTING.MD](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#writing-documentation)
