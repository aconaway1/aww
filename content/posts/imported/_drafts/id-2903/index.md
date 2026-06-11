---
title: "Pynetbox TLDR"
draft: true
---

## Basics

- Import the packages

- Import the data we need

- Connect to Netbox

- Delete the token we created

```python
import pynetbox
import yaml
 
ENV_FILE = "env.yml"
 
with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)
     
nb_conn = pynetbox.api(url=env_vars['netbox_url'])
 
token = nb_conn.create_token(env_vars['username'], env_vars['password'])

...

token.delete()
```

## Adding

Add a record to Netbox. See your Swagger documentation for details.

```
#A generic object
my_record = { "name": "My object", "slug": "myobject", ... }

# Add a site
result = nb_conn.dcim.sites.create(my_record)
# Add a prefix
result = nb_conn.ipam.prefixes.create(my_record)
# Etc.
```

## Querying

Get one device

```
device =nb_conn.dcim.devices.get(q="my_device)
```

Get all the firewalls

```
devices = nb_conn.dcim.devices.filter(role="firewall")
devices = nb_conn.dcim.devices.filter(q="firewall")
```

## Deleting

Remove a record from Netbox.

- Get the object from Netbox

- Delete it

```
decommed_device =nb_conn.dcim.devices.get(q="my_device)
result = decommed_device.delete()
```

## Updating

Update an existing record in Netbox

- Get the object from Netbox

- Make your changes

- Update the object

```
updated_device =nb_conn.dcim.devices.get(q="my_device)
update_device["description"] = "My new description"
result = updated_device.update()
```

## Mapping
