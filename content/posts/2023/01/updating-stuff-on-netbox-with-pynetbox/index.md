---
title: "Updating Stuff on Netbox with Pynetbox"
date: 2023-01-25
categories: 
  - "automation"
  - "netbox"
  - "pynetbox"
  - "python"
tags: 
  - "automation"
  - "netbox"
  - "neteng"
  - "pynetbox"
  - "python"
---

Let's see. We've [queried stuff](/posts/2022/12/querying-netbox-with-pynetbox/) on [Netbox](https://docs.netbox.dev/en/stable/) and [added stuff](/posts/2023/01/adding-stuff-to-netbox-with-pynetbox/) to Netbox. Now let's update stuff.

Netbox, like all sources of truth, needs to be kept up-to-date if it's going to be useful. Without doing some maintenance on the data, it will wind up being like that one Visio diagram that you give the auditors -- it might have been accurate at one point but gets further and further from the truth every day. We'll need to keep our stuff updated today in order to use it more effectively tomorrow.

As a warning to everyone, I am not a developer. I am a network engineer who is trying to do some automation stuff. Some of what I’m doing sounds logical to me, but I would not trust my own opinions for production work. I’m sure you can find a [Slack channel](https://netdev.chat/) or [Mastodon instance](https://infosec.exchange/explore) with people who can tell you how to do things properly.

We're going to again use [Python](https://www.python.org/) and [pynetbox](https://pynetbox.readthedocs.io/en/latest/) for this (as the title says). Here's the environment I'm working in.

```text
Python         :  3.9.10 
Pynetbox       :  7.0.0  
Netbox version :  3.4.3 (Docker)
```

Remember when [we loaded the data from the _sites.yml_ file last time](/posts/2023/01/adding-stuff-to-netbox-with-pynetbox/)? We're going to use that same file to run another script that will update existing information. This time, the script will check Netbox for some site values and updated it if it doesn't match the [YAML](https://yaml.org/) file. Here we go. As always, these scripts and YAML files are available in [my Github repository](https://github.com/aconaway1/blog-pynetbox).

```python {linenos=true}
### pynetbox_update_sites.py
import pynetbox
import yaml

ENV_FILE = "env.yml"
SITES_FILE = "sites.yml"

with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)
    
with open(SITES_FILE) as file:
    sites_to_load = yaml.safe_load(file)
    
nb_conn = pynetbox.api(url=env_vars['netbox_url'])

token = nb_conn.create_token(env_vars['username'], env_vars['password'])

for site in sites_to_load:
    are_they_different = False
    print(f"Checking {site['name']} for updates...", end="")
    queried_site = nb_conn.dcim.sites.get(name=site['name'].upper())
    if not queried_site:
        print(f"Site {site['name']} does not exist. I'm choosing not to add it.")
        continue
    for key in site.keys():
        if site[key] != queried_site[key]:
            are_they_different = True
            print("looks like it's different. Will update.")
    if are_they_different:
        queried_site.update(site)
    else:
        print("seems to be the same.")
        continue
    
token.delete()
```

All the way down to line 16 should be pretty familiar already. Check out [the last few posts](/tags/pynetbox/) to get caught up.

Line 18 goes through all the sites in the YAML so we can do stuff.

Line 19 sets a boolean variable called _are\_they\_different_ to track if we need to do the update or not. We could just blindly update the object, but it seems a bit inefficient if the data is the same.

Lines 21 - 24 check to make sure the site actually exists. If it doesn't, print a message and skip it. We'll use that queried site object here in a bit to compare against the YAML.

I'm having trouble wording an explanation for lines 25 - 28. We first take the keys for the dictionary that we imported from YAML and go through each of them. If the value for that key in the Netbox object is different than the value for the same key in the YAML file, then we'll set that boolean variable to _True_. If they're the same, nothing will happen.

Lines 29 - 30 check to see if we need to do an update and then do it if needed. We're done .all(), .get(), .filter(), and .create() (and even .delete() if you count [the token thing](/posts/2023/01/using-pynetbox-to-create-netbox-api-tokens/)), but this is the first time we're doing an .update(). In this case, we're taking the _queried\_site_ object and updating it with the data that came from the YAML. Any values that are different get updated.

Lines 31 - 33 tell the user nothing is happening since the values match.

Line 35 nukes the token we created in line 16.

Is this horrible code or what? We could probably take the YAML, do some value validation, then just update the object without all this frilly stuff. I mean, this isn't our production database that's taking 65k connection per second, so we're probably not bogging down the Netbox server with additional updates. Also, the [populate](https://github.com/aconaway1/blog-pynetbox/blob/master/pynetbox_populate_sites.py) script and this update script should be one and the same. We would just load everything up from file, add things that needed to be added, and update things that needed to be updated. See again the note about me not knowing what I'm talking about. LOL

Watch out for keys in the YAML file. If you import a key that doesn't match a valid Netbox field, then you'll get an exception either from the comparison (the key doesn't exist in the Netbox object, so [KeyError](https://rollbar.com/blog/python-keyerror/)) or the update (you can't update the _sreail\_mun_ field, so [RequestError](https://pynetbox.readthedocs.io/en/latest/request.html?highlight=exception#pynetbox.core.query.RequestError)). You also need to make sure the field is of the correct type; you can't pass a string when Netbox is expecting an ID. You'll need to do some validation to make sure you're not going to get yourself in trouble later.

The script works. That's fine, but all we've done is move the task of updating the data from Netbox to the YAML file. Someone still has to maintain the data no matter where it lives. It would be pretty cool if we had something to automagically go out into the network and get the data we need to update Netbox. We can definitely do that, but let's start simple and just update serial numbers.

Where do the serial numbers live? Well, on the devices themselves. We'll need to log into them -- usually with SSH -- to scrape that data. For SSH-enabled devices, we'll use [Netmiko](https://github.com/ktbyers/netmiko) to log in, run a command that shows the serial number, and update Netbox if needed. At home, the only device I have that runs SSH is a [Mikrotik hAP AC3](https://mikrotik.com/product/hap_ac3), so we'll just act like this is the Internet router in Phoenix. If you're interested in Netmiko and much-better Python than I would ever generate, make sure you take [Kirk Byers course on Python for Network Engineers](https://pynet.twb-tech.com/free-python-course.html) -- this is very much worth your time if you're just getting started in Python.

We have yet another YAML file with the IP information for the devices...and another one with the credentials to use to log in. This is pretty much the worst way to do this. The IP information should already be in Netbox, so just get it from there. The creds should be in a vault of some kind and not in a YAML file that you'll wind up publishing on a public GitHub repo accidentally. This is a lab, though, so we'll just do it this way for now. This sounds like more topics for later, doesn't it?

The device YAML files contains a list of devices to check with _name_ and _mgmt\_ip_.

```yaml
### devices_to_update.yml
- name: PHX-RTR01
  mgmt_ip: 172.22.0.1
```

The credentials YAML file is just _username_ and _password_. I'm not going to publish my version for security's sake.

Alright. Code.

```python {linenos=true}
### pynetbox_update_device_serial.py
import pynetbox
import yaml
from netmiko import ConnectHandler
import re

ENV_FILE = "env.yml"
DEVICES_FILE = "devices_to_update.yml"
DEVICE_CREDS_FILE = "device_creds.yml"

def load_env_vars():
    with open(ENV_FILE) as file:
        return yaml.safe_load(file)

def load_devices():
    with open(DEVICES_FILE) as file:
        return yaml.safe_load(file)
    
def load_device_creds():
    with open(DEVICE_CREDS_FILE) as file:
        return yaml.safe_load(file)

env_vars = load_env_vars()
devices_to_update = load_devices()
device_creds = load_device_creds()

nb_conn = pynetbox.api(url=env_vars['netbox_url'])
token = nb_conn.create_token(env_vars['username'], env_vars['password'])

for device in devices_to_update:
    print(f"Scraping {device['name']} for update.")
    # Build a dictionary for Netmiko to use to connect to the devices
    dev_conn = {
        'device_type': 'mikrotik_routeros',
        'host': device['mgmt_ip'],
        'username': device_creds['username'],
        'password': device_creds['password']
    }
    conn = ConnectHandler(**dev_conn)
    output = conn.send_command("/system/routerboard/print")
    conn.disconnect()
    
    scraped_info = {}
    
    lines = output.split("\n")
    
    for line in lines:
        m = re.match(".+serial-number: (\S+)", line)
        if m:
            scraped_info['serial'] = m.group(1)
            
    queried_device = nb_conn.dcim.devices.get(name=device['name'])
    if isinstance(queried_device, type(None)):
        print(f"The device {device['name']} doesn't exist. Skipping.")
        continue
    if queried_device['serial'] == scraped_info['serial']:
        print(f"The serials match. No changes.")
    else:
        print(f"Updating the serial number for {device['name']}.")
        queried_device.update({"serial": scraped_info['serial']})

token.delete()
```

The code is getting a bit out of hand without some comments. I'll have to start including those from now on.

Lines 23 - 25 are calling local functions to load up the data from the YAML files. These are here just to show that I do indeed know how to use functions. :)

Line 30 goes through all the devices in our file. We only have one, so it shouldn't take too long.

Lines 33 - 41 are the Netmiko stuff. First, we build up a dictionary that contains the connection information - host, username, password, and device type. This is the Netmiko device type and is used to figure out what prompts and login process to expect. Line 40 gets the output of the command _/system/routerboard/print_ (a [RouterOS](https://mikrotik.com/software) command) and stores it in _output_. We'll look at that again in a second.

Line 43 defines the dictionary we'll send to Netbox if an update is needed.

Line 45 turns the value of output, which is a long string from the device, into a list of lines that are more usable. We'll use those lines to do a [regex](https://www.w3schools.com/python/python_regex.asp) match to find the serial number. Regex is its own beast, so do some reading & testing on your own.

Lines 47 - 50 are where the regex magic happens. Line 48 does the heavy lifting here; it finds a line that contains "serial-number: " (yes, there's a space in there at the end) and saves the characters after it. We use that value in line 50 (the _m.group(1)_ thing) to set the serial number in the _scraped\_info_ dictionary.

Line 52 queries Netbox for the object we might need to update. The next few lines make sure it really exists before moving forward. We should probably do this before the SSH stuff so we don't waste our time if the device isn't already in Netbox.

Line 56 does the comparison of the scraped serial number versus the serial number in Netbox. If they don't match, then we update like we did for the sites.

Updating serial numbers is nice, but that's in the bottom 1% of the data you care about. You really care about subnets and addresses and interfaces and circuits and rack locations and more. Some things can be derived from the gear and others can't. There's always going to be some stuff you have to keep updated manually, but that data that can be updated automatically should be taken out of the hands of people. People make mistakes, get lazy, don't read directions...that leads to something worse than no documentation -- bad documentation.

Send any docker router images questions my way.

#python #netbox #pynetbox #automation #neteng
