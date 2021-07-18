

A system monitor is a hardware or software component used to monitor system resources and performance in a computer. Its main goal is to analyze the computer’s operation and performance, and to detect and alert about possible errors. Such systems give us the ability to find out what is happening on our machine at any given time. Whether it’s the percentage of system’s resources currently used, what commands are being run, or who is logged on.

**Prometheus** is a free software application used for event monitoring and alerting. It records real-time metrics in a time series database built using a HTTP pull model, with flexible queries and real-time alerting.

**Grafana** is a multi-platform open source analytics and interactive visualization web application. It provides charts, graphs, and alerts for the web when connected to supported data sources.

Our goal is install `Prometheus` and `Grafana` on `Ubuntu Server 20.04` to  monitor system resources and performance.

</br>

## Installing Grafana

In this first step, you will install Grafana onto your Ubuntu 20.04 server. You can install Grafana either by [downloading it directly from its official website](https://grafana.com/grafana/download) or by going through an APT repository. Because an APT repository makes it easier to install and manage Grafana’s updates, you’ll use that method in this tutorial.

Download the Grafana `GPG` key with `wget`, then `pipe the output` to `apt-key`. This will add the key to your APT installation’s list of trusted keys, which will allow you to download and verify the GPG-signed Grafana package:

```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

Next, add the Grafana repository to your APT sources:

```bash
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
```

Refresh your APT cache to update your package lists:

```bash
sudo apt update
```

You can now proceed with the installation:

```bash
sudo apt install grafana
```

Once Grafana is installed, use `systemctl` to start the Grafana server:

```bash
sudo systemctl start grafana-server
```

Next, verify that Grafana is running by checking the service’s status:

```bash
sudo systemctl status grafana-server
```

We could check Grafana TCP connection listen on port 3000

```bash
sudo netstat -tulpn | grep grafana-serve
```

Lastly, enable the service to automatically start Grafana on boot:

```bash
sudo systemctl enable grafana-server
```

</br>

## Installing Prometheus

We are going to install Prometheus using the APT tool to install it:

```bash
sudo apt-get install prometheus prometheus-node-exporter
```

We could check `Prometheus` `Prometheus-node-exporter` TCP connections listen on port 9090 and 9100

```bash
sudo netstat -tulpn | grep prometheus
sudo netstat -tulpn | grep prometheus-no
```

<br/>

## Login into the Grafana

Start by navigating to `http://localhost:3000` from your web browser. This will bring up the default login screen where you’ll see the Grafana logo, a form asking you to enter an **Email or username** and **Password**, a **Log in** button, and a **Forgot your password?** link.

![](https://i.imgur.com/fyZf1qZ.png)

Enter `admin` into both the **Email or username** and **Password** fields and then click on the **Log in** button.

On the next screen, you’ll be asked to make your account more secure by changing the default password:

![](https://i.imgur.com/iSKnYiP.png)

From here, you can click **Submit** to save the new information or press **Skip** to skip this step. If you skip, you will be prompted to change the password next time you log in.

In order to increase the security of your Grafana setup, click **Submit**. You’ll go to the **Welcome to Grafana** dashboard:

![](https://i.imgur.com/U5TVXYT.png)

You’ve now secured your account by changing the default credentials. Next, you will make changes to your Grafana configuration so that nobody can create a new Grafana account without your permission.

<br/>

## Setting up the Prometheus Data Source on Grafana

Grafana needs to add **Prometheus** data sources, click on `Configuration` > `Data Sources`on the left menu.

![](https://i.imgur.com/vLd35qw.png)

Click on `Add Data Source`

![](https://i.imgur.com/AjsjQmn.png)

Select `Promethus`

![](https://i.imgur.com/NOSwhdA.png)

Input Prometheus Server endpoint URL  `http://localhost:9090` on [HTTP] field 

![](https://i.imgur.com/cL9lM0C.png)

Scroll down, click [Save & Test] button.

![](https://i.imgur.com/ymXCq3u.png)

<br/>

## Creating Grafana Dashboard

Grafana has lots of Grafana Dashboards created by different users which are shared in the [Grafana Dashboard](https://grafana.com/dashboards) URL. I use the “[Node Exporter Full](https://grafana.com/dashboards/7039)” Dashboard, so I only have to add the [**1860**](https://grafana.com/grafana/dashboards/1860) ID in the import Dashboard tool of Grafana.

Next, Click on `+` from left menu then click `Import`

![](https://i.imgur.com/pDnvfc2.jpg)

Input Dashboad ID [**1860**](https://grafana.com/grafana/dashboards/1860) on Import via grafana.com section and click on `Load`

![](https://i.imgur.com/gJ8NJMk.png)

Select Prometheus data source as `Prometheus` from dropdown menu then click on `Import`

Optional: You can also change `Name` and `UID` if you want.

![](https://i.imgur.com/d2r2cVH.png)

Finally, we can see our colorful Dashboard

![](https://i.imgur.com/F6r4tjm.png)

</br>

## Disabling Grafana Registrations and Anonymous Access

Grafana provides options that allow visitors to create user accounts for themselves and preview dashboards without registering. When Grafana isn’t accessible via the internet or when it’s working with publicly available data like service statuses, you may want to allow these features. However, when using Grafana online to work with sensitive data, anonymous access could be a security problem. To fix this problem, make some changes to your Grafana configuration.

Start by opening Grafana’s main configuration file for editing:

```bash
sudo nano /etc/grafana/grafana.ini
```

 Locate the following `allow_sign_up` directive under the `[users]` heading:

```bash
...
[users]
# disable user signup / registration
;allow_sign_up = true
...
```

Enabling this directive with `true` adds a **Sign Up** button to the login screen, allowing users to register themselves and access Grafana.

Disabling this directive with `false` removes the **Sign Up** button and strengthens Grafana’s security and privacy.

Uncomment this directive by removing the `;` at the beginning of the line and then setting the option to `false`:

```bash
...
[users]
# disable user signup / registration
allow_sign_up = false
...
```

Next, locate the following `enabled` directive under the `[auth.anonymous]` heading:

```bash
...
[auth.anonymous]
# enable anonymous access
;enabled = false
...
```

Setting `enabled` to `true` gives non-registered users access to your dashboards; setting this option to `false` limits dashboard access to registered users only.

Uncomment this directive by removing the `;` at the beginning of the line and then setting the option to `false`.

```bash
...
[auth.anonymous]
# enable anonymous access
enabled = false
...
```

 Save the file and exit your text editor. To activate the changes, restart Grafana:

```bash
sudo systemctl restart grafana-server
```

Verify that everything is working by checking Grafana’s service status:

```bash
sudo systemctl status grafana-server
```

 Like before, the output will report that Grafana is `active (running)`.

Now, point your web browser to `http://localhost:3000`. To return to the **Sign Up** screen, bring your cursor to your avatar in the lower left of the screen and click on the **Sign out** option that appears.

Once you have signed out, verify that there is no **Sign Up** button and that you can’t sign in without entering login credentials.

At this point, Grafana is fully configured and ready for use. Next, you can simplify the login process for your organization by authenticating through GitHub.

</br>

## Setting Up a GitHub OAuth App

For an alternative approach to signing in, you can configure Grafana to authenticate through GitHub, which provides login access to all members of authorized GitHub organizations. This can be particularly useful when you want to allow multiple developers to collaborate and access metrics without having to create Grafana-specific credentials.

Start by logging in to a GitHub account associated with your organization and then navigate to your GitHub profile page at [`https://github.com/settings/profile`](https://github.com/settings/profile).

Switch settings context by clicking on your name on the left side of the screen then selecting your organization in the dropdown menu. This will switch context from **Personal settings** to **Organization settings**.

On the next screen, you’ll see your **Organization profile** where you can change settings like your **Organization display name**, organization **Email**, and organization **URL**.

Because Grafana uses [OAuth](https://oauth.net/)—an open standard for granting remote third parties access to local resources—to authenticate users through GitHub, you’ll need to create a new [OAuth application within GitHub](https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/).

Click the **OAuth Apps** link under **Developer settings** on the lower left-hand side of the screen.

If you don’t already have any OAuth applications associated with your organization on GitHub, you’ll be told there are **No Organization Owned Applications**. Otherwise, you’ll see a list of the OAuth applications already connected to your account.

Click the **New OAuth App** button to continue.

On the next screen, fill in the following details about your Grafana installation:

- **Application name** - This helps you distinguish your different OAuth applications from one another.
- **Homepage URL** - This tells GitHub where to find Grafana. Type `https://your_domain` into this field, replacing `your_domain` with your domain.
- **Application Description** - This provides a description of your OAuth application’s purpose.
- **Application callback URL** - This is the address where users will be sent once successfully authenticated. For Grafana, this field must be set to `https://your_domain/login/github`.

Keep in mind that Grafana users logging in through GitHub will see the values you entered in the first three preceding fields, so be sure to enter something meaningful and appropriate.

When completed, the form will look something like:

![GitHub Register OAuth Application](https://assets.digitalocean.com/articles/66778/GitHub_Register_OAuth_Application.png)

Click the green **Register application** button.

You will now be redirected to a page containing the **Client ID** and **Client Secret** associated with your new OAuth application. Make note of both values, because you will need to add them to Grafana’s main configuration file to complete the setup.

**Warning:** Make sure to keep your **Client ID** and **Client Secret** in a secure and non-public location, because they could be used as the basis of an attack.

With your GitHub OAuth application created, you’re now ready to reconfigure Grafana to use GitHub for authentication.

</br>

## Configuring Grafana as a GitHub OAuth App

To complete GitHub authentication for your Grafana setup, you will now make some changes to your Grafana configuration files.

To begin, open the main Grafana configuration file.

```bash
sudo nano /etc/grafana/grafana.ini
```

Locate the `[auth.github]` heading, and uncomment this section by removing the `;` at the beginning of every line except `;allowed_domains =` and `;team_ids =`, which will not be changed in this tutorial.

Next, make the following changes:

- Set `enabled` and `allow_sign_up` to `true`. This will enable GitHub Authentication and permit members of the allowed organization to create accounts themselves. Note that this setting is different from the `allow_sign_up` property under `[users]` that you changed.
- Set `client_id` and `client_secret` to the values you got while creating your GitHub OAuth application.
- Set `allowed_organizations` to the name of your organization to ensure that only members of your organization can sign up and log in to Grafana.

The complete configuration will look like this:

```bash
...
[auth.github]
enabled = true
allow_sign_up = true
client_id = your_client_id_from_github
client_secret = your_client_secret_from_github
scopes = user:email,read:org
auth_url = https://github.com/login/oauth/authorize
token_url = https://github.com/login/oauth/access_token
api_url = https://api.github.com/user
;allowed_domains =
;team_ids =
allowed_organizations = your_organization_name
...
```

 You’ve now told Grafana everything it needs to know about GitHub. To complete the setup, you’ll need to enable redirects behind a reverse proxy. This is done by setting a `root_url` value under the `[server]` heading.

```bash
...
[server]
root_url = https://your_domain
...
```

 Save your configuration and close the file. Then, restart Grafana to activate the changes:

```bash
sudo systemctl restart grafana-server
```

Lastly, verify that the service is up and running.

```bash
sudo systemctl status grafana-server
```

The output will indicate that the service is `active (running)`. Now, test your new authentication system by navigating to `https://your_domain`. If you are already logged in to Grafana, hover your mouse over the avatar log in the lower left-hand corner of the screen, and click on **Sign out** in the secondary menu that appears next to your name.

On the login page, you’ll see a new section under the original **Log in** button that includes a **Sign in with GitHub** button with the GitHub logo.

![Grafana Login page with GitHub](https://assets.digitalocean.com/articles/67242/grafana_login_github.png)

Click on the **Sign in with GitHub** button to be redirected to GitHub, where you’ll sign in to your GitHub account and confirm your intention to **Authorize Grafana**.

Click the green **Authorize your_github_organization** button.

**Note:** Make sure your GitHub account is a member of your approved organization and your Grafana email address matches your GitHub email address. If you try to authenticate with a GitHub account that isn’t a member of your approved organization, you’ll get a **Login Failed** message telling you **User not a member of one of the required organizations**.

You will now be logged in with your existing Grafana account. If a Grafana account doesn’t already exist for the user you logged in as, Grafana will create a new user account with **Viewer** permissions, ensuring that new users can only use existing dashboards.

To change the default permissions for new users, open the main Grafana configuration file for editing.

```bash
sudo nano /etc/grafana/grafana.ini
```

 Locate the `auto_assign_org_role` directive under the `[users]` heading, and uncomment the setting by removing the `;` at the beginning of the line.

Set the directive to one of the following values:

- `Viewer` — can only use existing dashboards
- `Editor` — can use, modify, and add dashboards
- `Admin` — has permission to do everything

This tutorial will set the auto-assign to `Viewer`:

```bash
...
[users]
...
auto_assign_org_role = Viewer
...
```

 Once you’ve saved your changes, close the file and restart Grafana:

```bash
sudo systemctl restart grafana-server
```

Check the service’s status:

```bash
sudo systemctl status grafana-server
```

Like before, the status will read `active (running)`.

At this point, you have fully configured Grafana to allow members of your GitHub organization to register and use your Grafana installation.