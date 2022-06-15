# renovate-conflicted-repro

This repo is meant to reproduce the `conflicted` issue on Renovatebot noted in this discussion: https://github.com/renovatebot/renovate/discussions/16094

For this repo to work, you will need `node` and `docker` installed in your system. To get started, fork this repo. Afterward, run:

```sh
yarn
```

You will also need a Buildkite account, which we will use to self-host our Renovatebot runner and which you can create for free on their website: https://buildkite.com/.

Once the account is created, make a new pipeline called `Renovatebot Repro` (this is important for the `.buildkite/environment` file). Go through the instructions on their website to configure your forked repo. Once the pipeline is created, do the following:
- Click the `Edit Steps` button to go to the `Steps` section of the pipeline
- Scroll to the `Legacy Steps` panel
- Click `Add` > `Read steps from repository`
- Under `Commands to run`, enter: `buildkite-agent pipeline upload .buildkite/pipeline.yml`
- Click `Save steps`

Before Buildkite can run your pipeline, you'll also need an agent to run it. Prior to creating an agent, create an `.env.local` file in the root of the forked repository. In that file, add two environment variables:
- `BUILDKITE_AGENT_TOKEN`, which you can get from the Buildkite dashboard
- `GITHUB_API_TOKEN`, which you can create in `Settings` > `Developer settings` > `Personal access tokens` > `Generate new token`

Once `.env.local` is created, run `docker compose up` in the root of the repo to run a local Buildkite agent. If it connects successfully, you'll see the `Agents` number value in the Buildkite dashboard navigation go up to 1.

Finally, click on `New Build` in the `Renovatebot Repro` pipeline to kick off a build. After a few seconds, you Renovatebot should first create an issue that summarizes the upcoming PRs it will create (unless it's before 9am on Monday, then it'll create the PRs too!). Check off the line item that says `chore(deps): pin dependencies (@types/react, @types/react-dom)` and click `New Build` again on Buildkite. This will produce an actual PR (if one didn't exist already) to renovate those packages.

## Issue

Note that in https://github.com/haldunanil/renovate-conflicted-repro/blob/main/renovate.json, there is a key for `rebaseWhen` that is set to `conflicted`. However, if you go to the [issue that was created](https://github.com/haldunanil/renovate-conflicted-repro/pull/4), you'll note that it says:

> ♻ Rebasing: Whenever PR is behind base branch, or you tick the rebase/retry checkbox.

which is not the behavior expected when `rebaseWhen="conflicted"`.
