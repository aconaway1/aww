---
title: "Adding Stuff to Netbox with Pynetbox"
date: 2023-01-17
categories: 
  - "automation"
  - "netbox"
  - "pynetbox"
  - "python"
tags: 
  - "netbox"
  - "pynetbox"
  - "python"
---

As a warning to everyone, I am not a developer. I am a network engineer who is trying to do some automation stuff. Some of what I’m doing sounds logical to me, but I would not trust my own opinions for production work. I’m sure you can find a [Slack channel](https://netdev.chat/) or [Mastodon instance](https://infosec.exchange/explore) with people who can tell you how to do things properly.

I think there's a theme in the last few posts. I can't quite put my finger on it, though. :) We've talked about querying [Netbox](https://docs.netbox.dev/en/stable/), but it's pretty useless without data actually in it. Let's look at how to get stuff in there using [pynetbox](https://github.com/netbox-community/pynetbox).

Here’s the environment I’m running. All this code is in [my Github repo](https://github.com/aconaway1/blog-pynetbox).

```
Python         :  3.9.10 
Pynetbox       :  7.0.0  
Netbox version :  3.4.2  (Docker)
```

Adding sites is pretty logical first step in a new Netbox install. They don't have any required fields that have to be created first, so let's start there. I've got a [YAML](https://yaml.org/) file called _sites.yml_ that contains the site data I want to import. Here's what that looks like.

```yaml
### sites.yml
- name: NYC
  description: New York City
  physical_address: "123 Main Street\nNew York, NY 10001"
- name: CHI
  description: Chicago
  physical_address: "123 Main Street\nChicago, IL 60007"
- name: STL
  description: Saint Louis
  physical_address: "123 Main Street\nSaint Louis, MO 63101"
- name: DEN
  description: Denver
  physical_address: "123 Main Street\nDenver, CO 80014"
- name: PHX
  description: Phoenix
  physical_address: "123 Main Street\nPhoenix, AZ 73901"
- name: LAX
  description: Los Angeles
  physical_address: "123 Main Street\nLos Angeles, CA 90001"
```

This is a list of dictionaries - one for each site. Each site has a name, description, and physical address to use.

Here's the code we'll use to import that data. I will quickly admit that this code includes some very non-Pythonic methods. In my opinion, making code more easily readable is more important that doing it "the right way" in a lot of cases.

```python
### pynetbox_populate_sites.py
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
    name = site['name'].upper()
    slug = site['name'].lower()
    queried_site = nb_conn.dcim.sites.get(name=name)
    if queried_site:
        print(f"Site {site['name']} already exists.")
        continue
    print(f"Adding {site['name']} to Netbox.")
    constructed_site = {"name": name, "slug": slug}
    if "description" in site.keys():
        constructed_site['description'] = site['description']
    if "physical_address" in site.keys():
        constructed_site['physical_address'] = site['physical_address']
    result = nb_conn.dcim.sites.create(constructed_site)
    
token.delete()
```

Lines 1 & 2 are the modules we want to use.

Lines 4 & 5 set the name of the files where some data will live.

Line 7 & 8 import the Netbox URL, username, password, etc., from a YAML file into a dictionary called _env\_vars_. [This post](https://aconaway.com/2023/01/12/using-pynetbox-to-create-netbox-api-tokens/) talks about that a bit.

Line 10 & 11 import the site data from a YAML file into a dictionary called _sites\_to\_load_.

Lines 13 - 15 and line 32 connects to Netbox, creates a token to use, then deletes it. See [this post](https://aconaway.com/2023/01/12/using-pynetbox-to-create-netbox-api-tokens/) for more on that.

Line 17 goes through the sites from the YAML file to do the work.

Line 18 creates a variable called _name_ with a value of the given site name in upper case. We'll use this as the name of the site in Netbox. I just like to have the names of things that I configure in upper case. Total personal opinion.

Line 19 converts the name from the YAML file to lower case and saves it in a variable called _slug_. The slug is a URL-friendly version of the name that's used by...heck, I don't even know. It's a required field, so something needs to be in there. I just feed it the name in lower case.

Line 20 start some checking. We don't want to try and add a site that already exists, so let's ask Netbox before trying to add it. The result is stored in _queried\_site_.

Line 21 looks to see if _queried\_site_ has any value. If it does, that means the site name already exists in Netbox, so we need to skip it.

Line 22 & 23 prints an "already exists" message and continues to the next site in the list.

Line 25 start a new dictionary called _constructed\_site_ which we'll use when it's time to create the site. _Name_ and _slug_ are the required fields that we already know, so we'll go ahead and add those.

Line 26 - 29 look to see if the optional fields for description and address exist. If they do, then add them to _constructed\_site_ for processing. If you want to add other fields to the YAML to import (region, ASN, timezone, tags, etc.), you can just add some lines to check that as well.

Line 30, of course, is where the magic happens. This uses .create() to -- wait for it -- create a site using the given dictionary. This returns the site object we created. We're not doing anything with it, though we definitely should be checking the result to make sure it worked!

The output is pretty unremarkable. If the site exists, it says "Site X already exists." If it get added, it says "Adding X to Netbox."

What about some more-complex objects like devices? We can do that, too. To add a device, we need to pause a bit and take a look at the required fields, though. If you go into the GUI to add one manually, you'll see _device role_ (the function of the devices), _device type_ (the make and model), _site_, and _status_ are all required. They also are all objects that must already exist in Netbox, so we'll have to check the given data before trying to add the device. If we don't, we'll get an exception somewhere down the line.

We'll do another YAML file for the devices. This is what it looks like. There may or may not be some bad data in this one, so be on the lookout. \*\*hint, hint\*\*

```yaml
### devices.yml
<SNIP>
- name: CHI-RTR01
  site: CHI
  type: GENERIC
  role: INET_ROUTER
- name: LAX-FRW01
  site: LAX
  type: GENERIC
  role: FIREWALL
<SNIP>
- name: ATL-FRW01
  site: ATL
  type: GENERIC
  role: INET_ROUTER
- name: PHX-RTR01
  site: PHX
  type: GENERIC
  role: INET_ROUTER
  status: planned
<SNIP>
```

The YAML contains a list of devices that include name, type, role, and status. It's funny how that matches the required configuration, isn't it? **NOTE:** To make things easier for us, I created a device type called "GENERIC" by hand. Every device here has this type, but you should put in the real makes and models in production. Someone will ask you for an inventory in the next few months, so I suggest you get serial number in there as well. Audit season is always around the corner. :)

Alright, here's the long, long code. I'll only mention the lines that are different than the code above.

```python
### pynetbox_populate_devices.py
import pynetbox
import yaml

ENV_FILE = "env.yml"
DEVICES_FILE = "devices.yml"

with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)
    
with open(DEVICES_FILE) as file:
    devices_to_load = yaml.safe_load(file)
    
nb_conn = pynetbox.api(url=env_vars['netbox_url'])

token = nb_conn.create_token(env_vars['username'], env_vars['password'])

valid_devices_status = []
for choice in nb_conn.dcim.devices.choices()['status']:
    valid_devices_status.append(choice['value'])

for device in devices_to_load:
    name = device['name'].upper()
    slug = device['name'].lower()
    
    # See if the device already exists
    queried_device = nb_conn.dcim.devices.get(name=name)
    if queried_device:
        print(f"The device {name} already exists. Skipping.")
        continue
    
    # See if the given device type exists
    dev_type = device['type'].upper()
    queried_type = nb_conn.dcim.device_types.get(model=dev_type)
    if isinstance(queried_type, type(None)):
        print(f"The type {dev_type} does not exist. Skipping.")
        continue
    
    # See if the given device role exists
    dev_role = device['role'].upper()
    queried_role = nb_conn.dcim.device_roles.get(name=dev_role)
    if isinstance(queried_role, type(None)):
        print(f"The role {dev_role} does not exist. Skipping.")
        continue
    
    # See if the given site exists
    site = device['site'].upper()
    queried_site = nb_conn.dcim.sites.get(name=site)
    if isinstance(queried_site, type(None)):
        print(f"The site {site} does not exist. Skipping.")
        continue
    
    
    constructed_device = {"name": name, "slug": slug, "site": queried_site.id, "device_role": queried_role.id, "device_type": queried_type.id}
    if "description" in device.keys():
        constructed_device['description'] = device['description']
    if "status" in device.keys():
        if device['status'] in valid_devices_status:
            constructed_device['status'] = device['status']
        else:
            print(f"The status of {device['status']} isn't valid. Skipping.")
            continue
    print(f"Adding {device['name']} to Netbox.")
    result = nb_conn.dcim.devices.create(constructed_device)
    
token.delete()
```

Lines 17 - 19 are interesting. Some of the fields in the Netbox GUI are dropdown boxes where you select a valid choice. You can't just freehand the value; it has to be one of the valid choices available. You can use .choices() to get a full list of all valid choices, including the status field. Line 18 gets all the valid values for the status field and adds them to the list called _valid\_device\_status_ so we can check them later. As homework, you should write code to get the choices for devices, prefixes, and device types and explore them a bit.

Lines 26 - 50 all do checking. Does the given device already exists? Does the given type exist? Does the given role exist? Does the given site exist? If they don't, print an error message and go to the next device.

Lines 53 - 63 are basically the same as when we added the sites.

Line 57 is interesting. Remember the list of statuses we got in lines 16 - 18? This line checks the given status against that list to make sure they're valid. If it's not valid, print a message and move on. You can probably modify the script a bit to just default to "active" if you want.

Did you catch the bad data in there? One of the devices is for the Atlanta site, which doesn't exist in Netbox. When you run the script, you'll see this.

```
The site ATL does not exist. Skipping.
```

I guess some of that validation works. Not all of it, though. What if you put in a device without a role or type? This script would try to add a _None_ as the value, which would cause a [KeyError exception](https://www.digitalocean.com/community/tutorials/python-keyerror-exception-handling-examples). This definitely needs more work, but it will get the job done.

Send any 18" white oak logs questions to me.

#python #netbox #pynetbox
