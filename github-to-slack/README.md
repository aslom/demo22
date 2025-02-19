# Github to Slack

## Installing

Run these scripts, one after the others:
1. **0-setup.sh**: create a `kind` cluster and install core Knative Serving and Eventing using
   [`kn quickstart`](https://knative.dev/docs/install/quickstart-install/)
2. **1-addons.sh**: install the required add-ons to run the demo:
   * GitHubSource add-on
3. **3-deploy.sh**: deploy the demo. This scripts looks for 2 environment variables:
   * GITHUB_TOKEN: your GitHub [personal access token](https://github.com/settings/tokens)
   * SLACK_TOKEN: the [slack token](https://api.slack.com/authentication/oauth-v2) for your slack app.

## Event Topology

### Direct Delivery

```
GitHub -- (filter: issues, issue_comment) -> GitHub Adapter   
                                          -> Slack Sink App 
                                          -> Slack
```

Targeted slack channel: knativecon22-direct

### Kafka Channel

```
GitHub -- (filter: issues, issue_comment) -> GitHub Adapter 
                                          -> Kafka Channel
                                          -> Slack Sink App 
                                          -> Slack
```

Targeted slack channel: knativecon22-channel

## Scenarios

### Scenario 1

Show GitHub comments are being mirrored to a slack channel, no errors.

#### Steps

1. Create a GitHub issue. 
   * Title: `There is a bug.` 
   * Body: `It's not working`
2. Observe Slack notifications

### Scenario 2: ordering

### Steps

1. Add a comment to the previous created GitHub issue
   * Body: `first comment`
2. Add another comment (don't wait too long):
   * Body: `second comment`
3. In `knativecon22-direct` slack channel, observe the comments being out-of-order
3. In `knativecon22-channel` slack channel, observe the comments being in-order

### Scenario 3: retries

- special comment in github

GH -> GH Adapter -> SlackSinkAPP (send error) -> Slack

- the slack sink throw an error (cannot connect to backend database)

GH -> GH Adapter -> Kafka (with retry) -> SlackSinkAPP (send error) -> Slack

### Scenario 4: not losing events - using DLS

### Scenario 5: using broker vs channel

GH1 -(filter: issues, issue_comment) > ... GH Adapter -> Broker - (issues) > aggregations - does not care where it's coming from.
GH2 -(filter: issues, issue_comment) > ... GH Adapter -> Broker - (issues) > collect metrics?



## 
