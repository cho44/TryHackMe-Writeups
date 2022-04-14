## Bounty Hacker room for TryHackMe

## Difficulty: Easy

## www.tryhackme.com

When we first paste the ip into google we see that we already have a few names, Spike, Jet, Ed and Faye.

# Nmap Scan

This alone doesn't give us much to go by so we'll do an Nmap scan using the command `nmap -sC -sV [IP]`.

After a short while it will return a list of open ports.

![image](https://user-images.githubusercontent.com/66257304/163365336-93c39fef-bb84-48fa-bb5a-785cefe1c809.png)

We see that Anonymous ftp is enabled, this allows us to look at their ftp server without any login credentials, lets take a look using the command `ftp [IP]`.

It will ask for a name, lets put Anonymous.

We can now use the ls command to see all the files.

![image](https://user-images.githubusercontent.com/66257304/163365723-edb2ae2e-cfbe-4c4a-89e5-f49b3bac841d.png)

Lets use the command get [filename] to get the files so we can read them.

After we get both the files we can exit using the command `exit`.

Lets read the files we got.

![image](https://user-images.githubusercontent.com/66257304/163365988-efd71b13-48bf-4c3c-8cb3-94decff5bd10.png)
 
 Locks.txt looks like a file of jibberish, but is it?
 
 Lets look at Task.txt
 
 ![image](https://user-images.githubusercontent.com/66257304/163366151-7c383917-f22a-4b8d-bda3-c88ea3632046.png)

We see the file was make by Lin, this is the answer to question 3.

The next question is "Which service can be bruteforced with the text file found?"

So the file we found wasn't jibberish, it was a password list.

Now we know that we can bruteforce SSH, since this is the only service left we haven't looked at.

# Hydra

To bruteforce SSH, we can use a tool called Hydra, this tool is great for bruteforcing any type of logins.

But we need a username for the SSH server, we could try all the names from the webpage but these seemingly don't work.

The only other name we had was lin, lets try that with the command `hydra -l lin -P [PATH TO LOCKS.TXT] ssh://[IP]`

We have a hit!

![image](https://user-images.githubusercontent.com/66257304/163370997-2cfdd2c5-0c50-4b2c-a84a-0fbd5eba4bff.png)

We can now login with SSH using the command `ssh lin@[IP]` and enter the password we found.

We can also answer question 5.

# SSH

We can now have a look round for user.txt, which is conveniently in their Desktop.

There we have our first flag, time to look for some privilege escalation.

If we use the command `sudo -l` we can see all the files that we can execute.

And /bin/tar is one of them.

![image](https://user-images.githubusercontent.com/66257304/163374352-d922e6a1-e39b-4386-8dd4-784a13c3773b.png)

If we google tar priv esc, we come across GTFObins, if we look at the first command, `tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`.

We should be able to use this to get root, just by doing the command `sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`.

And Boom, We are root.

Now we can get the final flag and complete the room.





 
 
