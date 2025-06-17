## Prerequisites
* Git. You need to know how to git clone, pull, push and commit.
* Make - it should be installed by default on Linux/Mac. If you have Windows, install it from sourceforge. Note - you won't need any previous experience in using make for this workshop.
* Node 12 or thereabouts.
* Docker.
* A Github account.
* A PactFlow account.

The project uses a Makefile to simulate a very simple build pipeline with two stages - test and deploy.

* Test
  * Run tests (including the pact tests that generate the contract)
  * Publish pacts, associating the consumer version with the name of the current branch
  * Check if we are safe to deploy to prod (ie. has the pact content been successfully verified)
* Deploy (only from master)
  * Deploy app (just pretend for the purposes of this example!)
  * Record the deployment in the Pact Broker


## Step by step

In Github:

1. Open your forked example-consumer project (https://github.com/<your-username>/example-consumer)
2. Click on the **Actions** tab.
3. Click the button with the text **"I understand my workflows, go ahead and enable them"**
4. Under the **Workflows** menu on the left, select **Build**.
5. You will see the text **This workflow has a workflow_dispatch event trigger**. To the right of that text is a button with the label **Run workflow**. Click the button.
6. Leave the branch as **maste**r and click the green **Run workflow** button.

*The build will fail with an authentication error when it tries to publish the pact - that's expected. We need to update the configuration to point it at your new PactFlow account.*

## Configure consumer pipeline

The source repositories are configured to use the the public broker at test.pactflow.io. You will need to update the credentials to point to your own PactFlow account. To do this, we need to update the **PACT_BROKER_BASE_URL** environment variable in the Github workflow file, and create a Github Secret to store the PactFlow API token in.

1. Create a Github Secret to store your PactFlow API token in.
* In PactFlow:
     * Log in to your PactFlow account (**https://<your-subdomain>.pactflow.io**), and go to Settings > API Tokens.
     * Click the Copy button for the read/write CI token (make sure it's the read write one, not the read only one).
* In Github:
     * Open your forked **example-consumer** project (**https://github.com/<your-username>/example-consumer**)
     * Click on the **Settings** tab.
     * Open the **Secrets and variables** dropdown in the side menu.
     * Select Actions from the dropdown
     * Click the **New repository secret** button
     * Set the name of the secret to **PACTFLOW_TOKEN_FOR_CI_CD_WORKSHOP**
     * Paste in the PactFlow API token value you copied in the previous step.
2. Update your workflow files in GitHub to point at your PactFlow Broker
* In PactFlow:
     * Go to Settings > API Tokens.
     * Click the **COPY PACTFLOW BASE URL** button
* In Github:
     * Open your forked **example-consumer** project (**https://github.com/<your-username>/example-consumer**)
     * Open **.github/workflows/build.yml**
     * In the upper right corner of the file view, click 🖊️ to open the file editor.
     * Update the value of **PACT_BROKER_BASE_URL** to the base URL of your own PactFlow account. You can easily get this by clicking the COPY PACTFLOW BASE URL button on the API Tokens page in PactFlow.
     * Press the green **Commit changes** button
3. View the build:
* In Github:
     * Select the most recent build, this will have been triggered when you committed the changes in the last page

*This build should now successfully publish the pact, but it will fail on the can-i-deploy step before it tries to deploy. This is because the provider has not published a successful verification result for the pact.*


## Configure provider pipeline

The source repositories are configured to use the the public broker at test.pactflow.io. You will need to update the credentials to point to your own PactFlow account. To do this, we need to update the **PACT_BROKER_BASE_URL** environment variable in the Github workflow file, and create a Github Secret to store the PactFlow API token in.

1. Create a Github Secret to store your PactFlow API token in.
* In PactFlow:
     * Log in to your PactFlow account (**https://<your-subdomain>.pactflow.io**), and go to Settings > API Tokens.
     * Click the Copy button for the read/write CI token (make sure it's the read write one, not the read only one).
* In Github:
     * Open your forked **example-provider** project (**https://github.com/<your-username>/example-provider**)
     * Click on the **Settings** tab.
     * Open the **Secrets and variables** dropdown in the side menu.
     * Select Actions from the dropdown
     * Click the **New repository secret** button
     * Set the name of the secret to **PACTFLOW_TOKEN_FOR_CI_CD_WORKSHOP**
     * Paste in the PactFlow API token value you copied in the previous step.
2. Update your workflow files in GitHub to point at your PactFlow Broker
* In PactFlow:
     * Go to Settings > API Tokens.
     * Click the **COPY PACTFLOW BASE URL** button
* In Github:
     * Open your forked **example-provider** project (**https://github.com/<your-username>/example-provider**)
     * Open **.github/workflows/build.yml**
     * In the upper right corner of the file view, click 🖊️ to open the file editor.
     * Update the value of **PACT_BROKER_BASE_URL** to the base URL of your own PactFlow account. You can easily get this by clicking the COPY PACTFLOW BASE URL button on the API Tokens page in PactFlow.
     * Press the green **Commit changes** button
     * Open **.github/workflows/contract_requiring_verification_published.yml**
     * In the upper right corner of the file view, click 🖊️ to open the file editor.
     * Update the value of **PACT_BROKER_BASE_URL** to the base URL of your own PactFlow account. You can easily get this by clicking the COPY PACTFLOW BASE URL button on the API Tokens page in PactFlow.
     * Press the green **Commit changes** button
3. View the build:
* In Github:
     * Select the most recent build, this will have been triggered when you committed the changes in the last page

*After you have pushed your changes to the workflow files, the provider pipeline will run, fetching and verifying the configured pacts from your PactFlow account, and publishing the results back. The can-i-deploy command will pass, and allow the provider to be deployed.*


## Back to the consumer
To make both your builds go green, we are going to retry can-i-deploy, now that the consumer is deployed to production.

1. Find the latest failing **example-consumer** workflow in the Github Actions page (**Actions** -> Under **Workflows**, select **Build** -> **failing build**).
2. In the top right on of the failing job page, select to Re-run the failed jobs (can-i-deploy) (**Actions** -> Under **Re-run jobs**, select **Re-run failed jobs**).
3. The test run should be pre-passing
4. The **can-i-deploy** step should now pass - our consumer is safe to deploy, now that our provider published a verification result, and is deployed to production
5. If you click on the step you should see a successful verification result, and **Computer says yes \o/**
6. The **deploy** step should pass too - our consumer is deployed to production, and we record this with the **record-deployment command**, marking this application version of our consumer as deployed to **production**

*Both consumer and provider builds passing*
