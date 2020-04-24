By default, you can connect CircleCI Windows machines only with SSH.  However, in some cases, you may want a full Remote Desktop session so you can edit files, view the stack trace, etc.  This wiki page tells you how to connect to these instances via remote desktop.  This wiki page tells you how to do so.  Credit to @peterjc123 for these instructions.

1. Use port forwarding in SSH

    By default, CircleCI will provides us with a line of ssh command for remote debugging. e.g. `ssh -p 54782 35.237.241.189 -- cmd.exe`. So you may pass an another argument `-L [remote port]:localhost:[local port]` so that the command will look like `ssh -p 54782 35.237.241.189 -L 8000:localhost:8000 -- cmd.exe`. Please note that the local port and the remote port can be different.

2. Log on to the machine

    Type in the last command in CMD/Powershell and press enter.

3. Add a rule for local port forwarding on the remote VM

    Since it is impossible to listen to the RDP port 3389 directly, we need to set up a rule for port forwarding locally. That is `netsh interface portproxy add v4tov4 listenport=8000 connectaddress=127.0.0.1 connectport=3389`. Please note that the `listenport` here and the `[remote port]` in the previous command should be the same.

4. Change user password on the remote VM

    Actually, the RDP connection is ready in the previous step. However, you don't know the password so you cannot login. So you may just change the password, since you have admin rights. 
`net user circleci [new password]`. Please note that Windows Server requires a very complex password, which should include an uppercase letter, a digit and a special symbol and at least 12 letters long.

5. Test connection locally

    Open `mstsc` or any clients that supports RDP connection, login with `circleci` and the password you set. And you should be all set.