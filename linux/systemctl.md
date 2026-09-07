Systemclt is a cmd that is central control tower for the systemd(software that init and manages and tracks almost every aspect of the modern linux systems) and also work as a manager for general as well
- Everything the systemd manages is a "Unit" like :
-- .service 
-- .socket
-- .timer
-- .mount
-- .target
Some of the systemctl cmds are :
Sudo systemctl start <name>
-- stop
-- reload
-- enable
-- disable
-- mask
-- unmask
for the diagnostics :
--systemctl status ngnix.service --complete and detailed health check
to analyse the boot performance :
systemd-analyze blame --ye saare active boot services ki ordered list deta hai with initialization time
for tracing the dependency tree: 
-- systemctl list-dependencies docker.service -- visual drawing deta hai ye
for advanced ops of units:
list all running services : systemctl list-units --type=service --state=runing  (services ko chnage kr sakte hai aur state ko inactive set kr sakte hai deads ka pta karne ke liye)
Examine all system config files:
-- systemctl list-unit-files
For editing and overriding files :
sudo systemctl edit ngnix.service
fr reload : sudo systemctl daemon-reload
Cron Jobs : this area is for these time wali cheeze 
batch jobs : this area '' ''
$SHELL is used to see which bash is the user using currently
to change the shells......................
to view the avilablity : cat /etc/shells
To change the shell for the current user in an interective mode: chsh 
we can direclty change the shells as well using the -s flag like : chsh -s /bin/zsh <user>
to verify the change for the different user : grep <user> /etc/passwd
lspci : is used to know about all the hardware with manufactures



