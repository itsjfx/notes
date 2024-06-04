# Actions

## Deploy to GitHub Pages without a branch

Previously you could only deploy to GitHub pages with a branch, meaning your repo was larger than it needed to be, even after rewriting history to have a single commit.

Now you can use a workflow similar to: https://github.com/itsjfx/jfx.ac/blob/master/.github/workflows/hugo.yaml to create an artifact in CI (similar to a release) then deploy from that artifact.

Doc: https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages