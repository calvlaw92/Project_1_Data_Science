# Project_1_Data_Science
Detailed outline of steps required to configure Jupyter Data Science Notebook Server on Amazon Web Services

**Setting up a SSH Key**
1. Open Terminal/Bash
2. $ssh-keygen                                                          #This will generate an public/private rsa key pair
A generated key pair should be defaultly saved to a .ssh file. It is fine to not set a passphrase especially if it is a personal computer. Be careful of overwriting previously saved generated key pairs!
3. $ls ~/.ssh                                                           #This will list the files within the .ssh folder
You should see two files, one named id_rsa which will be your identification and id_rsa.pub which will be your public key.
4. $cat ~/.ssh/id_rsa.pub                                               #This will display the contents of the key

**Amazon EC2 Key pair and Security Group**
1. Sign into the Amazon Web Services Console
2. Under the All Services tab, click into EC2
3. Under resources or the network & security tab on the dashboard, click into Key Pairs
4. Click import Key Pair
Give the Key Pair a name to help you differentiate your key pairs, such as based on when you created the key. Copy and Paste the contents of the id_rsa.pub key into the Public key contents box.Then import it.
5. Under resources or the network & security tab on the dashboard, click into Security Groups
6. Click create Security Group
Give the Security Group a name, and enter a description for it such as ssh http. Under inbound rules, select type SSH and HTTP, which should be ports 22 and 80 respectively. Change the source for both of them to anywhere. Create the Security Group.

**Setting up a AWS Operating System**
1. Click Launch Instance, which can also be found on the EC2 dashboard.
2. Select the Amazon Machine Image (AMI) of your coosing.                
Ubuntu being one of the more popular option that is widely used by many people and companies. If last digit on the version is even, it usually mean that it is a stable version while an odd number demarks it to be a test version.
3. Choose an Instance Type.
Select the Tier that lines up with the amount of computer processing power that is require for your project.
4. Skip the Configure Instance Step to move on to Add Storage.
5. Add Storage.
Enter the amount of Elastic Block Store(EBS) volume needed for your project.
6. Skip the Add Tag Step to move on to Configure Security Group.
7. Configure Security Group.
Choose to Select an existing Security Group and select the one that you have just created for this project. Make sure to check the inbound rules to see if the ports selected will allow you to connect to the Jupyter server. Click Review and Launch.
8. Review Instance Launch
The warning sign above that says that your security group is open to the world just shows that you are connected to the internet and you can access it from anywhere. Make sure to go through and check each section to see if any mistakes were made on the above steps before clicking Launch.
9. Key Pairing
A prompt should show up after clicking Launch asking you to select a key pair. Choose to use an existing key pair and select the key pair that we created. Tick the checkbox asking if you have access to the selected key file and click Launch Instance.
10. Instances
Click on View Instances to bring you back to the Instances tab. Click on the Instance you just made to check that your imbound rules to see if your ports are open. Give a name to the Instance.

**Docker Installation**
1. Copy the IPv4 Public IP number of the Instance you created for the project from the Instances tab on AWS.
2. $ssh (AMI used, eg. ubuntu)@(Paste IPv4 Public IP number here)       #On Terminal/Bash.
When prompted if we still want to continue due to being unable to authenticate the host server, say yes. This will then establish a connection between your local system to the AWS system that you had just set up.
3. $curl -sSL https://<i></i>get.docker.com | sh                        #This downloads the script off Docker.com to install Docker
4. $sudo usermod -aG docker (AMI used, eg. ubuntu)                      #This adds our user to the Docker group on our system
5. $exit                                                                #Logout from our user for the above to take effect
6. $ssh (AMI used, eg. ubuntu)@(Paste IPv4 Public IP number here)       #Log back in
7. $docker -v                                                           #This verifies the version of docker that was installed

**Jupyter Installation**
1. $docker pull jupyter/datascience-notebook                            #Pull the Jupyter Image to run our Jupyter notebook server
2. $docker Images                                                       #This lists and verify the Image that was pulled
3. *Optional* $docker tag (Image ID) (alternate name, eg. dsnb)         #This creates a new Image that shares the same Image ID
4. $docker run -v /home/ubuntu:/home/jovyan -p 80:8888 -d dsnb          #This command allows the writing of files from Jupyter to AWS
-v allows us to write files to AWS. 80 is the port of HTTP which we assigned in AWS Security group. 8888 is Docker's assigned port.
5. $docker ps                                                           #This views the containers that are running
6. $docker exec (container ID) jupyter notebook list                    #Jupyter notebook servers running on our docker container
From the currently running servers list, copy the token number within the displayed url parameter
7. Go to webpage browser and type (IPv4 Public IP number) into the address bar to take you to jupyter
8. Paste the token number into the password/token number box to log into jupyter


