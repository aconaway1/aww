---
title: "Deleting Stuff from Netbox with Pynetbox"
date: 2023-02-24
categories: 
  - "automation"
  - "netbox"
  - "pynetbox"
  - "python"
  - "sot"
tags: 
  - "automation"
  - "netbox"
  - "neteng"
  - "pynetbox"
  - "python"
  - "sot"
---

As a warning to everyone, I am not a developer. I am a network engineer who is trying to do some automation stuff. Some of what I’m doing sounds logical to me, but I would not trust my own opinions for production work. I’m sure you can find a [Slack channel](https://netdev.chat/) or [Mastodon instance](https://infosec.exchange/explore) with people who can tell you how to do things properly.

We've added stuff and updated stuff, so let's delete some stuff. "Hey, man...you already did that," you say? You're right! When [we started creating API tokens based on user/pass](/posts/2023/01/using-pynetbox-to-create-netbox-api-tokens/), we made sure to delete the token at the end. That means we should all be professional [pynetbox](https://pynetbox.readthedocs.io/en/latest/) deleters, then, right? :)

When using pynetbox, we mostly deal with object. When updating, we get the object, make changes, then save it back to [Netbox](https://docs.netbox.dev/en/stable/). We don't say "update object 38718 with a new widget"; you actually manipulate an object. When we delete something, we do the same thing...get the object and delete it. Here's a snippet of the token cleanup script to show that.

```python {linenos=true}
<SNIP>
all_tokens = nb_conn.users.tokens.all()

for token in all_tokens:
    <SNIP>
    token.delete()

<SNIP>
```

Don't think on the logic of this code too much. I removed a lot of stuff. LOL

We get all the tokens, which come to us as a RecordSet. We then go through each token (which is the Record) and delete it with the .delete() function. Super-easy. A little too easy. Let's try harder. Well, the .delete() won't be harder...just the logic around when to use it.

Let's say that our Denver switches have been upgraded. We have the older devices in Netbox with a status of `decommissioning`, but we don't want to remove them just yet since we have to make sure they're written off the books and recycled properly. We put some text in the `description` field that says "delete after <DATE>" so that everyone knows to keep this device in Netbox until then. Here are our devices.

```text
Name:   DEN-OLDFRWL01 Status: Decommissioning Desc: delete after 12 Jan 2022
Name: DEN-OLDSWITCH01 Status: Decommissioning Desc: delete after 16 Feb 2023
Name: DEN-OLDSWITCH02 Status: Decommissioning Desc: delete after 16 Feb 2023
Name: NYC-OLDROUTER01 Status: Decommissioning Desc: delete after 22 Mar 2023
```

It looks like the old firewall was decommissioned last year some time, but it's still in here. \*tsk, tsk\* And it looks like something in NYC is being decommissioned. Interesting. All the target devices have a "delete after" date, though, so we can remove them as needed.

Things that we'll need to do:

- Get the devices with a status of `decommissioning`

- Pull the "delete after" date out and figure out if we're past that yet

- Delete devices that need to be removed.

We're running [Python](https://www.python.org/) with pynetbox and querying our local Netbox server. Here's what we're running. As always, the code is in [my Github repo](https://github.com/aconaway1/blog-pynetbox).

```text
Python         :  3.9.10
Pynetbox       :  7.0.0
Netbox version :  3.4.3 (Docker)
```

```python {linenos=true}
# pynetbox_decom_devices.py
"""
Deletes devices from Netbox after an indicated date in the description field
"""
import re
from datetime import datetime
import pynetbox
import yaml

ENV_FILE = "env.yml"

# Load the environment information
with open(ENV_FILE, encoding="UTF-8") as file:
    env_vars = yaml.safe_load(file)
# Connect to Netbox and get a token
nb_conn = pynetbox.api(url=env_vars['netbox_url'])
my_token = nb_conn.create_token(env_vars['username'], env_vars['password'])
# Get a list of all the devices with a status of "decommission"
decommed_devices = nb_conn.dcim.devices.filter(status="decommissioning")
# Get today's date to do some math on later
todays_date = datetime.now()
# Go through the list of devices returned
for decommed_device in decommed_devices:
    # Do a regex search for "delete after " with some text
    date_search = re.search("delete after (.+)", decommed_device.description)
    # If you found a match to the regex
    if date_search:
        # Format the "delete after" date
        delete_date = datetime.strptime(date_search.group(1), '%d %b %Y')
        # If today's date is after the "delete after" date
        if todays_date > delete_date:
            # Tell us we're going to delete stuff
            print(f"Deleting the device {decommed_device.name}...", end="")
            # Delete the device. You should get a True back
            result = decommed_device.delete()
            # If everything went fine
            if result:
                print("...deleted.")
            # If it didn't delete
            else:
                print("failed.")
# Delete the token we're using to work with Netbox
my_token.delete()

```

I've got some nice comments in the code this time, so you should be able to follow along. "Nice" might be too strong of a word. I personally think it makes the text too unreadable like this, but that's probably because I put too many in there. There's a happy medium somewhere! \*sigh\*

Here are the highlights.

Line 21 gets the date and time for right now. This is a [datetime](https://docs.python.org/3/library/datetime.html) object that we can do math on later.

Line 25 is a [regex](https://docs.python.org/3/library/re.html) search for the string in the description. Regex is its own beast, and everyone is intimidated by it at first. This just says to look at the description (decommed\_device.description) and save anything that comes after "delete after " to use later.

Line 27 checks if you found a match to the regex on line 25.

Line 29 takes the date from the device description and converts it to a datetime object so we can do some date math. Check out [strptime](https://www.geeksforgeeks.org/python-datetime-strptime-function/) for details on the formatting. The time is included in this object at midnight of that day.

Line 31 is the comparison of today's date and time with date in the description of the device. If the decom date is in the past, we'll delete it.

Line 35 deletes the device.

Here's the output.

```text
Deleting the device DEN-OLDFRWL01......deleted.
Deleting the device DEN-OLDSWITCH02......deleted.
Deleting the device DEV-OLDSWITCH01......deleted.
```

The old firewall and both old switches in Denver were removed. The old router in New York gets skipped since we haven't reached the delete date yet. Everything seems to work fine.

Send any ~~ADS-B receivers~~ questions my way.

#python #netbox #pynetbox #automation #sot #neteng
