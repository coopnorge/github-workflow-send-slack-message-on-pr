# Send Slack message on Pull Request

Reusable Github workflow for sending a message to a designated Slack channel whenever a Pull Request is opened or updated.


## Configuring your repository

You can change settings in the repository by manipulating configuration file in
`.pallet/gitconfig.yaml`

The full definition of the api can be found [here][gitconfig-api-ref]

CI of the changes in the repositoryconfig is not configured yet. For now, you
can use the [argocd app status][argocd-app-ref] to find the status and
potential errors.


[gitconfig-api-ref]: https://github.com/coopnorge/cloud-platform-apis/blob/main/cloud-platform-apis/templates/repositoryconfig.github.coop.no/definition.yaml
[argocd-app-ref]:  https://argocd.internal.coop/applications?search=pallet-github-workflow-send-slack-message-on-pr&showFavorites=false&proj=&sync=&autoSync=&health=&namespace=&cluster=&labels=
