Git Deploy
==========

Bash script to automatically deploy repositories to local or remote environments when pushing a branch. Deployments are created using symbolic links named after their branch. Compatible with Linux and macOS.

Installation
------------

You only need to install the script on systems on which you are planning to set up deployment remotes for a repository. The actual deployment process is handled by hooks within the remote environment.

Clone this repository into a temporary directory.

```sh
git clone https://github.com/lrdn/git-deploy.git
```

Create the required system directories should they not exist already.

```sh
sudo install -d -m 755 /usr/local/bin
sudo install -d -m 755 /usr/local/share/git-deploy/hooks
```

Place the executable script and deployment hooks in their respective directories.

```sh
sudo install -m 755 git-deploy/bin/git-deploy /usr/local/bin/git-deploy
sudo install -m 644 git-deploy/hooks/post-receive /usr/local/share/git-deploy/hooks/post-receive
sudo install -m 644 git-deploy/hooks/pre-receive /usr/local/share/git-deploy/hooks/pre-receive
```

Configuration
-------------

Enter your source repository to configure a deployment target specifying an environment and location. Environments are simply remotes and created automatically. The location can either be a local path or an SSH URL.

```sh
git deploy production ssh://user@example.com:/var/www/app
```

The setup routine will spawn an ``ssh-agent`` whenever you create a remote environment. Only public key authentication with the default identity is currently supported. You can however launch a custom shell instance beforehand which the setup will then use instead to establish the connection.

Deployment
----------

Push an environment branch to deploy your application at the target location.

```sh
git push production master
```

Your application will be deployed using the symbolic link ``/var/www/app/production/master`` referencing the directory containing the actual commit work tree. This allows you to deploy branches individually within environments.

Build Automation
----------------

The deployment process can automatically perform build tasks by including a ``build.sh`` script within your repository. You can easily distinguish where the application was deployed to by checking the environment variables listed in the following example. This can be particularly useful to avoid accidental builds within your development environment.

```sh
#!/usr/bin/env bash

echo "GIT_DEPLOY_BRANCH=${GIT_DEPLOY_BRANCH}"
echo "GIT_DEPLOY_WORK_TREE=${GIT_DEPLOY_WORK_TREE}"
echo "GIT_DEPLOY_ENVIRONMENT=${GIT_DEPLOY_ENVIRONMENT}"

exit 0
```