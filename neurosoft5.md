#5. MySQL (MacOS)

1. Get your MacOS version and find out if you should download the ARM or x86 installer.
    - Click on the apple sign in the upper left corner
    - Click About this Mac
    - If the Chip starts with M (i.e. Apple M3 Pro), then download the ARM DMG. Otherwise, use the x86 installer
    - Click on More Info
    - In the macOS section you will see the OS version

2. Go to the [MySQL Community Downloads](https://dev.mysql.com/downloads/mysql/) page.

3. Select your operating system.

4. Select your OS version. 

5. Download the corresponding installer (ARM or x86). Click "No thanks, just start my download".

6. Run the installer and follow the instructions. When prompted, enter a password for the MySQL root user. Remember this password if it's different than the one you use to login to your computer.

7. Start your MySQL Server
    - Open the Terminal
        - Click on the search sign in the upper right corner, next to the date and time
        - Type Terminal in the search bar
        - Click on the Terminal application
    - Type `sudo /usr/local/mysql/support-files/mysql.server start`

8. Add MySQL to your path
    - In the same Terminal, type `echo "export PATH=/usr/local/mysql/bin:$PATH" >> ~/.bashrc`
    - Then type `source ~/.bashrc`

9. Check your installation
    - In the same Terminal, type `mysql -u root -p`
    - Then type the root password that you specified during installation