# OpenShift CI/CD Demo

Basic demonstration of OpenShift Pipelines for deploying applications across environments.

## Pipelines

![pipelines](./docs/images/pipelines.png)

## Demo

### Prerequisites

* OpenShift 4.1 or higher
* OpenShift Pipelines installed in OpenShift

### Projects

Create the projects (environments):

    oc apply -R -f ./environments

### Pipelines

Create the tasks:

    oc apply -R -f ./tasks

Create the pipelines:

    oc apply -R -f ./pipelines

Create the triggers (webhooks):

    oc apply -R -f ./triggers

Get the route for the webhook:

    export HELLO_EVENT_LISTENER_ROUTE=$(oc get route el-hello-event-listener -o jsonpath='{.spec.host}' -n hello-dev)

#### CI Pipeline (deploy to DEV building from develop branch)

A CI pipeline is started with a push event from the repository (Webhook). It can be simulated with the following command:

    curl -v http://$HELLO_EVENT_LISTENER_ROUTE \
        -H 'X-GitHub-Event: push' \
        -H 'X-Hub-Signature: sha1=db756b435cc9db6b36ba1e81561293a9dc61c465' \
        -H 'Content-Type: application/json' \
        -d '{"ref": "refs/heads/develop","head_commit": {"id": "master"},"repository": {"url": "https://github.com/shumonsharif/openshift-cicd-pipelines-demo"}}'

Note: the **head_commit.id** field is set to *master* just for demo reasons, in real scenarios the request will be generated by the repository with the correct values.

This action creates a PipelineRun:

![ci-pipeline-run](./docs/images/ci-pipeline-run.png)

#### CD Pipeline (deploy to TEST and PROD building from master branch)

A CD pipeline is started with a pull request event from the repository. It can be simulated with the following command:

    curl -v http://$HELLO_EVENT_LISTENER_ROUTE \
        -H 'X-GitHub-Event: push' \
        -H 'X-Hub-Signature: sha1=558de43f5f7bfe2800d879f77b28bf7f0e53796c' \
        -H 'Content-Type: application/json' \
        -d '{"ref": "refs/heads/master","head_commit": {"id": "master"},"repository": {"url": "https://github.com/shumonsharif/openshift-cicd-pipelines-demo"}}'

This action creates a PipelineRun:

![cd-pipeline-run](./docs/images/cd-pipeline-run.png)        
