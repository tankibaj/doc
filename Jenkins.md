# Branch

### dev

**Type:** Development.

**Actions:** Build | Test



### bug-fix

**Type:** Quick bug fix

**Actions:** Build | Test



### beta

**Type:** Pre-release

**Actions:** Build |Test | Deploy to beta server



### main

**Type:** Production

**Actions:** Build |Test | Deploy to production server



# Jenkins

#### Setp 01: Docker run

```bash
docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```



### Step 02: Create github access token with following permission:

Url: https://github.com/settings/tokens/new?scopes=repo,read:user,user:email,write:repo_hook

Permission:

`repo` = Select all | Full control of private repositories

`admin:org` = read:org | Read org and team membership, read org projects

`admin:repo_hook` = Select all | Full control of repository hooks

`user` = user:email | Access user email addresses (read-only)



### Step 03: Setup credentials on jenkins

`Manage Jenkins` => `Manage Credentials`

Setup two global credentials one as `secrect key text` another as `username password` (password is generated access token not github password)



### Step 03: Github access token setup on Jenkins:

`Manage Jenkins` => `Configure System` | GitHub



Ubuntu Permission

```bash
sudo usermod -a -G root jenkins
```

```
jenkins ALL=(ALL) NOPASSWD: ALL
```





# Jenkins Plugin

- [Workspace Cleanup Plugin](https://plugins.jenkins.io/ws-cleanup)
- [Docker Pipeline](https://plugins.jenkins.io/docker-workflow)
- [Blue Ocean](https://plugins.jenkins.io/blueocean)



