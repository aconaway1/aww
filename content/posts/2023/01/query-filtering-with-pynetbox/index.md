---
title: "Query Filtering with Pynetbox"
date: 2023-01-16
categories: 
  - "automation"
  - "netbox"
  - "pynetbox"
  - "python"
tags: 
  - "filter"
  - "netbox"
  - "pynetbox"
  - "python"
  - "query"
---

As a warning to everyone, I am not a developer. I am a network engineer who is trying to do some automation stuff. Some of what I’m doing sounds logical to me, but I would not trust my own opinions for production work. I’m sure you can find a [Slack channel](https://netdev.chat/) or [Mastodon instance](https://infosec.exchange/explore) with people who can tell you how to do things properly.

[A bit ago](/posts/2022/12/querying-netbox-with-pynetbox/), we talked about getting information out of Netbox with Pynetbox. The example was very simple, but I'm afraid the real world dictates that querying every device every time is not very efficient or manageable. At some point, we'll need to ask for a subset of everything, so let's look at filtering.

We used .all() last time. It's pretty obvious what that gives us. If we don't want everything in the world returned, we can use .filter() along with some parameters to limit that result. Let's get to an example.

We want to print a report of all devices with hostname and role. The devices should be grouped by site. This means we need to get a list of sites, go through that list, get the devices there, and print what we want. Here it goes.

Here's the environment I'm running. All this code is in [my Github repo](https://github.com/aconaway1/blog-pynetbox).

```text
Python         :  3.9.10 
Pynetbox       :  7.0.0  
Netbox version :  3.4.2  (Docker)
```

```python {linenos=true}
### pynetbox_query_filter_1.py
import pynetbox
import yaml

ENV_FILE = "env.yml"

with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)

nb_conn = pynetbox.api(url=env_vars['netbox_url'])
token = nb_conn.create_token(env_vars['username'], env_vars['password'])

sites = nb_conn.dcim.sites.all()

for site in sites:
    site_header = f"\nDevices at site {site.name} ({site.description})"
    print(site_header)
    print("-" * len(site_header))
    devices = nb_conn.dcim.devices.filter(site_id=site.id)
    if len(devices) < 1:
        print("No devices.")
        continue
    for device in devices:
        print(f"{device.name:^20} {device.device_role.name:^20}")
        
token.delete()
```

Lines 2 & 3 are our imports. Basic Python stuff there.

Lines 5 - 11 and 26 are from [a previous post about generating keys in pynetbox](/posts/2023/01/using-pynetbox-to-create-netbox-api-tokens/).

Line 13 gets all the sites.

Line 15 goes through each site to do the magic.

Lines 16 - 18 just print some header info. Line 18 is a pretty cool trick for having the right number of underscores.

Line 19 is the one we care about right now. This asks Netbox to provide all the devices that have a site ID equal to the site we're looking at. We're using "site\_id" as the argument here, but you can use any field you want to filter on. Status, rack ID, manufacturer, tags, create time..the list goes on. You can have more than one argument, too, which is pretty great.

Lines 20 - 22 check if we actually got devices for a site. If not, we just say "No devices." and move on to the next site using _continue_.

Lines 23 & 24 go through the devices for this site and print the name and role. They use some fancy formatting to make it look nice.

Here's the output from running this.

```text
Devices at site CHI (Chicago)
------------------------------
     CHI-CSW01           CORE_SWITCH     
     CHI-RTR01           INET_ROUTER     

Devices at site DEN (Denver)
-----------------------------
     DEN-CSW01           CORE_SWITCH     
     DEN-RTR01            WAN_ROUTER     

Devices at site LAX (Los Angeles)
----------------------------------
     LAX-CSW01           CORE_SWITCH     
     LAX-FRW01             FIREWALL      
     LAX-RTR01            WAN_ROUTER

Devices at site NYC (New York City)
------------------------------------
     NYC-CSW01           CORE_SWITCH     
     NYC-FRW01             FIREWALL      

Devices at site PHX (Phoenix)
------------------------------
     PHX-CSW01           CORE_SWITCH     
     PHX-RTR01           INET_ROUTER     

Devices at site STL (Saint Louis)
----------------------------------
     STL-ASW01            ACC_SWITCH     
     STL-CSW01           CORE_SWITCH     
     STL-FRW01             FIREWALL
```

I think you can probably figure out how to do it, but check out [pynetbox\_query\_filter\_2.py](https://github.com/aconaway1/blog-pynetbox/blob/master/pynetbox_query_filter_2.py) in the repo to see a .filter() with more than one argument.

When you use .filter(), pynetbox returns a _RecordSet_ (or None if there's nothing to get), even if the query returns a single result. This means that you have to loop through the result each time you use filter(). If you want to get back a single _Record_, then use .get().

.get() takes the same arguments as .filter(), but the arguments must be specific enough for Netbox to return a single result. That is, the total sum of all the arguments must be unique across Netbox. If your arguments match more than one result, you get an error like this one.

```text
ValueError: get() returned more than one result. Check that the kwarg(s) passed are valid for this endpoint or use filter() or all() instead.
```

You can keep stacking arguments until it's unique ("_device\_role="firewall", site="NYC", rack="RACK1", position=14_", etc.), but that's not very scalable or even worth your time to figure out if the query is unique enough. Because of that, I tend to only use .get() when I know the object ID (id=X). Since this is assigned by Netbox and can't be reused., using it assures us that the query is specific enough.

.get() has its limitations but it's still very useful, though. If a Netbox object has a reference to another Netbox object, the result will include some information about that referenced object. That's a terrible sentence. Things might be clearer if we look at the result from a query.

```text
{'airflow': None,
 'asset_tag': None,
 'cluster': None,
 'comments': '',
 'config_context': {},
 'created': '2023-01-16T14:43:40.208662Z',
 'custom_fields': {},
 'device_role': {'display': 'FIREWALL',
                 'id': 7,
                 'name': 'FIREWALL',
                 'slug': 'firewall',
                 'url': 'http://*.*.*.*/api/dcim/device-roles/7/'},
<SNIP>
```

This is a snip of a device record. You can see _device\_role_ isn't just a string result; it's got some information about the role for this device, including the ID of that role. Now we have a piece of information that we can use as a query for a specific device.

Here's some code to get shipping information for the devices with a "planned" status. The real-world scenario is that you have configured these devices and need to ship them out to the right site for install.

```python {linenos=true}
### pynetbox_query_filter_3.py
import pynetbox
import yaml

ENV_FILE = "env.yml"

with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)

nb_conn = pynetbox.api(url=env_vars['netbox_url'])
token = nb_conn.create_token(env_vars['username'], env_vars['password'])

devices = nb_conn.dcim.devices.filter(status='planned')

for device in devices:
    site = nb_conn.dcim.sites.get(id=device.site.id)
    print(f"Ship {device.name} to:\n{site.physical_address}\n")
        
token.delete()
```

Line 13 is a .filter() that retrieves only devices in a "planned" state. This is a _RecordSet_, so you have to iterate through to get anything useful.

Line 16 is the .get(). We get the site ID returned with the device (device.site.id), so we can use that in a .get() argument to get a single result. This is a _Record_, so you can use it directly.

The rest of the lines are pretty much the same as above, so I'll skip the explanation. Here's the output.

```text
Ship LAX-RTR01 to:
123 Main Street
Los Angeles, CA 90001

Ship PHX-RTR01 to:
123 Main Street
Phoenix, AZ 73901

Ship STL-FRW01 to:
123 Main Street
Saint Louis, MO 63101
```

In summary, filtering is good. Carry on.

Send any ~~soapmaking tips~~ questions my way.
