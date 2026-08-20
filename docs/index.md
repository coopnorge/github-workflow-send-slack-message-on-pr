# github-workflow-send-slack-message-on-pr

A reusable GitHub workflow that sends a message in a target Slack channel
whenever a Pull Request is created, and updates said message whenever the state
of the PR changes.

## Goals

The workflow is split into two jobs: one that checks for an existing message
(either from cache or the channel history), and one that creates or updates the
message depending on the first job's results.

For `check-existing-message`:
* Check cache for a PR-specific entry that would hold the Slack message
identifier (timestamp)
* If cache entry is not found, search the channel for messages made by the app
that would contain a link to the PR
* If the message is found, save the timestamp to the cache

For `create-or-update-message`:
* Check cache for a PR-specific entry that would hold the Slack message
identifier (timestamp)
* If cache entry is found, update message with new PR state
* If cache entry is not found or the update fails, post a new message containing PR state
* If the message is new, save the timestamp to the cache

GitHub Actions cache entries have a default lifetime of 90 days, however they
can be automatically deleted if the cache size limit in the repository
is exceeded. Therefore, we use an API call to fetch the history of the Slack channel
as a fallback to avoid repeated posts of the same PR.

## Limitations

The API call to fetch channel history in `check-existing-message` is currently limited
to 100 entries between the current time and PR creation time. It is therefore
theoretically possible that in a particularly busy channel the workflow would
repost older PR messages, though it is unlikely.

## Usage

### Slack app

In order to post messages into the channel, you'll need a [Slack app](https://api.slack.com/apps) to be set up in the
workspace and invited to the target channel.

Its minimum required permissions are:

* `chat:write`
* `channels:history`
* `groups:history`
* `im:history`
* `mpim:history`

### Inputs

```yaml
inputs:
  channel-id:
    description: "Slack channel ID to post messages"
    required: true
    type: string
  cache-key-prefix:
    description: "Cache key prefix for saving message identifier (timestamp)"
    required: false
    default: "SLACK_TS_"
    type: string

secrets:
  slack-token:
    description: "Slack bot token"
    required: true
```

This workflow can be added to your workflow as follows:

```yaml
jobs:
  # <some other jobs>
  example-ci:
    name: "Example CI"
    uses: coopnorge/github-workflow-send-slack-message-on-pr/.github/workflows/send-slack-message-on-pr.yaml@v1.0.0
    with:
      channel-id: YOUR-SLACK-CHANNEL-ID
      cache-key-prefix: CUSTOM-CACHE-KEY-PREFIX-
    secrets:
      slack-token: ${{ secrets.YOUR_SLACK_BOT_TOKEN }}
  # <some other jobs>
```
