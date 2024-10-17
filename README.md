# Plex Media Server Installation on Ubuntu

This guide walks you through the process of installing Plex Media Server on Ubuntu, setting up media folders, and ensuring proper permissions for both Plex and the user.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Step 1: System Update](#step-1-system-update)
- [Step 2: Install Required Packages](#step-2-install-required-packages)
- [Step 3: Add Plex Repository and Key](#step-3-add-plex-repository-and-key)
- [Step 4: Install Plex Media Server](#step-4-install-plex-media-server)
- [Step 5: Start and Enable Plex Service](#step-5-start-and-enable-plex-service)
- [Step 6: Set Up Media Directory](#step-6-set-up-media-directory)
- [Step 7: Configure Permissions](#step-7-configure-permissions)
- [Step 8: Mount Media Folder to Plex Directory](#step-8-mount-media-folder-to-plex-directory)
- [Step 9: Make Bind Permanent](#step-9-make-bind-permanent)
- [Step 10: Adjust Permissions for Media Access](#step-10-adjust-permissions-for-media-access)
- [Step 11: Finalize and Restart Plex Service](#step-11-finalize-and-restart-plex-service)
- [Conclusion](#conclusion)

---

## Prerequisites

Before starting, ensure you have:
- Ubuntu system (20.04 or later recommended)
- Sudo privileges
- Stable internet connection

---

## Step 1: System Update

Make sure your system is up to date before installing Plex.

```bash
sudo apt update && sudo apt upgrade -y
```
![Screenshot from 2024-10-14 15-38-32](https://github.com/user-attachments/assets/32f0cd35-9fcd-443d-a649-1ee1ae218fd3)


---
## Step 2: Install Required Packages

Install the dependencies that Plex requires:

```bash
sudo apt install dirmngr ca-certificates software-properties-common apt-transport-https curl -y
```
![2](https://github.com/user-attachments/assets/e04280a6-e938-41bb-8bc9-e45cbeace62e)

---
## Step 3: Add Plex Repository and Key

1. **Add the GPG Key**:
To ensure that the packages from Plex are trusted, download the GPG key and add it to your keyring:

   ```bash
   curl -fsSL https://downloads.plex.tv/plex-keys/PlexSign.key | sudo gpg 
   dearmor | sudo tee /usr/share/keyrings/plex.gpg
   ```
![3](https://github.com/user-attachments/assets/ff64f1b0-e6cc-4ac9-9d56-4a338db4f02b)

2. **Add the Plex Repository**:
Add the Plex repository to your APT sources list. This allows you to install Plex directly using the package manager:

   ```bash
   echo deb [signed-by=/usr/share/keyrings/plex.gpg] 
   https://downloads.plex.tv/repo/deb public main | sudo tee 
   /etc/apt/sources.list.d/plexmediaserver.list
   ```
![4](https://github.com/user-attachments/assets/a1231c5e-0a75-40e7-ba4c-f72dea0c0dae)

3. **Update the Package List**:
Finally, update your package list to include the new Plex repository:

   ```bash
   sudo apt update
   ```

---

## Step 4: Install Plex Media Server

Now that the repository is added, you can install Plex Media Server using the APT package manager. Run the following command:

```bash
sudo apt install plexmediaserver
```

![5](https://github.com/user-attachments/assets/ea157c74-18dd-4dc9-8b9f-0a33f22dd7a0)

---

## Step 5: Start and Enable Plex Service

After the installation is complete, start the Plex Media Server service and enable it to run on system boot:

1. **Start the Plex Service**:

   ```bash
   sudo systemctl start plexmediaserver
   ```
2. **Enable Plex to Start on Boot**:
   ```bash
   sudo systemctl enable plexmediaserver
   ```

----

## Step 6: Set Up Media Directory

Create a directory in your home folder to store your media files for Plex. This will allow you to easily manage your media content:

```bash
mkdir /home/tillu/plexmedia
```

----

## Step 7: Configure Permissions

Set the appropriate ownership and permissions for the media directory so that Plex can access it properly:

1. **Change Ownership**: Ensure that the Plex user has ownership of the media directory:

   ```bash
   sudo chown -R plex:plex /home/tillu/plexmedia
   ```
2. **Set Permissions**: Set the permissions so that the Plex user can read and write to the directory:

    ```bash
    sudo chmod 755 /home/tillu/plexmedia
    ```
---

## Step 8: Mount Media Folder to Plex Directory

To allow Plex to access your media files, mount the media folder into the Plex directory. Follow these steps:

1. **Create the Plex Media Directory**:

   ```bash
   sudo mkdir /var/lib/plexmediaserver/plexmedia
   ```
2. **Mount the Media Folder**: Use the following command to bind your media directory to the Plex media directory:
   ```bash
   sudo mount --bind /home/tillu/plexmedia /var/lib/plexmediaserver/plexmedia
    ```
![Screenshot from 2024-10-14 15-48-18](https://github.com/user-attachments/assets/e9f0c54c-5fa5-4c0c-ab7c-3df1790cad16)

---

## Step 9: Make Bind Permanent

To ensure that the bind mount persists after a reboot, you need to add an entry to the `fstab` file:

1. **Edit the fstab File**:

   ```bash
   sudo nano /etc/fstab
   ```
2. **Add the Following Line**: Append the following line to the end of the file to make the bind mount permanent:

   ```bash
   /home/tillu/plexmedia /var/lib/plexmediaserver/plexmedia none bind 0 0
   ```

----

## Step 10: Adjust Permissions for Media Access

To ensure both the Plex service and your user can manage the media files, adjust the permissions accordingly:

1. **Change Ownership Back to the User**: Ensure that your user has ownership of the media directory:

   ```bash
   sudo chown -R tillu:tillu /home/tillu/plexmedia
   ```
2. **Add the User to the Plex Group**: Add your user to the Plex group to give it necessary permissions:
   
   ```bash
   sudo usermod -aG tillu plex
   ```
3. **Set Directory Permissions**: Adjust the permissions of the media directory to allow access:
   
    ```bash
    sudo chmod -R 775 /home/tillu/plexmedia
    ```

---


## Step 11: Finalize and Restart Plex Service

After configuring the permissions, restart the Plex Media Server to apply all changes:

1. **Restart the Plex Service**:

   ```bash
   sudo systemctl restart plexmediaserver
   ```
2. **Verify the Folder Permissions**: You can check the permissions of the media directory to ensure everything is set correctly:
     
    ```bash
    ls -ld /home/tillu/plexmedia
    ```
#### The output will be : 
    drwxrwxr-x 2 tillu tillu 4096 Oct 14 10:15 /home/tillu/plexmedia**

we get to know that everything is set to copy files into our server folder

![Screenshot from 2024-10-14 16-24-57](https://github.com/user-attachments/assets/e696f403-cc44-447d-b945-f6708e5816ba)


----

## Step 12: Access Plex Web Interface

Once Plex Media Server is installed and running, you can access the Plex Web interface to configure your media libraries and settings. Open your web browser and navigate to the following URL:

```bash
http://<your-server-ip>:32400/web
```

Replace `<your-server-ip>` with the IP address of the machine where Plex Media Server is installed. For example:

```bash
http://172.26.144.64:32400/web
```
----


If everything is set up correctly, you will be directed to the Plex login screen. Use your Plex account to sign in.

### Screenshot Example:
Below is an example of the Plex Web UI login screen you should see when accessing the server:

![Screenshot from 2024-10-14 15-50-00](https://github.com/user-attachments/assets/42be18a7-2798-4133-a2f5-20f6427be5f6)


---

## Step 13: Plex Dashboard and Media Library Setup

Once you’ve successfully signed in and granted access, you will be taken to the Plex dashboard where you can begin configuring your media libraries. Add directories containing your media files, and Plex will automatically organize them into categories like Movies, TV Shows, Music, and more.

From the dashboard, follow these steps:

1. **Click on “Add Library”** in the left-hand navigation.
2. **Select the type of media** (e.g., Movies, Music, TV Shows) that you want to add.

![Screenshot from 2024-10-14 16-00-39](https://github.com/user-attachments/assets/964da9a8-8d62-468b-b62d-a9e7c6d616f3)


3. **Browse for the directory** where your media files are stored. In our case, the folder would be `/home/tillu/plexmedia`.

![Screenshot from 2024-10-14 16-00-47](https://github.com/user-attachments/assets/2f25a1d5-c039-41ac-b4b7-dfe6e0034960)

4. **Click “Add Library”** to confirm and start scanning the folder.


![Screenshot from 2024-10-14 16-10-36](https://github.com/user-attachments/assets/fb8c7a7a-b6ed-4a1a-944a-f29f6ca54530)


Plex will now begin indexing your media files, fetching metadata, and making the content available for streaming.

---

## Step 15: Share Media with Friends via Plex

Plex allows you to share your media libraries with friends, so they can watch content from their own Plex account. Here’s how to invite someone to watch your media:

1. **Go to your Plex Web Interface**.
2. **Select a media file** that you want to share or invite someone to watch.
3. **Click on the “Watch Together”** option in the media controls.

   A dialog will appear, allowing you to invite someone who has access to your Plex library.

![Screenshot from 2024-10-14 16-09-37](https://github.com/user-attachments/assets/0aca8a2d-34f9-4711-ae99-0df76cfd96de)

4. **Choose a friend** from the list of users who already have library access.
5. **Click “Invite”** to send an invitation. Your friend will be able to watch the content on their Plex account.


![WhatsApp Image 2024-10-14 at 4 13 35 PM](https://github.com/user-attachments/assets/c9febfa2-b5de-43e9-9e1c-fd8caec5b174)

6. **Our friends can navigate in their Plex server by following these**:-

![WhatsApp Image 2024-10-17 at 12 25 24 AM (1)](https://github.com/user-attachments/assets/b6ec6ba8-6f5c-46f6-bbfa-68837f9c3247) ![WhatsApp Image 2024-10-17 at 12 25 24 AM](https://github.com/user-attachments/assets/1d659fec-b167-4915-9432-73156ba96993) 

![WhatsApp Image 2024-10-17 at 12 25 24 AM (2)](https://github.com/user-attachments/assets/b691e5c4-e374-4483-a41d-35364c6ed9fe)

7. **Whenever our friend plays our videos we can see that on our dashboard**


https://github.com/user-attachments/assets/d031d58d-d6cd-442f-af6b-48f5260ea306


### Enabling DRM for Playback:

If you encounter the **“Enable DRM”** message when trying to play certain media, you may need to enable DRM (Digital Rights Management) for Plex to play some types of protected content.

---



## Conclusion

Congratulations! You’ve successfully installed and configured Plex Media Server on your Ubuntu system. You can now manage and stream your media library across various devices using Plex.

### Final Checklist:
- [x] Plex Media Server is installed and running.
- [x] Media directory is set up and mounted.
- [x] Permissions are properly configured.
- [x] Plex Web is accessible from a browser.
- [x] Media libraries are added to Plex.
- [x] Our friend can also access our Plex server.

You can continue managing your server and enjoy streaming media from anywhere.

---
