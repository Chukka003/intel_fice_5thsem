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


## Conclusion

You have successfully installed Plex Media Server, configured the media directories, and set the appropriate permissions for both Plex and your user. Now, you can access the Plex Media Server on your local network and start streaming your media content.

