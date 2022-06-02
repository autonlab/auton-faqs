# Common ssh config files
As seen in lab [wiki](https://www.autonlab.org/autonlab_wiki/telecommuting.html), pasted below for convenience - replace `dr_who` with `your_username`
```
Host lov1 lov2 lov3 lov4 lov5 lov6 gpu1 gpu2 gpu3 gpu4 gpu5 gpu6 gpu8 gpu9 gpu10 gpu15 gpu16 gpu17 gpu18 gpu19 gaia ari athena foxconn git
    User dr_who
    ProxyCommand ssh bash.autonlab.org exec nc %h %p
    LocalForward 8888 localhost:8888
    LocalForward 8080 git:80
    LocalForward 2222 git:2222
Host lop2
     User dr_who
     Port 22
     ServerAliveInterval 30
     HostName lop2.autonlab.org
     LocalForward 1111 localhost:8888
     LocalForward 3733 localhost:3733
     LocalForward 8080 git:80
     LocalForward 2222 git:2222

 Host gogs
     Hostname lop2.autonlab.org
     User dr_who
     Port 22
     LocalForward 2222 git.int.autonlab.org:80
```

# ssh key generation
Allows logging into lab servers and pulling from gogs without use of LDAP password.

For generating a key on a remote host, first check for the existence of an `~/.ssh/id_rsa.pub` file or something similar. If it exists, skip to key generation (step 2), otherwise:
## 1. Generate a key
Run `ssh-keygen` or `ssh-keygen -t ecdsa -b 521` as advised [here](https://www.ssh.com/academy/ssh/keygen#creating-an-ssh-key-pair-for-user-authentication)

## 2. Copy local key to remote host
In most cases, you want to do this with lop2 which should cover you for most if not all lab server use-cases.

Use the `ssh-copy-id` command to copy local ssh public key to remote host, so it can use that for authentication going forward instead of requesting LDAP password:
```
ssh-copy-id -i {your_key_location_here} {your_username}@lop2.autonlab.org
```

# jupyter port pass-throughs
ssh into server you intend to run notebook on, such as lov2, then open a tmux session (short for terminal multiplexer, allows for persistent sessions through log-out) and your environment of choice. Now you're ready to get jupyter going.
## 1. Get jupyter goin, copy token
```
jupyter notebook --no-browser --port 8888
```
Note/copy the token password it shows, you'll need it for step 2. Once that's done you can leave your tmux session with `ctrl-A, D` and `exit` out of your ssh session, and the jupyter notebook shall remain alive and waiting for you to...

## 2. SSH into remote, showing jupyter notebook locally
You do this by entering an ssh session with the server currently running your jupyter session, with the small addition of informing the ssh you want the local requests sent to a particular port to actually pass through to the server port hosting your jupyter session. This can be done by:
```
ssh -L {localport}:{remoteport} lov2
```
An example if you used the port `8888` in step 1 might be:
```
ssh -L 8889:8888 lov2
```

## 3. Hit local port, enter password, and enjoy your jupyter
Direct your browswer to `localhost:{localport}`, paste the token you copied in step 1, set the password and enjoy :)

# Mounting remote disks
If, for example, I wanted to locally access a remote file system like the lab's copy of zfsmladi data, I could use an ssh file sync, or `sshfs`:
```
sshfs -o allow_other,default_permissions,IdentityFile=~/.ssh/id_rsa lov3:/zfsmladi/originals ~/workspace/remote
```

With this completed, I could now hit my local `~/workspace/remote` directory and find it populated with everything in `zfsmladi/originals`, allowing me to run local jobs that read from remote data.