---
title: "Netbox Upgrade Play-by-play"
date: 2023-04-25
categories: 
  - "netbox"
  - "sot"
tags: 
  - "linux"
  - "netbox"
  - "neteng"
  - "sot"
  - "sourceoftruth"
  - "upgrade"
---

I just upgraded my Netbox server from v2.7.6 to v3.4.8. This is just a record of what I did in case anyone want to know how I did it.

## Environment

- The source v2.7.6 server is an Ubuntu 18.04 VM. Yes, both are very old.

- The destination v3.4.8 server is an Ubuntu 20.04 VM.

- We have no media, scripts, or reports in Netbox.

- I'm running Virtualbox on my laptop to do the data migrations.

- I did the Netbox installs with [Netbox Build-o-matic](https://github.com/jordanrvillarreal/netbox-build-o-matic).

## Process Overview

Since we're running such an old version of Netbox, we need to do an interim upgrade to v2.11.x before proceeding to v3.x.x. We decided on v2.11.12.

The main idea here is that you export you data, install on a VM, upgrade the app on that VM, then export it out after your upgrades are done. Of course, that is very simplified.

One key here is to take snapshots every time you do something. I started with an Ubuntu 20.04 install, ran an update, then took a snapshot. That's where the real work starts, and a place to restore to when you really messed things up.

## The Details

**Set Up Your VM**

I'm assuming you have Virtualbox installed and a new Ubuntu 20.04 VM created and updated.

Take a snapshot. Name it something like `Updated OS` so you know what it is.

**Export Your Old Data**

On your production server, export your data. You can use [the Replicating Netbox page](https://docs.netbox.dev/en/stable/administration/replicating-netbox/) on the official docs to see the details. I had to do all the exporting as the `postgres` user to get access to the data.

```bash
sudo su - postgres
pg_dump netbox > nb.oldversion.sql
```

Copy this file off to your machines somewhere. I usually SCP it over.

**Install Netbox v2.11.12**

On your VM, install Netbox v2.11.12 using Netbox Build-o-matic.

```bash
git clone https://github.com/jordanrvillarreal/netbox-build-o-matic.git
```

This will create a directory called "netbox-build-o-matic". Go in there and edit the file `step2.sh`. There's a line near the top where it does a curl to get the latest version number, but you can just change that to whatever you want. I usually comment out that line and add a new one like this. The "v" is very important!

```bash
#netboxVERSION=`curl -s https://api.github.com/repos/netbox-community/netbox/releases/latest | grep "tag_name" | cut -d : -f 2,3 | tr -d \" | tr -d \,`
netboxVERSION="v2.11.12"
```

The instructions for Netbox Build-o-matic tell us to chmod the `install.sh` file and run it as root.

```bash
sudo su -
chmod u+x install.sh
./install.sh
```

This thing will take a few minutes to run, but, at the end, you'll have v2.11.12 up and running. You can go to `https://<YOURIP>` to make sure everything installed properly.

SNAPSHOT!

**Importing the Old Data**

Copy your export up to your VM. Put it in `/tmp` to make sure you have access to it.

Next, we'll drop the netbox database, recreate it, then import in the old data. You probably need to do this as the `postgres` user like we did before.

```bash
sudo su - postgres
psql -c 'drop database netbox'
psql -c 'create database netbox'
psql netbox < /tmp/nb.oldversion.sql
```

Now we run the Netbox upgrade scripts to get the new data set up properly.

```bash
sudo /opt/netbox/upgrade.sh
```

After the upgrade runs, you'll have to restart the services. Since this is a VM, I usually just reboot the whole VM because I'm lazy.

When everything is back up, browse over to the Netbox GUI and log in with a Netbox admin user (often `admin` or the like). All your stuff should be there. You can also check the page footer to make sure you're on v2.11.12.

Take a snapshot! Call is something like `v2.11.12`.

**Exporting v2.11.12 Data**

Use the same method as above to export the data. Make sure to call the file something like `nb.v2.11.12.sql` so you know which one is which. Copy that over to your machine.

```bash
sudo su - postgres
pg_dump netbox > nb.v2.11.2.sql
```

**Create a New VM for v3.4.8**

Look in your list of snapshots and clone a new server from your `Updated OS` snapshot. This creates a fresh VM for you to break since you have v2.11.12 running properly already.

**Install Netbox v3.4.8**

Use Netbox Build-o-matic again to install v3.4.8. This time, though, we're not going to edit the version. Just run it and let it get the latest-and-greatest. Or you can set it to `v3.4.8`. I'm not your mother...do what you want.

So, clone the repo, become root, chmod the installer, run the installer.

```bash
git clone https://github.com/jordanrvillarreal/netbox-build-o-matic.git
cd netbox-build-o-matic
sudo su -
chmod u+x install.sh
./install.sh
```

Let it roll. Reboot the server at the end. Check the GUI to make sure everything is working.

SNAPSHOT!

**Import v2.11.12 Data**

Copy your v2.11.12 data up to the VM. Put it in `/tmp` like before and import it in.

```bash
sudo su - postgres
psql -c 'drop database netbox'
psql -c 'create database netbox'
psql netbox < /tmp/nb.v2.11.12.sql
```

Before we run the upgrade, we need to deal with the way Netbox changed contacts in v3.2. Before, contacts were associated with a site (????), but v3.2 changed that to be a contact object associated with a site. I chose the easy way out and just told the upgrade script to delete the contact info for now. You do that by setting an environment variable.

The ASN data looks to be in the same boat here (Thanks, Justin!). If you do the upgrade this way, you'll have to recreate that as well.

```bash
sudo su -
export NETBOX_DELETE_LEGACY_DATA=1
/opt/netbox/upgrade.sh
```

As we did before, browse over to the GUI to make sure everything is in there and that you're running the correct version.

SNAPSHOT!

**Export v3.4.8 Data**

Yep. Export the data again. This time, call it `nb.v3.4.8.sql`. Copy it off.

```bash
sudo su - postgres
pg_dump netbox > nb.v3.4.8.sql
```

**Import v3.4.8 Data to Production**

Now that we have our data on the right version, we can import it into our new production server. Same method here. Copy it up to `/tmp`. You've done it 3 times now, so update your LinkedIn profile to make sure everyone knows you are a Postgres admin now.

```bash
sudo su - postgres
psql -c 'drop database netbox'
psql -c 'create database netbox'
psql netbox < /tmp/nb.v3.4.8.sql
```

Run the upgrade script to make sure things are kosher.

```bash
sudo /opt/netbox/upgrade.sh
```

**Profit**

You're done. Enjoy v3.4.8.

## Cleanup

- Securely delete your `.sql` files from your machine. You don't want that data hanging out.

- Turn off the VMs you created. Delete them at will later.

- Decom the old server. Get that old thing off your network.

## Polishing

You may need to do some polishing in Netbox.

- Add your contact info back

- Install your scripts and media if needed

- Regenerate API tokens

#netbox #sourceoftruth #sot #upgrade
