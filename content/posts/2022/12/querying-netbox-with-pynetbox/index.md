---
title: "Querying Netbox with Pynetbox"
date: 2022-12-11
---

You should be using [Netbox](https://github.com/netbox-community/netbox) or something equivalent. I'm serious. Stop documenting your network with Word docs and Wiki pages and use something where the information can be queried. I've been using Netbox for a couple years, and it's where I keep all that important information about my network. I use it to store hardware inventory, circuit inventory, contact information, site information...all sorts of stuff. Since all this information is already recorded there, I can just query it for the information I need. That includes any time I need to write some Python code to do something on the gear. I use the [pynetbox](https://github.com/netbox-community/pynetbox) module to do that.

_To use pynetbox (or anything that uses API calls to Netbox), you'll need to set up an [API token](https://demo.netbox.dev/static/docs/rest-api/authentication/). I am not qualified to tell you what the best way to manage these are, so we're just going to assume you have an appropriate token configured already._

## The Python Code

We're going to write a short script to get all the devices from the Netbox instance...and here it is![^1]

```python {linenos=true}
import pynetbox
import urllib3

NETBOX_SERVER = "*.*.*.*"
NETBOX_API_KEY = "742*****"

nb_conn = pynetbox.api(url=f"https://{NETBOX_SERVER}", token=NETBOX_API_KEY)
nb_conn.http_session.verify = False
urllib3.disable_warnings()

devices = nb_conn.dcim.devices.all()

for device in devices:
    print(f"DEVICE NAME: {device.name:^10} DEVICE IP: {device.primary_ip4}")
```

Lines 1 & 2 are for [importing](https://docs.python.org/3/reference/import.html) **pynetbox** and [urllib3](https://urllib3.readthedocs.io/en/stable/). We don't have a proper certificate installed on the Netbox server, so we're going to get some warnings and messages about that. We import **urllib3** so that we can suppress those later. I'm working with a VM on my home machine, so I don't really need one for my testing. Your production instance should have one, though.

Next, on lines 4 & 5, we set some [constants](https://www.pythontutorial.net/python-basics/python-constants/) for the server address and API token. Obviously, this is edited for security, but put in your values.

To actually do some real work, we need to define a [pynetbox API](https://pynetbox.readthedocs.io/en/latest/#api) object on line 7. This is the object we'll use to send our queries. In our case, we're defining a variable called **nb\_conn** as the type **pynetbox.api**. This takes at least two arguments -- url and token. In the example here, we're using [Python f-strings](https://realpython.com/python-f-strings/) to send it a proper URL (that is, https://<the IP you declared above>). The token argument is just what we declared earlier.

Lines 8 & 9 are to keep those dang warnings from showing up. Again, if you're in a production environment, put a real cert on the box and don't worry about all this. You could also just leave these lines out and live with the warnings. Either way is fine and won't affect what we're doing.

We've got a **pynetbox.api** object created as **nb\_conn**, so let's ask Netbox to tell us everything about all the devices that it knows about. We'll store that information in a variable called **devices** so we can go through each device and print some information we need.

Note that the directive to get all the devices is **nb\_conn.dcim.devices.all()**. Does any of that look familiar? If you were to open the devices page in the Netbox GUI, you would see that your URI is **dcim/devices**. Coincidence? I think not! A lot (most? all?) of pynetbox consists of a nice mapping of the API, which makes it pretty easy to use. What's your guess on what to use to get a list of sites? How about your IP addresses? **nb\_conn.dcim.sites.all()** and **nb\_conn.ipam.ip\_addresses.all()**[^2]. Easy.

If all went well, we now have a list of all our devices. Let's look closer at the data we have, though. In Python, when you have a more than one of a particular type of object, you keep them in a [list](https://docs.python.org/3/tutorial/datastructures.html). If you were to look at the data type of the variable **devices**, though, you'll see that it's not a list. It's actually a [RecordSet](https://pynetbox.readthedocs.io/en/latest/response.html#pynetbox.core.response.RecordSet). This is a different data type than a list, but, for what we're doing, we can just treat it as if it were just a list. You may see some different behaviors when you get into some more advances stuff. Ask me how [I know](https://github.com/netbox-community/netbox/discussions/6738).

In line 13, we go through that **RecordSet** and address each device as the variable called **device**. If you look at line 14, you'll see that we're calling object attributes for each device (**name** and **primary\_ip4**) as opposed to calling dictionary keys or something. That's because the devices in the **RecordSet** are actually all of the type **Devices**. This is [a Netbox data type](https://demo.netbox.dev/static/docs/core-functionality/device-types/) that includes all the information you may (or may not) want for the device. It includes the Netbox ID, the name, the device type, device role, serial...pretty much anything associated with the device.[^3] Because these are **Devices** object, we can simply call the name and IP address of each device and print them out.

Here's the output from the code above.

```text
DEVICE NAME:  ROUTER A  DEVICE IP: 10.0.0.1/24
DEVICE NAME:  ROUTER B  DEVICE IP: 172.16.2.2/24
DEVICE NAME:  ROUTER C  DEVICE IP: None
DEVICE NAME:  ROUTER D  DEVICE IP: 172.16.3.99/25
DEVICE NAME:  ROUTER E  DEVICE IP: None
```

## Some Points on Netbox Data Types

What other information can we get in the **Devices** data type? If you were to do a **dir()** on one of the devices (i.e., **print(dir(device))**), you can see all the variables that you can pull out of the object. These are the ones that don't start with the underscores (which is another topic all together). You can see name in there somewhere. You can also see primary\_ip4. Here's the output of the **dir()** so you can see what I'm talking about.

```text
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattr__', '__getattribute__', '__getitem__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__key__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_add_cache', '_diff', '_endpoint_from_url', '_full_cache', '_init_cache', '_parse_values', 'airflow', 'api', 'asset_tag', 'cluster', 'comments', 'config_context', 'created', 'custom_fields', 'default_ret', 'delete', 'device_role', 'device_type', 'display', 'endpoint', 'face', 'full_details', 'has_details', 'id', 'last_updated', 'local_context_data', 'location', 'name', 'napalm', 'parent_device', 'platform', 'position', 'primary_ip', 'primary_ip4', 'primary_ip6', 'rack', 'save', 'serial', 'serialize', 'site', 'status', 'tags', 'tenant', 'update', 'updates', 'url', 'vc_position', 'vc_priority', 'virtual_chassis']
```

Be very careful, as I've misled you a little bit here. Data points like name come back as strings. ID comes back as an integer. **Device\_role**, **device\_type**, and **primary\_ip4** (even **primary\_ip** and **primary\_ip6**) come back as nested objects, though. The script output shows a nice IP address in the last column, but it's a trick. The **primary\_ip4** object just happens to be named with the IP and mask so that it looks like a string. The object returned, though, is of type **IPAddresses**. Here's a dir() on one of those for you to see.

```text
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattr__', '__getattribute__', '__getitem__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__key__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_add_cache', '_diff', '_endpoint_from_url', '_full_cache', '_init_cache', '_parse_values', 'address', 'api', 'default_ret', 'delete', 'display', 'endpoint', 'family', 'full_details', 'has_details', 'id', 'save', 'serialize', 'update', 'updates', 'url']
```

As before, the non-underscored items are things you can call. If you wanted to get a string of the IP address, you could call **device.primary\_ip4.address**. This returns a real string that includes the mask in CIDR notation. It would be something like "10.0.0.1/24". You can just split it at the "/" to get the address and mask. Or, if you just want the address, you can do something like.

```python {linenos=true}
for device in devices:
    print(f"DEVICE NAME: {device.name:^10} DEVICE IP: {device.primary_ip4.address.split("/")[0]}")
```

Notice that a couple of the lines in our output above have an address of "None". This is another trick, really. It's not a string of value "None", but, rather, it's an object of [NoneType](https://realpython.com/null-in-python/). In Python, if something doesn't exist, a NoneType object is returned. If a device in Netbox doesn't have a primary IP address assigned, this code will return a value of "None", which is a NoneType object. If we printed the **type()** of each device's **primary\_ip4**, we could verify that.

```text
<class 'pynetbox.models.ipam.IpAddresses'>
<class 'pynetbox.models.ipam.IpAddresses'>
<class 'NoneType'>
<class 'pynetbox.models.ipam.IpAddresses'>
<class 'NoneType'>
```

If you were to run the code with the split in it, it would die with a [TypeError exception](https://docs.python.org/3/library/exceptions.html#TypeError). The code was expecting a type of **IPAddresses**, but it got back **NoneType**. You would have to add some code to check the type to get past that.

## Now What?

Alright. You've got all your devices and a bunch of information for each. Now you can go and actually do something useful. Pull the address and name and generate an list to import into an SSH program. Pull the name and serial for your annual support contract renewal. Get a count of the number of each device you have in the network. Provide your audit team with a full inventory to include in audit preparation.

Send try-except code snippets any questions my way.

[^1]: This is just something I wrote really quickly. Use more [Pythonic techniques](https://docs.python-guide.org/writing/style/) on your finished product.
[^2]: Notice the `_` and not the `-` here. Python doesn't like the dash, so you have to use the underscore.
[^3]: For some reason, interfaces aren't included here. I don't know why.
