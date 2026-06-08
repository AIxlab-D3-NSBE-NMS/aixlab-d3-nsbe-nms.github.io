# Editing this Documentation Page

This website was created using `mkdocs`. To modify these instructions, clone the repository.
Individual pages in the website cn be edited in `docs/`. To create a new page, create a new `.md` file in `docs/` and add the filename in `mkdocs.yml`.

### Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.


### Commands

It is useful to have `mkdocs` installed as it allows local serve and autoreload before pushing changes to GitHub. Here's a list of useful commands:

* `mkdocs serve` - Start the live-reloading docs server. Open localhost:8000 on browser for the website live preview
* `mkdocs gh-deploy` - builds and deploys the changes to the gh-pages branch. This is the default branch where github builds the webpage from. This has been automated via the workflow in .github/workflows at every push to main. You can run it, but you shouldn't need to. Check out the build progress after every push in [Github Actions](https://github.com/aixlab-d3-nsbe-nms/aixlab-d3-nsbe-nms.github.io/actions)
* `mkdocs -h` - Print help message and exit.
