```bash
wangh21@D206-59064:~$ ssh user@10.25.100.116
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:5j3o70WW5Owiq9LbbO6ROlTZb2yQNR3nagMBvKnlYc4.
Please contact your system administrator.
Add correct host key in /home/wangh21/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/wangh21/.ssh/known_hosts:9
  remove with:
  ssh-keygen -f "/home/wangh21/.ssh/known_hosts" -R "10.25.100.116"
ECDSA host key for 10.25.100.116 has changed and you have requested strict checking.
Host key verification failed.
wangh21@D206-59064:~$ ssh-keygen -R "10.25.100.116"
# Host 10.25.100.116 found: line 9
/home/wangh21/.ssh/known_hosts updated.
Original contents retained as /home/wangh21/.ssh/known_hosts.old
```


`ssh-keygen -R "10.25.100.116"`
