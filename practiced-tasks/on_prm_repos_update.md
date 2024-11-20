The commands you provided are used to modify the configuration files for YUM repositories on CentOS. Here’s what each command does:

1. **Comment out `mirrorlist` lines:**

    ```bash
    sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
    ```

    This command uses `sed` to search for lines containing `mirrorlist` in all files matching `/etc/yum.repos.d/CentOS-Linux-*` and comments them out by prefixing them with `#`.

2. **Uncomment and modify `baseurl`:**

    ```bash
    sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
    ```

    This command uses `sed` to search for lines starting with `#baseurl=http://mirror.centos.org` and replaces them with `baseurl=http://vault.centos.org` in all files matching `/etc/yum.repos.d/CentOS-Linux-*`. It effectively enables the `baseurl` line by removing the `#` comment character and changes the URL.

### Summary:
- **First command:** Comments out existing `mirrorlist` lines.
- **Second command:** Updates the `baseurl` from the CentOS mirror to the CentOS vault.



To add the repository configuration files to CentOS using `vim`, follow these steps:

### 1. **Create and Edit `local.repo`**

1. Open the terminal.
2. Use `vim` to create or edit the `/etc/yum.repos.d/local.repo` file:

   ```bash
   sudo vim /etc/yum.repos.d/local.repo
   ```

3. Add the following lines to the file:

   ```bash
   [InstallMedia-BaseOS]
   name=CentOS Linux 8 - BaseOS
   metadata_expire=-1
   gpgcheck=1
   enabled=1
   baseurl=file:////run/media/root/CentOS-8-5-2111-x86_64-dvd/BaseOS/
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial

   [InstallMedia-AppStream]
   name=CentOS Linux 8 - AppStream
   metadata_expire=-1
   gpgcheck=1
   enabled=1
   baseurl=file:////run/media/root/CentOS-8-5-2111-x86_64-dvd/AppStream/
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
   ```

4. Save the file by pressing `Esc`, then type `:wq`, and hit `Enter`.

### 2. **Create and Edit `mysql.repo`**

1. Open the `/etc/yum.repos.d/mysql.repo` file in `vim`:

   ```bash
   sudo vim /etc/yum.repos.d/mysql.repo
   ```

2. Add the following lines to the file:

   ```bash
   [mysql57-community]
   name=MySQL 5.7 Community Server
   baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/
   enabled=1
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
   ```

3. Save the file by pressing `Esc`, then type `:wq`, and hit `Enter`.

### Final Notes:
- These repository files will allow you to install packages from both the CentOS installation media and the MySQL 5.7 repository.
- Make sure the DVD or media is mounted properly at `/run/media/root/CentOS-8-5-2111-x86_64-dvd/`.
- The `gpgkey` files should be available at the specified locations (`/etc/pki/rpm-gpg/`).

These modifications are typically made to ensure that YUM repositories point to a specific location, such as a vault, for older CentOS versions that may no longer be actively mirrored.