This API Builder project implements the Amplify Central Agent Status Change Notifier described [here](https://blog.axway.com/apis/amplify-central-agent-status-change-notifier) but implemented using Axway's API Builder.

The project requires the following environment variables:
* **API_KEY** - an API Builder API Key that you provide. You pass this key as a header using apikey as the key and the value you enter here. This is configured in your Integration Resource definition
* **MS_TEAMS_WEBHOOK_URL** - the URL for your Microsoft Teams Incoming Webhook Connector
* **API_CENTRAL_URL** - The base path for your Amplify Central (defaults to https://apicentral.axway.com)

This API Builder project exposes one API:
* `/api/amplifycentralwebhookhandler` - triggered by Integration Resource Webhook for when an an Agent's status changes (i.e. stops running)

Set your Integration Resource to point to the API Builder API as follows:

  agentstatuschangesint.yaml

  ```
  name: agentstatuschangesint
  kind: Integration
  apiVersion: v1alpha1
  title: Agents Status Changes Integration
  tags:
  - Agents
  spec:
    description: Agents Status Changes Integration
  ---
  name: agentstatuschangesintwh
  kind: Webhook
  apiVersion: v1alpha1
  title: Agents Status Changes Integration Webhook
  attributes:
  release: 1.0.0
  metadata:
    scope:
      kind: Integration
      name: agentstatuschangesint
  spec:
    enabled: true
    url: <BASE ADDRESS OF YOUR API BUILDER PROJECT>/api/amplifycentralwebhookhandler
    headers:
        apikey: <YOUR API KEY ENV VAR VALUE>
  ---
  group: management
  apiVersion: v1alpha1
  kind: ResourceHook
  name: agentstatuschangesintrh
  title: Agents Status Changes Integration Resource Hook
  metadata:
    scope:
      kind: Integration
      name: agentstatuschangesint
  spec:
    triggers:
      - group: management
        kind: DiscoveryAgent
        name: "*"
        scope:
          kind: Environment
          name: "*"
        type:
        - updated
      - group: management
        kind: TraceabilityAgent
        name: "*"
        scope:
          kind: Environment
          name: "*"
        type:
        - updated
    webhooks:
      - agentstatuschangesintwh
  ```
