# <img width="24" height="24" src="assets/logo.svg"> Create Pull Request
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Create%20Pull%20Request-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAOCAYAAAAfSC3RAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAAM6wAADOsB5dZE0gAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ5vuPBoAAAERSURBVCiRhZG/SsMxFEZPfsVJ61jbxaF0cRQRcRJ9hlYn30IHN/+9iquDCOIsblIrOjqKgy5aKoJQj4O3EEtbPwhJbr6Te28CmdSKeqzeqr0YbfVIrTBKakvtOl5dtTkK+v4HfA9PEyBFCY9AGVgCBLaBp1jPAyfAJ/AAdIEG0dNAiyP7+K1qIfMdonZic6+WJoBJvQlvuwDqcXadUuqPA1NKAlexbRTAIMvMOCjTbMwl1LtI/6KWJ5Q6rT6Ht1MA58AX8Apcqqt5r2qhrgAXQC3CZ6i1+KMd9TRu3MvA3aH/fFPnBodb6oe6HM8+lYHrGdRXW8M9bMZtPXUji69lmf5Cmamq7quNLFZXD9Rq7v0Bpc1o/tp0fisAAAAASUVORK5CYII=)](https://github.com/marketplace/actions/create-pull-request)

A GitHub action to create a pull request for changes to your repository in the actions workspace.

Changes to a repository in the Actions workspace persist between steps in a workflow.
This action is designed to be used in conjunction with other steps that modify or add files to your repository.
The changes will be automatically committed to a new branch and a pull request created.

Create Pull Request action will:

1. Check for repository changes in the Actions workspace. This includes untracked (new) files as well as modified files.
2. Commit all changes to a new branch, or update an existing pull request branch.
3. Create a pull request to merge the new branch into the currently active branch executing the workflow.

## Usage

See [examples](examples.md) for detailed use cases.

Linux
```yml
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v1.6.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Multi platform - Linux, MacOS, Windows (beta)
```yml
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v1.6.0-multi
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Note**: If you want pull requests created by this action to trigger an `on: pull_request` workflow then you must use a [Personal Access Token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line) instead of the default `GITHUB_TOKEN`.
See [this issue](https://github.com/peter-evans/create-pull-request/issues/48) for further details.

### Environment variables

These variables are *all optional*. If not set, sensible default values will be used.

| Name | Description | Default |
| --- | --- | --- |
| `COMMIT_MESSAGE` | The message to use when committing changes. | `Auto-committed changes by create-pull-request action` |
| `COMMIT_AUTHOR_EMAIL` | The email address of the commit author. | For `push` events, the HEAD commit author. Otherwise, <GITHUB_ACTOR>@users.noreply.github.com, where `GITHUB_ACTOR` is the GitHub user that initiated the event. |
| `COMMIT_AUTHOR_NAME` | The name of the commit author. | For `push` events, the HEAD commit author. Otherwise, <GITHUB_ACTOR>, the GitHub user that initiated the event. |
| `PULL_REQUEST_TITLE` | The title of the pull request. | `Auto-generated by create-pull-request action` |
| `PULL_REQUEST_BODY` | The body of the pull request. | `Auto-generated pull request by [create-pull-request](https://github.com/peter-evans/create-pull-request) GitHub Action` |
| `PULL_REQUEST_LABELS` | A comma separated list of labels. | none |
| `PULL_REQUEST_ASSIGNEES` | A comma separated list of assignees (GitHub usernames). | none |
| `PULL_REQUEST_REVIEWERS` | A comma separated list of reviewers (GitHub usernames) to request a review from. | none |
| `PULL_REQUEST_TEAM_REVIEWERS` | A comma separated list of GitHub teams to request a review from. | none |
| `PULL_REQUEST_MILESTONE` | The number of the milestone to associate this pull request with. | none |
| `PULL_REQUEST_BRANCH` | The branch name. See **Branch naming** below for details. | `create-pull-request/patch` |
| `PULL_REQUEST_BASE` | Overrides the base branch. **Use with caution!** | Defaults to the currently checked out branch. |
| `BRANCH_SUFFIX` | The branch suffix type. Valid values are `short-commit-hash`, `timestamp`, `random` and `none`. See **Branch naming** below for details. | `short-commit-hash` |

**Output environment variables**

- `PULL_REQUEST_NUMBER` - The number of the pull request created.

**Debug environment variables**

The following parameter is available for debugging and troubleshooting.

- `DEBUG_EVENT` - If present, outputs the event data that triggered the workflow.

### Branch naming

For branch naming there are two strategies. Always create a new branch each time there are changes to be committed, OR, create a fixed-name pull request branch that will be updated with any new commits until it is merged or closed.

#### Strategy A - Always create a new pull request branch (default)

For this strategy there are three options to suffix the branch name.
The branch name is defined by the variable `PULL_REQUEST_BRANCH` and defaults to `create-pull-request/patch`. The following options are values for `BRANCH_SUFFIX`.

- `short-commit-hash` (default) - Commits will be made to a branch suffixed with the short SHA1 commit hash. e.g. `create-pull-request/patch-fcdfb59`, `create-pull-request/patch-394710b`

- `timestamp` - Commits will be made to a branch suffixed by a timestamp. e.g. `create-pull-request/patch-1569322532`, `create-pull-request/patch-1569322552`

- `random` - Commits will be made to a branch suffixed with a random alpha-numeric string. This option should be used if multiple pull requests will be created during the execution of a workflow. e.g. `create-pull-request/patch-6qj97jr`, `create-pull-request/patch-5jrjhvd`

#### Strategy B - Create and update a pull request branch

To use this strategy, set `BRANCH_SUFFIX` to the value `none`. The variable `PULL_REQUEST_BRANCH` defaults to `create-pull-request/patch`. Commits will be made to this branch and a pull request created. Any subsequent changes will be committed to the *same* branch and reflected in the existing pull request.

### Ignoring files

If there are files or directories you want to ignore you can simply add them to a `.gitignore` file at the root of your repository. The action will respect this file.

## Reference Example

The following workflow is a reference example that sets all the main environment variables.

See [examples](examples.md) for more realistic use cases.

```yml
name: Create Pull Request
on: push
jobs:
  createPullRequest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Create report file
        run: date +%s > report.txt
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v1.6.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          COMMIT_MESSAGE: Add report file
          COMMIT_AUTHOR_EMAIL: peter-evans@users.noreply.github.com
          COMMIT_AUTHOR_NAME: Peter Evans
          PULL_REQUEST_TITLE: '[Example] Add report file'
          PULL_REQUEST_BODY: |
            New report
            - Contains *today's* date
            - Auto-generated by [create-pull-request][1]

            [1]: https://github.com/peter-evans/create-pull-request
          PULL_REQUEST_LABELS: report, automated pr
          PULL_REQUEST_ASSIGNEES: peter-evans
          PULL_REQUEST_REVIEWERS: peter-evans
          PULL_REQUEST_MILESTONE: 1
          PULL_REQUEST_BRANCH: example-patches
          BRANCH_SUFFIX: short-commit-hash
      - name: Check output environment variable
        run: echo "Pull Request Number - $PULL_REQUEST_NUMBER"
```

This reference configuration will create pull requests that look like this:

![Pull Request Example](assets/pull-request-example.png)

## License

[MIT](LICENSE)
