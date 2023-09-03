# Trigger Jenkins Job - A GitHub Action

The [Trigger Jenkins Job](https://github.com/mickeygoussetorg/trigger-jenkins-job) is a [composite run step custom action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-run-steps-action) that can be used to trigger a Jenkins job, wait for it to complete, and return the results.  Please make sure to read this README completely, as there are some caveats to using this action.

## Background

For many people who move to using GitHub Enteprise Cloud, but have a strong investment in Jenkins, the ability to continue to use Jenkins is required. However, for most people, Jenkins is not exposed to the Internet, so the ability to create a webhook to, say, trigger a Jenkins job on creation of a pull request is not possible.   We can, however, simulate a webhook by creating a GitHub Actions workflow, and using a self-hosted runner inside a company's network.

This action was designed to be used in that scenario, though it could be used in other scenarios as well.  The action itself is written as a Bash script, which is why a Linux runner is required. It makes use of `curl` commands against the Jenkins API to trigger and poll, hence the need for `curl` to be installed on the runner.

Please feel free to contribute back to this action if you are interested. 

## Caveats

The following are known issues with this action:

- The action only works on Linux runners
- The Linux runners must have `curl` installed
- Currently the action only works with jobs with no parameters. Parameters will be available in a future version

## Example Workflow

```
jobs:
  trigger-a-jenkins-job:
    runs-on: ubuntu-latest
    steps:
      - id: triggerjenkinsjob
        uses: mickeygoussetorg/trigger-jenkins-job@v1
        with:
          jenkins-server:  "${{ secrets.JENKINS_SERVER }}" # URL of the jenkins server. ex: http://myjenkins.acme.com:8080
          jenkins-job: "mySampleApp" # The name of the jenkins job to run
          jenkins-username: ${{ secrets.JENKINS_USERNAME }} # user name for accessing jenkins
          jenkins-pat: ${{ secrets.JENKINS_PAT }} # personal Access token for accessing Jenkins
          poll-time: 10 # how often (seconds) to poll the jenkins server for results
          timeout-value: 600 # How long (seconds) to poll before timing out the action
          verbose: true # true/false - turns on extra logging
          parameters: "PARAM1=VALUE PARAM2=VALUE" 
```

## Input Variables

Variable Name | Description
------------- | -----------
**jenkins-server** | Text string the URL to your Jenkins server. Example: http://myjenkins.acme.com:8080. It is suggested you put this value in a secret.
**jenkins-job** | Text string containing the name of the Jenkins job to execute
**jenkins-username** | Text string of username to access Jenkins. It is suggested you put this value in a secret
**jenkins-pat** | Text string of personal access token to access Jenkins. It is suggested you put this value in a secret
**poll-time** | Time, in seconds, of how often the action should poll the Jenkins job to see if it has completed
**timeout-value** | Time, in seconds, of how long the action should run before timing out, i.e. how long should the action wait for Jenkins to complete the job
**verbose** | true/false. This value, when true, enables extra logging in the build output. By default it is false, meaning minimal logging
**parameters** | A set of Jenkins build parameters to pass through on the job trigger. Example: "PARAM1=VALUE PARAM2=VALUE"
