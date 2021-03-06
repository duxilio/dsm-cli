```
Usage: dsm [add | rm | show | pull | push] [args...]

COMMANDS
    add <reference> <secret> [<fields...>]  encrypts and adds a secret to the manager
                                            next to the main secret/field you can optionally
                                            also add other fields using the NAME=VALUE syntax
                                            e.g. dsm add kitten.io secretstr user=kitten token=123
    rm <reference>                          removes a secret from the manager
    show [<reference>] [<field>]            either shows all encrypted secrets or decrypts and shows a specific secret
    pull                                    git pulls from the remote secrets repo
    push                                    git pushes to the remote secrets repo

ENV vars
    DSM_PASSWORD                            the master password
    DSM_REMOTE_SECRETS_PATH                 the path to the remote secrets repo
```

### Setup

#### \#1. Installation
```
npm i -g dsm-cli
```
#### \#2. Environment Variables
In order to use the secrets manager you need to configure your master password and the path to the remote secrets git repository. The easiest way to do this is by adding these enviourment variables to your `rc` file.
```
export DSM_PASSWORD="123"
export DSM_REMOTE_SECRETS_PATH="user@server:/path/to/dsm-secrets.git"
```

### Secrets Git Repository
The remote secrets repository is there so you and your colleagues are able to sync the passwords between computers. We like to keep the repository in a place as secure as possible by simply creating a barebone Git repo on a server.
```
mkdir -p dsm-secrets.git && cd dsm-secrets.git
git init --bare
```

Now the repository is created but it doesn't have a master branch yet. Clone the repo, add the `secrets.json` file and push to the remote repo. *Note: make sure the repo has the right permissions for you to be able to push to it*
```
git clone user@server:/path/to/dsm-secrets.git
cd dsm-secrets
echo '{}' > secrets.json
git add :/
git commit -m "adds secrets.json"
git push origin master
```

Now make sure your local enviourment variables are set up to be able to sync with your new repo.
```
export DSM_REMOTE_SECRETS_PATH="user@server:/path/to/dsm-secrets.git"
```

In case your server doesn't have Git you can also create the repo locally and then `scp` or `rsync` it to your server.

### Troubleshooting

#### Secrets Permission Denied
```
Secrets repository not available locally. Trying to clone user@server:/path/to/dsm-secrets.git...
 fatal: could not create work tree dir 'secrets'.: Permission denied
```
You probably installed the package using `sudo` which means it's owned by the `sudo` user. This can be fixed by changing the owner of the `dsm-cli` package back to your user.
```
sudo chown -R $USER /usr/local/lib/node_modules/dsm-cli
```
