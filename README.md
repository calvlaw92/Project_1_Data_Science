# Project_1_Data_Science
Detailed outline of steps required to configure Jupyter Data Science Notebook Server on Amazon Web Services

**Setting up a SSH Key**
1. Open Terminal/Bash
2. $ssh-keygen    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;                     #This will generate an public/private rsa key pair<br />
A generated key pair should be defaultly saved to a .ssh file. It is fine to not set a passphrase especially if it is a personal computer. Be careful of overwriting previously saved generated key pairs!
3. $ls ~/.ssh     &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; #This will list the files within the .ssh folder<br />
You should see two files, one named id_rsa which will be your identification and id_rsa.pub which will be your public key.
4. $cat ~/.ssh/id_rsa.pub &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; #This will display the contents of the key<br />

**Amazon EC2 Key pair and Security Group**
1. Sign into the Amazon Web Services Console
2. Under the All Services tab, click into EC2
3. Under resources or the network & security tab on the dashboard, click into Key Pairs
4. Click import Key Pair <br />
Give the Key Pair a name to help you differentiate your key pairs, such as based on when you created the key. Copy and Paste the contents of the id_rsa.pub key into the Public key contents box.Then import it.
5. Under resources or the network & security tab on the dashboard, click into Security Groups
6. Click create Security Group <br />
Give the Security Group a name, and enter a description for it such as ssh http. Under inbound rules, select type SSH and HTTP, which should be ports 22 and 80 respectively. Change the source for both of them to anywhere. Create the Security Group.

**Setting up a AWS Operating System**
1. Click Launch Instance, which can also be found on the EC2 dashboard.
2. Select the Amazon Machine Image (AMI) of your coosing. <br />               
Ubuntu being one of the more popular option that is widely used by many people and companies. If last digit on the version is even, it usually mean that it is a stable version while an odd number demarks it to be a test version.
3. Choose an Instance Type. <br />
Select the Tier that lines up with the amount of computer processing power that is require for your project.
4. Skip the Configure Instance Step to move on to Add Storage.
5. Add Storage. <br />
Enter the amount of Elastic Block Store(EBS) volume needed for your project.
6. Skip the Add Tag Step to move on to Configure Security Group.
7. Configure Security Group. <br />
Choose to Select an existing Security Group and select the one that you have just created for this project. Make sure to check the inbound rules to see if the ports selected will allow you to connect to the Jupyter server. Click Review and Launch.
8. Review Instance Launch <br />
The warning sign above that says that your security group is open to the world just shows that you are connected to the internet and you can access it from anywhere. Make sure to go through and check each section to see if any mistakes were made on the above steps before clicking Launch.
9. Key Pairing <br />
A prompt should show up after clicking Launch asking you to select a key pair. Choose to use an existing key pair and select the key pair that we created. Tick the checkbox asking if you have access to the selected key file and click Launch Instance.
10. Instances <br />
Click on View Instances to bring you back to the Instances tab. Click on the Instance you just made to check that your imbound rules to see if your ports are open. Give a name to the Instance.

**Docker Installation**
1. Copy the IPv4 Public IP number of the Instance you created for the project from the Instances tab on AWS.
2. $ssh (AMI used, eg. ubuntu)@(Paste IPv4 Public IP number here)       &nbsp; &nbsp;&nbsp; &nbsp; #On Terminal/Bash.<br />
When prompted if we still want to continue due to being unable to authenticate the host server, say yes. This will then establish a connection between your local system to the AWS system that you had just set up.
3. $curl -sSL https://<i></i>get.docker.com | sh  &nbsp; &nbsp;&nbsp; &nbsp; #This downloads the script off Docker.com to install Docker <br />
4. $sudo usermod -aG docker (AMI used, eg. ubuntu)                      &nbsp; &nbsp;&nbsp; &nbsp; #This adds our user to the Docker group on our system <br />
5. $exit                                                                &nbsp; &nbsp;&nbsp; &nbsp; #Logout from our user for the above to take effect <br />
6. $ssh (AMI used, eg. ubuntu)@(Paste IPv4 Public IP number here)       &nbsp; &nbsp;&nbsp; &nbsp; #Log back in <br />
7. $docker -v                                                           #This verifies the version of docker that was installed <br />

**Jupyter Installation**
1. $docker pull jupyter/datascience-notebook         &nbsp; &nbsp;&nbsp; &nbsp; #Pull the Jupyter Image to run our Jupyter notebook server <br />
2. $docker Images               &nbsp; &nbsp;&nbsp; &nbsp;                 #This lists and verify the Image that was pulled <br />
3. *Optional* $docker tag (Image ID) (alternate name, eg. dsnb)    &nbsp; &nbsp;&nbsp; &nbsp;     #This creates a new Image that shares the same Image ID <br />
4. $docker run -v /home/ubuntu:/home/jovyan -p 80:8888 -d dsnb     &nbsp; &nbsp;&nbsp; &nbsp;     #This command allows the writing of files from Jupyter to AWS <br />
-v allows us to write files to AWS. 80 is the port of HTTP which we assigned in AWS Security group. 8888 is Docker's assigned port. <br />
5. $docker ps                                &nbsp; &nbsp;&nbsp; &nbsp;      #This views the containers that are running <br />
6. $docker exec (container ID) jupyter notebook list          &nbsp; &nbsp;&nbsp; &nbsp;          #Jupyter notebook servers running on our docker container <br />
From the currently running servers list, copy the token number within the displayed url parameter
7. Go to webpage browser and type (IPv4 Public IP number) into the address bar to take you to jupyter
8. Paste the token number into the password/token number box to log into jupyter

**Security Concerns with Jupyter**
Jupyter has an arbitrary code execution, which means it includes output, people that accessed someone elses code and execute it if they opened a notebook that they did not write. Which is why, Jupyter added a next level of security by asking for a token number. As Jupyter is becoming more popular, it being a publicly accessible server that is on the open web, means that it is more susceptible to being hacked by nefarious people.

**Diagram of Overall System**
<img width="769" alt="screen shot 2018-01-13 at 11 29 03 pm" src="https://user-images.githubusercontent.com/24471044/34913704-9e9fac6a-f8b9-11e7-905a-159900efeb5e.png">

**Calculating Budget for three EC2 instances**
1. Go the the Amazon Web Services website
2. Click on the Pricing tab at the top of the website
3. Scroll down the page until you see AWS Simple Monthly Calculator under the Calculate Your Savings section and click into it
4. Put in the informations to estimate the cost per month

In these examples, I am choosing not to use the free-trial credits and using Oregon as the region.
<br />
<img width="1278" alt="screen shot 2018-01-13 at 11 54 44 pm" src="https://user-images.githubusercontent.com/24471044/34913901-3fc141b4-f8bd-11e7-9690-f29a1bbd550b.png">
<br />
For this example of running a Jupyter Data Science Notebook Server, I am using a Linux t2.micro E2 Instance with a EBS Volume Storage of 30GB. As the picture shows, on the tab next to Services called Estimate of your Monthly Bill, it shows the estimated cost of $11.50 for this configuration. Thus 3 months of running this single Jupyter Data Science Notebook Server will cost about $34.50.<br />

<img width="1280" alt="screen shot 2018-01-14 at 12 08 32 am" src="https://user-images.githubusercontent.com/24471044/34914008-34e73454-f8bf-11e7-891e-ea41b5239264.png">

Changing the configurations, using a Linux t2.medium E2 Instance with a EBS Volume Storage of 60GB, the estimated monthly bill is $39.97. This mean running this E2 Instance for 3 months will cost an estimated $119.91.<br />

<img width="1280" alt="screen shot 2018-01-14 at 12 10 35 am" src="https://user-images.githubusercontent.com/24471044/34914021-67b31a7e-f8bf-11e7-86ce-6e483094563f.png">

For the last example, I use a Linux t2.small E2 Instance with a EBS Volume Storage of 40GB, the estimated monthly bill is $20.84, which makes running it for 3 months to come up to be $62.52.
