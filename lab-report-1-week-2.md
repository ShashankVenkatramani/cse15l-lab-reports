# Hi welcome to Lab Report 1 Week 2

In this page you will learn how to log into your course specific account on UCSD computers on ieng6. Note this is targeted towards those on UNIX systems.

## Step One: Installing VSCode

Search for the VSCode download on google and download the dmg. From there unzip the file and open your VSCode. You should see a screen something like this:

![VSCode Example Image](VSCode.png)

## Step Two: Remotely Connecting

In order to remotely connect you first need to set up your username and password for your UCSD CSE15 account. Go to the ETS Account management system to reset your password and then wait 15 minutes for the password reset to take place.

Once your account password is set, you can now connect via SSH for the first time. Enter the command:

`ssh yourAccountName@ieng6.ucsd.edu`

note: the yourAccountName should look like `cs15lwi22xxx`

After running the command you will be prompted to enter your password, which you should go ahead and do, and from there you will see a successful connection like this:

![Logged In](loggedInSuccessfully.png)

## Step Three: Running Commands

Now lets go ahead and try to run some commands on `ieng6`

Note: your terminal should no longer list your personal computer and should have something like:
`[cs15lwi22xxx@ieng6-203]:~:14$ `

Try running the command: `pwd`

And you should see an output like this:

![Running Command](runningCommand.png)

## Step Four: Moving Files with `scp`

In order to take code you have written on your computer and run it on ieng6 you first need to move the respective files onto ieng6 to compile and run. In order to send files over ieng6 you can use the `scp` command as following:

`scp fileName.fileExtension cs15lwi22xxx@ieng6.ucsd.edu~/`

What this command does is takes the first argument, the file you want, and sends it to the address in the second argument. Note the `~/` which tells scp to place the file in your default directory when you log into ieng6 under your account (which should be your folder).

After hitting enter you will be prompted to enter your password, and when the `scp` is successful the terminal will print back the name of the file you transferred over, and it should look like this:

![SCP Example](scpExample.png)

## Step Five: Setting an SSH Key

Now typing your password in each time you ssh or scp gets tedious, and slows down your workflow. In order to speed this up, you can create whats called an SSH key, which gives you a public and private key pair that terminal can use to automatically authenticate you when SSH'ing from your computer.

On your computer enter command:

`ssh-keygen`

And it should generate your public/private rsa key pair. From there ssh into ieng6 using the same commands from above, and use the `mkdir` command to create a .ssh folder. This is where you will end up storing your public key. Go ahead and logout from ieng6.

From there use the scp command to move your id_rsa.pub key to your .ssh folder. The path for your id_rsa.pub should be visible in the output from running `ssh_keygen`. Run the following scp command:

`scp yourIDRSA.pubKeyPath cs15lwi22xxx@ieng6.ucsd.edu:~/.ssh/authorized_keys`

This moves your public key into the authorized keys folder. Note now scp says ~/.ssh/authorized_keys, so it will go into your default folder, then .ssh, then authorized_keys.

You should now be able to SSH into ieng6 without your password as so:

![SSH no password](sshNoPassword.png)

## Step Six: Optimizing Remote Running

Great! We can now do passwordless SSH. However we still have to enter commands to compile and then run code which is still a pretty tedious process. In order to streamline running files we have sent over we can run the following line:

`ssh cs15lwi22xxx@ieng6.ucsd.edu "javac fileName.java; java fileName"`

This command will ssh into ieng6 under your account, and then from there run all the commands in the quotes seperated by the semicolon. It will start with compiling the file, and then running it and should look like this:

![faster SSH run](fasterSSHRun.png)

#Conclusion

Thanks for reading through the guide! I hope it all worked out :)
