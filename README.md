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

