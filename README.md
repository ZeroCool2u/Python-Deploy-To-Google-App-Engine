# Python-Deploy-To-Google-App-Engine: A GitHub Action to auto deploy to Google App Engine.

This repo is an example workflow for automatically deploying to Google App Engine if your unit tests pass. This example uses [pytest](https://docs.pytest.org/en/latest/), but you can of course change this to whatever testing framework your project uses. It's as simple as changing the commandline arguments in the relevant step. Before you touch the workflow file there are a few steps below that you'll need to follow. If you've ever deployed your app before, you're probably already familiar with this process. If not, check out the [App Engine docs](https://cloud.google.com/appengine/docs/standard/python3/testing-and-deploying-your-app), they're both well written and extensive! Finally, this workflow was designed with Python in mind, but there's absolutely nothing preventing you from extending it. In fact, beyond the call to `pytest` and setting up Python itself, you should be able to use this out of the box for another languages runtime as long as your `app.yaml` file is configured appropriately.

There are a couple preliminary steps that you'll need to take prior to creating your deployment workflow. 

1. Create a service account and download the json private key file. Be sure you don't commit this file to your repo on accident! Best practice is to minimize the number of permissions this service account has, but for getting setup, you can give the service account project owner permissions and figure out exactly what scope is appropriate later. 
Click here for docs on creating a service account: https://cloud.google.com/iam/docs/creating-managing-service-account-keys#iam-service-account-keys-create-console 
2. Open a new terminal and cd into the folder your keyfile is located in, then run the following command: 

  `base64 /path_to_your/key_files.json`

3. Copy and paste the resulting output into a new Secret, which you can create in your repos settings under Secrets. Name the secret GCLOUD_AUTH. 
4. If you use any GCP services in your application, such as Cloud Pub/Sub or any other GCP service and you're running tests with pytest or any other framework, you probably have your application setup to use local `APPLICATION_DEFAULT_CREDENTIALS`. This is fine, but you'll need to create one more secret, so that they're readable by your application within the GitHub Action Docker container. This is accomplished by [writing the credentials directly to a file from the environment variable that stores the secret](https://github.com/ZeroCool2u/Python-Deploy-To-Google-App-Engine/blob/ec123b91500995e8ff62bf9a97b4f08066250355/example.yml#L28). To do this, create one more secret, called APPLICATION_CREDENTIALS and paste in the contents of the same file you just converted to base64, just don't convert it to base64 this time. We could probably accomplish this in one step, but the GCP Auth action we use at the beginning expects the key to be in base64 format and we can keep this workflow modular by seperating the two completely.
4. Next, you'll need to enable the [App Engine Admin API](https://cloud.google.com/appengine/docs/admin-api/accessing-the-api), so you can interact with it programatically. You may need to give it a few seconds before this is enabled successfully.   
5. Finally, you should have this already if you're using App Engine, but make sure you have your App Engine `app.yaml` file prepared. I would also highly recommend creating a `.gcloudignore` and `.gitignore` file if you haven't already to ensure your service account key file isn't committed/pushed to Github or uploaded to App Engine.
6. Copy the other file in this Gist, main.yml into your repository.

  `mkdir your/repo/.github/workflows`

  `touch your/repo/.github/main.yml`

Open this newly created file and paste the contents of the example workflow. The only thing you need to do is to make sure your [app.yaml file is named correctly](https://github.com/ZeroCool2u/Python-Deploy-To-Google-App-Engine/blob/ec123b91500995e8ff62bf9a97b4f08066250355/example.yml#L38) and your GCP [project name is set](https://github.com/ZeroCool2u/Python-Deploy-To-Google-App-Engine/blob/ec123b91500995e8ff62bf9a97b4f08066250355/example.yml#L35). 
