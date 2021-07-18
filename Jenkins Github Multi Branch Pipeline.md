## GitHub App authentication

There are several reasons for considering this authentication approach including:

- The rate limit for a GitHub App scales with your organization size, whereas a user based token has a limit of 5000 requests per hour, regardless of how many repositories you have.
- For organizations that have 2-factor authentication enforced - with GitHub Apps there is no need to manage 2 factor authentication tokens for a 'bot' user.
- Jenkins GitHub Apps require a minimal, controlled set of privileges compared to a service user and its personal access token.

**GitHub App authentication can be used with Multibranch Pipeline jobs, Organization folders, and Shared Libraries. It it not available for regular Pipeline or Freestyle jobs.**

Setting up GitHub App authentication requires several steps in both GitHub and Jenkins. Complete the following steps in GitHub:

1. [Create the GitHub App](#Creating-the-GitHub-App)
2. [Generate a private key for authenticating to the GitHub App](#Generating-a-private-key-for-authenticating-to-the-GitHub-App)
3. [Install the GitHub App to your organization](#Installing-the-GitHub-App-to-your-organization)

Afterwards, complete the following steps in Jenkins:

1. [Add the Jenkins credential](#Adding-the-Jenkins-credential)
2. [Configure the GitHub Organization](https://docs.cloudbees.com/docs/cloudbees-ci/latest/cloud-admin-guide/github-app-auth#_configuring_the_github_organization)



### Creating the GitHub App

To create the GitHub App:

1. In the upper-right corner of any page in GitHub, click `your profile icon` => `Settings` => `Developer settings` => `GitHub Apps` => `New GitHub App` |  https://github.com/settings/apps
3. Select `New GitHub App`.
4. Complete the following fields as follows:
   - **GitHub App Name:** enter an appropriate name to reference this authentication, i.e. `cicd-pipeline`.
   - **Homepage URL:** enter your company domain or GitHub repository URL.
   - **Webhook URL:** enter your Jenkins instance URL such as `https://<jenkins-host>/github-webhook/`.
5. You do not need to complete any of the fields in the **Identifying and authorizing users** section.
6. Under **Repository permissions**, choose the following permissions. For each type of permission, use the drop-down menu to select `Read-only`, `Read & write`, or `No access`.
   - **Administration:** `Read-only`
   - **Checks:** `Read & write`
   - **Contents:** `Read-only` (to read the `Jenkinsfile` and the repository content during `git fetch`).
   - **Metadata:** `Read-only`
   - **Pull requests:** `Read-only`
   - **(Optional) Webhooks:** If you want the plugin to manage webhooks for you, `Read & write`.
   - **Commit statuses:** `Read & write`

7. Under **Subscribe to events**, select the following events:

   - Check run
   - Check suite
   - Pull request
   - Push
   - Repository

   Note: If you do not see an event listed, check that you have applied the appropriate Repository permissions in the previous step. The events are added to the list based on the permissions selected.

8. Select the appropriate choice for **Where can this GitHub App be installed?**

   - **Only on this account:** If you only want to use the GitHub App for a single GitHub account.

   - **Any account:** If you want to use the GitHub App for multiple organizations.

   Note: If you select Any account, you will need one credentials entry in CloudBees CI per organization.  


9. Clickon `Create GitHub App`.



### Generating a private key for authenticating to the GitHub App

After you have created the GitHub App, you will need to generate a private key for authenticating to the GitHub App.

To generate a private key authenticating to the GitHub App:

1. In the upper-right corner of any page in GitHub, click `your profile icon` => `Settings` => `Developer settings` => `GitHub Apps` => `New GitHub App` |  https://github.com/settings/apps
2. Select the GitHub App.
3. Under **Private keys**, click on `Generate a private key` option.
4. A private key in PEM format will be downloaded to your computer.



### Converting the private key for Jenkins

After you have generated the private key authenticating to the GitHub App, you need to convert the key into a different format that Jenkins can use with the following command:

```bash
openssl pkcs8 -topk8 -inform PEM -outform PEM -in key-in-your-downloads-folder.pem -out converted-github-app.pem -nocrypt
```



### Installing the GitHub App to your organization

Finally, you must install the newly created app to your organization.

To install the newly created GitHub App to your organization:

1. From the **GitHub Apps** settings page, select the GitHub App.
2. In the left navigation, select **Install App**.
3. Select **Install** next to the organization or user account containing the correct repository.
4. Install the app on all repositories or select repositories.
5. Once installed, you will have configuration options for the app on your selected account.



### Adding the Jenkins credential

To add the Jenkins credential in the UI:

1. Go to: `Manage Jenkins` => `Manage Credentials`

2. Select the global credential store.

   ![cred store](https://i.imgur.com/kuzgTrd.png)

3. Click on `Add Credentials`.

4. Select `GitHub App` as the `Kind`.

5. For the `ID`, enter the `GitHub App Name` you created in [Creating the GitHub App](https://docs.cloudbees.com/docs/cloudbees-ci/latest/cloud-admin-guide/github-app-auth#_creating_the_github_app).

6. For `App ID` enter the `GitHub App ID`.

   ![github apps settings](https://docs.cloudbees.com/docs/cloudbees-common/latest/_images/github-app-auth/github-apps-settings.6a25994.png)

7. Select the `Add` button to enter the [converted private key](#Converting-the-private-key-for-Jenkins) in the `Key` field.

8. (Optional) Click on the `Advanced` button if you have installed the same GitHub App on multiple organizations, and enter the name of the organization or user in the `Owner` field.

9. Click on `OK`.



### Configuring the GitHub Organization

To configure your GitHub Organization to use your GitHup App for authentication:

1. From the GitHub Organization, select **Configure** in the left navigation.

2. Select the **Projects** tab.

3. Select the new GitHub App credentials from the **Credentials** drop down in the **GitHub Organization** section. `GHApp verified, remaining rate limit: 5000` displays.

4. Select **Save**.

5. In the left navigation, select **Scan Organization Now**.

6. View the **Scan Organization log** and verify that the success message displays.







Source: https://www.youtube.com/watch?v=ZWwmh4gqia4&t=147s