# Actions

## Deploy to GitHub Pages without a branch

Previously you could only deploy to GitHub pages with a branch, meaning your repo was larger than it needed to be, even after rewriting history to have a single commit.

Now you can use a workflow similar to: <https://github.com/itsjfx/jfx.ac/blob/master/.github/workflows/hugo.yaml> to create an artifact in CI (similar to a release) then deploy from that artifact.

Doc: <https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages>

## Injection prevention

<https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions#using-an-intermediate-environment-variable>

I suggest passing ANY GitHub variables into an environment variable, then evaluate the variables with quotes in Bash. This is a good habit as you won't need to think too hard whether input is trusted or not.

e.g.

```yaml
      - name: Check PR title
        env:
          TITLE: ${{ github.event.pull_request.title }}
        run: |
          if [[ "$TITLE" =~ ^octocat ]]; then
          echo "PR title starts with 'octocat'"
          exit 0
          else
          echo "PR title did not start with 'octocat'"
          exit 1
          fi
```
