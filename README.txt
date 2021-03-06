
Updian v0.4 - UpdateDebian

###############################################################

Robert Klikics, RobHost GmbH [rk@robhost.de], 2007-2012

License: GPL

PLEASE NOTE THAT THIS SOFTWARE COMES WITH ABSOLUTELY NO WARRANTY!

###############################################################


********************
WHAT IS IT GOOD FOR?

Updian is a minimalistic update-engine for DEBIAN GNU/Linux based machines (and other flavours like Ubuntu based on APT) and, since v0.4, for machines with YUM such as CentOS.
You can use it to maintain all your machines remotely over a
simple webinterface written in PHP. There are 2 cronjobs, one checks for updates, another does them.
You can choose from the webinterface which servers to update (it shows up the packages) and read logs after the updates are done.

Updian does not need any databases, every data is stored by (mostly) empty flatfiles. 
It can manage a high number of servers, I've tested/used it with 100+ servers without any problems ...

Actually, Updian only does "apt-get upgrade", no "dist-upgrade" (for sure you can issue this cmd by running Multi-SSH). So it's a good idea to run "apticron" or anything in parallel
on the remote machines to keep informed about upcoming dist-upgrades. Apticron is also good for checking the correctness of Updian - it mails you the
updates every day including changelog. These you can now install with Updian. If Updian is working correctly, apticron should mail
you the same update-infos (except dist-upgrades) as Updian shows up in the webfrontend.

For every server Updian creates an logfile, so you're always informed about updates made. The logfiles are available through the webfrontend.


*******************
MULTI-SSH

With Multi-SSH (introduced in v0.2) you can run any shell-command on ALL your servers via UPDIAN. 
Very helpful for "apt-get dist-upgrade" or such things. The command is issued on the next cron_updates.php run.


*******************
REQUIREMENTS

- Debian GNU/Linux or other apt-running systems (Ubuntu, Knoppix ...) or yum-running systems like CentOS on the remote-side
- Any Linux-Distribution on the machine which runs Updian (local-side)
- PHP 4.xx or 5.xx installed as CLI (i.e. package php5-cli) on the local-side
    |_ You can also call the crons with lynx -dump by crond or something, but with php CLI is recommended
- Webserver with PHP 4 or 5 enabled (local-side)
- A crond running (local-side)
- Access as root to all involved machines (in fact, it doesnt make sense to run updates as normal user ;-) )
- Exchanged SSH-publickeys between the machine running Updian and the client-servers
    |_ that means you can login from the machine running Updian to the remote servers via "ssh <server>" without entering a password
    |_ Howto: 
        |_ On the machine running Updian: 
            |_ ssh-keygen -t dsa
            |_ cat ~/.ssh/id_dsa.pub | ssh root@remote_server cat - ">>" ~/.ssh/authorized_keys
            |_ OR use 'ssh-copy-id <server>'
                                            

*******************
INSTALLATION

- Unzip the files to a folder on your (web) server (the machine where Updian should run).
- Edit the config.php (add absolute pathes!) and read the instructions inside this file.
- Edit the .htpasswd file and change user/password (htpasswd .htpasswd updian)
- Make sure the /updian - folder is writeable by your webserver-user (www-data or something)
- Open http://yoururl.tld/path/to/updian/ in your web browser
- Klick on "Servers" and add your servers with a FQDN per line
- For test purposes run cron_collect.php manually with "php cron_collect.php" on your shell
    |_ You should see some output and (if there are updates) your should see the servers/updates on the webinterface
- Run cron_updates.php if you want Updian to update your choosed server (from the queue)
- Add cronjobs for full automatic updates (crontab -e):
    |_ Examples:
                |_ 0 8 * * * php /var/www/updian/cron_collect.php > /dev/null 2>&1 (collect updates daily at 8 am)
                |_ 0 9 * * * php /var/www/updian/cron_updates.php > /dev/null 2>&1 (run updates daily at 9 am)


*******************
CHECKRESTART FOR UPDATED SERVICES ON REMOTE MACHINES

Since v0.3 Updian can check if there are services running on remote machines that need to be restartet. That is often needed if libs used by many programs (libssl i.e.)
have been updated on the remote machine. After that it is i.e. required to restart apache or postfix.

Updian uses the script 'checkrestart' from the package 'debian-goodies' for that. Just apply 'apt-get install debian-goodies' on the desired remote machines. 

It does, in short, anything like this to find out which procs using deprecated libs:
lsof -n | egrep -i "(DEL|inode)"

Updian writes the output from 'checkrestart' to <server>_checkrestart.log (see "Logs" in webfrontend).







 


