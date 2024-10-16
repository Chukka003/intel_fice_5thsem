# intel_fice_5thsem
#Plex Media Server Installation on Ubuntu
#This guide walks you through the process of installing Plex Media Server on Ubuntu, setting up media folders, and ensuring proper permissions for both Plex and the user.

#Table of Contents
#Prerequisites
#Step 1: System Update
Step 2: Install Required Packages
Step 3: Add Plex Repository and Key
Step 4: Install Plex Media Server
Step 5: Start and Enable Plex Service
Step 6: Set Up Media Directory
Step 7: Configure Permissions
Step 8: Mount Media Folder to Plex Directory
Step 9: Make Bind Permanent
Step 10: Adjust Permissions for Media Access
Step 11: Finalize and Restart Plex Service
Conclusion
Prerequisites
Before starting, ensure you have:

Ubuntu system (20.04 or later recommended)
Sudo privileges
Stable internet connection
Step 1: System Update
Make sure your system is up to date before installing Plex.

bash
Copy code
sudo apt update && sudo apt upgrade -y
Step 2: Install Required Packages
Plex requires certain dependencies. Install them using the following command:

bash
Copy code
sudo apt install dirmngr ca-certificates software-properties-common apt-transport-https curl -y
Step 3: Add Plex Repository and Key
Add Plex's official repository and the required signing key.

bash
Copy code
curl -fsSL https://downloads.plex.tv/plex-keys/PlexSign.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/plex.gpg
Then, add the Plex repository to your APT sources list:

bash
Copy code
echo deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
Update the package list:

bash
Copy code
sudo apt update
Step 4: Install Plex Media Server
Install Plex Media Server using the APT package manager.

bash
Copy code
sudo apt install plexmediaserver
Step 5: Start and Enable Plex Service
Start Plex and ensure it runs at system startup.

bash
Copy code
sudo systemctl start plexmediaserver
sudo systemctl enable plexmediaserver
Step 6: Set Up Media Directory
Create a folder inside your home directory where you will store media files.

bash
Copy code
mkdir /home/tillu/plexmedia
Step 7: Configure Permissions
Set appropriate ownership and permissions for the media directory so Plex can access it.

bash
Copy code
sudo chown -R plex:plex /home/tillu/plexmedia
sudo chmod 755 /home/tillu/plexmedia
Restart Plex after setting the permissions:

bash
Copy code
sudo systemctl restart plexmediaserver
Step 8: Mount Media Folder to Plex Directory
Since Plex typically operates within the /var/lib/plexmediaserver/ directory, we need to bind the media folder from your home directory to a location Plex can access.

Create a folder for binding:

bash
Copy code
sudo mkdir /var/lib/plexmediaserver/plexmedia
Bind the home directory media folder to Plex's directory:

bash
Copy code
sudo mount --bind /home/tillu/plexmedia /var/lib/plexmediaserver/plexmedia
Step 9: Make Bind Permanent
To ensure the mount persists after reboot, modify the /etc/fstab file.

Open the /etc/fstab file:

bash
Copy code
sudo nano /etc/fstab
Add the following line at the end of the file:

bash
Copy code
/home/tillu/plexmedia /var/lib/plexmediaserver/plexmedia none bind 0 0
Save and exit (Ctrl + X, then Y, and Enter).

Step 10: Adjust Permissions for Media Access
To allow your user to manage files within the media directory, while keeping Plex's access intact, update the folder ownership and group memberships.

Change the ownership of the media directory back to your user:

bash
Copy code
sudo chown -R tillu:tillu /home/tillu/plexmedia
Add the plex user to your group:

bash
Copy code
sudo usermod -aG tillu plex
Set permissions to allow Plex and the user to read and write:

bash
Copy code
sudo chmod -R 775 /home/tillu/plexmedia
Step 11: Finalize and Restart Plex Service
After making these changes, restart Plex to apply the updated permissions.

bash
Copy code
sudo systemctl restart plexmediaserver
Verify the permissions:

bash
Copy code
ls -ld /home/tillu/plexmedia
Expected output:

arduino
Copy code
drwxrwxr-x  2 tillu tillu 4096 Oct 14 10:15 /home/tillu/plexmedia
Conclusion
You have now installed Plex Media Server on Ubuntu, set up a media folder in your home directory, and ensured both Plex and your user have the proper permissions. Plex is ready to serve media to your devices!

Feel free to add or modify this setup as your media library grows!

License
This guide is open-source and licensed under the MIT License.

By following these steps, you should have a fully functioning Plex Media Server on Ubuntu, ready to manage and serve your media files.
