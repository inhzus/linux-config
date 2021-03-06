# Arch on WSL 2

### Installing Arch Linux for WSL 2 from bootstrap

[Origin manual](https://github.com/badgumby/arch-wsl/blob/master/README.md)

1. Install/upgrade to the `Windows Subsystem for Linux 2`. [Go here.](https://docs.microsoft.com/en-us/windows/wsl/wsl2-index)

2. Install your favorite available distro from the `Windows Store`, then launch it from the `Start` menu.
    * `Steps 3-8 should be completed on a linux system`

3. Download the Arch Linux bootstrap (latest version at time of writing).

    `wget https://mirrors.edge.kernel.org/archlinux/iso/latest/archlinux-bootstrap-2020.07.01-x86_64.tar.gz`

4. Extract the image.

    `sudo tar -zxvf archlinux-bootstrap-2020.07.01-x86_64.tar.gz`

5. Enter the extraced directory `root.x86_64`

    `cd root.x86_64`

6. Uncomment some servers in the pacman mirrorlist.

    `vim etc/pacman.d/mirrorlist`

7. Recompress files in `root.x86_64` directory.

    `sudo tar -czvf root.tar.gz *`

8. Move the `root.tar.gz` file to an accessible Windows directory.

    `sudo mv root.tar.gz /mnt/c/Users/USERNAME/root.tar.gz`
    * `After this step, youno longer need the temporarily installed linux system`

9. Open a PowerShell prompt as Admin, and issue the following command:

    `wsl --import Archlinux PATH_WHERE_VHD_SHOULD_BE_CREATED C:\Users\USERNAME\root.tar.gz`

10. Launch Archlinux from WSL.

    `wsl -d Archlinux`

11. Initialize Arch keyring.

    ```sh
    pacman-key --init
    pacman-key --populate archlinux
    ```

12. Install base.

    `pacman -Syyu base base-devel git vim wget reflector fish`

13. Enable `multilib` (if you want).

    ```sh
    linenumber=$(grep -nr "\\#\\[multilib\\]" /etc/pacman.conf | gawk '{print $1}' FS=":")
    sed -i "${linenumber}s:.*:[multilib]:" /etc/pacman.conf
    linenumber=$((linenumber+1))
    sed -i "${linenumber}s:.*:Include = /etc/pacman.d/mirrorlist:" /etc/pacman.conf
    ```

14. Sync package databases.

    `pacman -Syy`

15. Update mirror list (replace United States with preferred repo mirror country).

    `reflector --country China --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist`

16. Set `root` user password.

    `passwd`

17. Create new user.

    `useradd -m -G wheel -s /bin/fish -d /home/username username`

18. Set password on user.

    `passwd username`

19. Enable `wheel` group.

    `sed -i '/%wheel ALL=(ALL) ALL/c\%wheel ALL=(ALL) ALL'  /etc/sudoers`

20. Edit Arch locale and regenerate.

    ```sh
    sed -i 's:#en_US.UTF-8 UTF-8:en_US.UTF-8 UTF-8:g' /etc/locale.gen
    locale-gen
    echo LANG=en_US.UTF-8 >> /etc/locale.conf
    echo LANGUAGE=en_US.UTF-8 >> /etc/locale.conf
    echo LC_ALL=en_US.UTF-8 >> /etc/locale.confS
    ```
 
***

### Install Yay AUR Helper and Pacman Wrapper

1. Create a directory for the yay PKGBUILD files and enter it.

   `mkdir ~/yay`
   `cd ~/yay`

2. Download yay PKGBUILD from AUR.

   `wget "https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=yay" --output-document=./PKGBUILD`

3. Run `makepkg` to build and install yay.

   `makepkg -si`

* Alternatively, you could use the instructions from the official [Yay Github repo](https://github.com/Jguer/yay))

***

### Configure systemd for some services

Install [docker-systemctl-replacement-git](https://aur.archlinux.org/packages/docker-systemctl-replacement-git)
by yay, or directly use scripts [here](https://github.com/gdraheim/docker-systemctl-replacement/tree/master/files/docker)
since the former aur package is orphaned.

### Install Windows Terminal

Get Windows Terminal from [Windows Store](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701).

With [Draculatheme](https://draculatheme.com/windows-terminal/) the Microsoft [manual](https://docs.microsoft.com/en-us/windows/terminal/customize-settings/profile-general)
you can make the terminal really pretty and smooth.

