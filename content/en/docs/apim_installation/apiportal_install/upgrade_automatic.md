{
"title": "Upgrade API Portal",
  "linkTitle": "Upgrade API Portal",
  "weight": "110",
  "date": "2019-08-09",
  "description": "Upgrade your existing API Portal to 7.7."
}
This section does not describe how to upgrade API Gateway. For information on upgrading API Gateway, see [API Gateway Upgrade Guide](/docs/apim_installation/apigw_upgrade/).

## Upgrade prerequisites

Before you upgrade, complete the following prerequisites. These prerequisites apply for all installations: software installation and Docker containers.

* If you intend to use the EasyBlog and EasyDiscuss plugins, you must install them before you start the upgrade. For more details, see [Install API Portal](/docs/apim_installation/apiportal_install/install_software/).
* Stop and back up the existing API Portal files and database. There is no option to roll back after you start the upgrade.
* To back up an API Portal software installation, perform a file system backup and export the database.

## Upgrade API Portal

If you have a 7.6.2 API Portal installation, you can upgrade to API Portal 7.7 without having to repeat the initial installation setup.

The following table shows the path for upgrading API Portal versions:

| From                 | To                                                                                                                    |
| -------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Any previous version | 7.6.2                                                                                                                 |
| 7.6.2                | [7.7 GA](https://axway-open-docs.netlify.app/docs/apim_relnotes/201904_release/apip_relnotes/)                        |
| 7.7 GA               | [7.7.x](https://axway-open-docs.netlify.app/docs/apim_relnotes/20200130_apip_relnotes/) (Including all Service Packs) |

To upgrade your API Portal software installation, follow these steps:

1. Download the API Portal upgrade package from the [Axway Support](https://support.axway.com).
2. Go to the the directory where you saved the upgrade package and extract it:

   ```
   tar xpvzf <package_name>.tgz
   ```
3. Extract the Joomla! update package (for example, `joomla-update-package-3.9.14-package.zip`) from the API Portal upgrade package to your local file system.
4. Log in to the Joomla! Administrator Interface (JAI) (`https://<API Portal host>/administrator`).
5. Click **Components > Joomla! Update**, and go to the **Upload & Update** tab. If **Joomla! Update** is not visible in the menu, connect to your user database and execute the following query for API Portal database:

   ```
   update s8f7h_menu set menutype='main' where title like 'com_joomlaupdate'
   ```
6. Select the Joomla! update package (for example, `joomla-update-package-3.9.14-package.zip`) from your file system.
7. Click **Upload & Install**, and follow the displayed instructions.
8. Enter the following to run the upgrade script:

   ```
   sudo ./apiportal_upgrade.sh
   ```

## Post-upgrade steps

After the upgrade, perform the following tasks.

### Reinstall Joomla! components

After upgrade, you must reinstall Easyblog and EasyDiscuss in JAI to update the component version and fix compatibility issues. The API Portal data related to the components (posts, attachments) is not affected.

1. Log in to the JAI.
2. Click **Components > EasyBlog**, and follow the instructions in the EasyBlog installer.
3. If prompted to select the installation method, select **Installation via Directory**, select the available package from the drop-down list, and follow the instructions in the installer to the finish. Do not install any of the modules and plugins unless you plan to use them. To prevent installing any modules, click **Modules** and deselect **Select All**, then repeat the same for **Plugins**.
4. Click **Components > EasyDiscuss**, and repeat the component installation as described for EasyBlog.
5. If a newer version is available for **EasyBlog** or **EasyDiscuss**, click **Update Now** to update the component.

{{< alert title="Note" color="primary" >}} To resolve a known issue (caused by EasyBlog) with broken menu paths when creating new custom menus for your API Portal in JAI, you must rebuild the menu paths. In JAI, select **Menus > Main Menu** and click **Rebuild**. You only need to rebuild the menu paths once after installation or upgrade. {{< /alert >}}

### Restore footer customizations

If you customized the company name in your API Portal footer using a Joomla! language override (**Extensions > Languages > Overrides** in JAI), you must perform the following steps to restore language overrides after upgrade:

1. Locate the backup file `/opt/axway/apiportal/htdoc/Backups/<timestamp>/administrator/language/overrides/en-GB.override.ini` that is created during an upgrade. The timestamp corresponds to the time of the upgrade, for example, `20180613105149`.
2. Copy the backup file to the path `/opt/axway/apiportal/htdoc/language/overrides/en-GB.override.ini`.

Any customizations performed using language overrides are now restored.

### Hide blog from the API Portal landing page (optional)

After the upgrade, the blog is visible and accessible on the main menu on the API Portal landing page, because the EasyBlog plugin overrides permissions. If you want to hide your blog on the landing page, do the following:

1. Log in to JAI.
2. Click **Menus > Main Menu**, and select **Blog**.
3. On the **Details** tab, set **Access** to **Registered**, and click **Save**.
4. Refresh the API Portal landing page. Blog is no longer visible in the main menu before signing in.

### Consolidate vhosts and .htaccess files (optional)

During upgrade, the original `vhost` file is backed up to the following location: `/etc/httpd/conf.d/apiportal.conf.old`.

A new `vhost` file is deployed at the same location. If you had any customization, which you want to preserve, you must merge the old and new files together manually.

Similarly, the original `.htaccess` file is backed up to `${apiportal-install-dir}/.htaccess.old`. However, the two files are merged automatically.

### Encrypt the Public API mode user password (optional)

If you are using the Public API mode in API Portal you must run a script to encrypt the Public API mode user password and specify a directory to store the encryption key.

```
sudo sh ./apiportal_encryption.sh
```

The directory is created along with a file. The last segment of the directory is the file name, for example: `/sample/directory/for/encryption/key` creates an empty file named "key" in the desired directory.

After the script is finished, re-enter the password for the Public API mode user in JAI to encrypt and store the script correctly. For more details see [Encrypt the Public API user password in unattended mode](/docs/apim_installation/apiportal_install/install_unattended/#encrypt-the-public-api-user-password-in-unattended-mode).

### Encrypt database password

If you did not choose to encrypt your database password during the installation process, you can use the `apiportal_db_pass_encryption.sh` script, available from both API Portal installation and upgrade packages, to encrypt the password at any time. For more details see [Encrypt database password](/docs/apim_installation/apiportal_install/secure_harden_portal/#encrypt-database-password).