# Connect to Kaggle instance from Local VSCode

## Setup

1. Generate ssh keys and make it public
```sh
ssh-keygen -t rsa -b 4096 -C "kaggle_remote_ssh" -f ~/.ssh/kaggle_rsa
```
2. Put the `kaggle_rsa.pub` file in this repo.

3. [Go to the raw ssh pub file and copy the URL](https://raw.githubusercontent.com/francisco-perez-sorrosal/vscode-kaggle-instances/refs/heads/main/kaggle_rsa.pub)

## Actions in Kaggle

1. Create a notebook in [Kaggle](https://www.kaggle.com/)
2. Execute the following instructions in a notebook cell:
    1.  
```sh
!printenv > /kaggle/working/kaggle_env_vars.txt
```

```
!git clone -b feat/zrok-integration https://github.com/buidai123/Kaggle_VSCode_Remote_SSH.git /kaggle/working/Kaggle_VSCode_Remote_SSH
```
```
%cd/kaggle/working/Kaggle_VSCode_Remote_SSH
!chmod +x zrok_setup.sh setup_ssh.sh
```

Configure ssh
```
%cd /kaggle/working/Kaggle_VSCode_Remote_SSH
!./setup_ssh.sh https://raw.githubusercontent.com/francisco-perez-sorrosal/vscode-kaggle-instances/refs/heads/main/kaggle_rsa.pub
```

Configure zrok (Requires you had created an account and you've copied the account token)
```
%cd /kaggle/working/Kaggle_VSCode_Remote_SSH
!./zrok_setup.sh
!zrok enable <your_zrok_account_token>
```

Create and share a reserved token for the sessions so we don't have a dynamic one each time we reconnect
```
%cd /kaggle/working/Kaggle_VSCode_Remote_SSH
!zrok reserve private localhost:22 --unique-name "my_reserved_token_name" --backend-mode tcpTunnel
!sleep 5
!zrok share reserved "my_reserved_token_name"
```

The output will show you something like `zrok access private "my_reserved_token_name"`, so copy the <token>

## In your local machine

1. Install [zrok](https://myzrok.io/)
2. Get the zrok token when creating the account
3. Enable zrok account `zrok enable <your_zrok_token_found_when_creating_the_zrok_account>`
4. Execute `zrok access private "my_reserved_token_name"`
5. Edit your `~/.ssh/config` and add:
```
Host kaggle
  HostName 127.0.0.1
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  IdentityFile ~/.ssh/kaggle_rsa
  Port 9191
  User root
  RequestTTY force
  RemoteCommand cd /kaggle/working && bash -l
```
6. Test it with `ssh kaggle`

Note that `RequestTTY` and `RemoteCommand` may not work in Windsurf.

## In VSCode/Windsurf

1. Hit cmd + shift + p, search for Remote-SSH: Connect to Host and choose `kaggle`

# Reference Instructions

- https://github.com/buidai123/Kaggle_VSCode_Remote_SSH
- https://github.com/buidai123/Kaggle_VSCode_Remote_SSH/tree/feat/zrok-integration?tab=readme-ov-file
