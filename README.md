# rsync-over-ssh
Use rsync to send data from local machine to balena edge device

This sample project installs sshd in container and configures it to listen on port 122. The daemon can be used to receive data stream from rsync.

replace authorized_keys file in rsync directory with your public ssh key (~/.ssh/id_rsa.pub by default)

The sshd isn't started by deamon so here's full procedure to send local data once the container runs:

1. Use balena ssh to dial into the rsync container and start ssh daemon:
balena ssh ff77fb4 rsync
root@c74ac1fc2dca:/usr/src/app# service ssh start
[ ok ] Starting OpenBSD Secure Shell server: sshd.

2. Start balena tunnel. It will listen on local port 1234 and redirect the traffic to port 122 on balenaOS, which in turn gets forwarded to the container:
balena tunnel ff77fb4 -p 122:0.0.0.0:1234
[Info]    Opening a tunnel to ff77fb46bcc8c330cccd58132bc1bd17...
[Info]     - tunnelling 0.0.0.0:1234 to ff77fb46bcc8c330cccd58132bc1bd17:122
[Info]    Waiting for connections...

3. rsync the data from directory testdir to /data directory in the container:
rsync -avz -e "ssh -p 1234" testdir root@localhost:/data/
building file list ... done
testdir/
testdir/f0
testdir/f1
testdir/f2
testdir/f3
testdir/f4

sent 327 bytes  received 136 bytes  102.89 bytes/sec
total size is 0  speedup is 0.00

