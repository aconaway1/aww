---
title: "Using Pynetbox to Create Netbox API Tokens"
date: 2023-01-12
categories: 
  - "netbox"
  - "python"
tags: 
  - "api"
  - "netbox"
  - "pynetbox"
  - "python"
  - "token"
---

As a warning to everyone, I am not a developer. I am a network engineer who is trying to do some automation stuff. Some of what I'm doing sounds logical to me, but I would not trust my own opinions for production work. I'm sure you can find a [Slack channel](https://netdev.chat/) or [Mastodon instance](https://infosec.exchange/explore) with people who can tell you how to do things properly.

[The last time](https://aconaway.com/2022/12/11/querying-netbox-with-pynetbox/), I talked about using [pynetbox](https://github.com/netbox-community/pynetbox) to make queries to [Netbox](https://github.com/netbox-community/netbox). This was a very simple example, and one of the things that bugged me the most about it was the [API token](https://docs.netbox.dev/en/stable/integrations/rest-api/#tokens). In that post, we used a statically-assigned API token where I went into the Netbox GUI and generated one for myself. I think I may have even noted that this was definitely not the best way to handle those things. A possibly-better way to do it is to use your username and password on Netbox to generate a token for yourself. This would a token that you then delete when you're done.

How is this better? The static tokens are just that -- they're static. If you generate your token, then anyone who has it can use it to scrape or change data until the token expires. Guess how long you get to use a token by default. It's forever. Unless you set an expiry, then the token is valid for as long as the Netbox instance is up. We can set an expiration, but you're exchanging that security for the management overhead of dealing with token rotation. There's a whole conversation that should be had on this topic, but I really want to move on.

It seems to me -- the person who is not the authority on such things -- that a better way would be using a user/pass combo to create a new token each and every time. First, the token isn't sitting in a [Python](https://www.python.org/) or [YAML](https://yaml.org/) file in a public [Github](https://github.com/) repo somewhere because you were careless with your pushes. Also, if you "did it right" and have [Netbox auth against an external source](https://demo.netbox.dev/static/docs/administration/authentication/), your user/pass combo is probably managed in other ways. I'm thinking something like Active Directory auth where you have proper password policies. Another whole conversation that I need to skip for now.

How about some code to see through the mud here? This code is all in [my Github repo](https://github.com/aconaway1/blog-pynetbox) for you to look at and share. Here's the environment I'm running.

```text
Python version: 3.9.10
Pynetbox version: 7.0.0
Netbox version: 3.4.2 (running in Docker)
```

Let's start with a YAML file called _env.yml_ that contains all the environment information like URL and user/pass info.

```yaml
username: admin
password: admin
netbox_url: "http://*.*.*.*"
```

"But, Aaron," you say. "You just went from your token in the repo to your user and pass in the repo. That's stupid." You are correct. You should probably derive the user and pass from somewhere, but that can be as simple as [prompting the user to enter that info](https://docs.python.org/3/library/getpass.html). I'm suspending my disbelief on this front for now to avoid having to tell you to implement a full vault solution.

Now for the Python. This is the file _pynetbox\_create\_token.py_.

```python {linenos=true}
import pynetbox
import pprint
import yaml

ENV_FILE = "env.yml"

with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)

nb_conn = pynetbox.api(url=env_vars['netbox_url'])

token = nb_conn.create_token(env_vars['username'], env_vars['password'])

pprint.pprint(dict(token))

token.delete()
```

This is pretty easy stuff, but I'll walk you through it.

Lines 1 -3 are the imports. Basic Python stuff.

Lines 5 - 8 import the environment information. We load the data in the _env.yml_ above and put that into a dictionary called _env\_vars_.

Line 10 instantiates an api class through which we talk to Netbox. Notice that we're using the _env\_vars_ dictionary to provide the URL.

Line 12 creates the token. You probably figured that out with the whole "create\_token" thing. Here, we're passing the username and password that we loaded in from _env.yml_. When we create a token, it is automatically associated with the api instance we created in line 10. From this point forward, any calls to Netbox using this connection will use this token.

Line 14 just prints the contents of the token we created. This just shows that we did indeed get a new token.

Line 16 deletes that token. We're using the token object itself to delete it. If this line doesn't run, the token can still be used. This kinda defeats the whole purpose, eh? Remember last night when you were chasing a bug for 3 hours? Every time the script crashed before deleting the token, you put another orphaned token in the list. Those need to be dealt with. More on that later.

Here's what the output looks like when we run it.

```text
{'allowed_ips': None,
 'created': '2023-01-12T18:08:07.816089Z',
 'description': '',
 'display': '<REDACTED>b0f6',
 'expires': None,
 'id': 168,
 'key': '<REDACTED>b0f6',
 'last_used': None,
 'url': 'http://*.*.*.*/api/users/tokens/168/',      
 'user': {'display': 'admin',
          'id': 1,
          'url': 'http://*.*.*.*/api/users/users/1/',
          'username': 'admin'},
 'write_enabled': True}
```

Run it again to show that the token is different each time. Of course, make sure to verify the token doesn't still live on Netbox after it runs.

As for those orphaned tokens, they can be a problem. I did some troubleshooting on an unrelated script the other day, and I wound up generating a couple dozen of those things. Way too many to delete manually, so I wrote a script to delete them all.

This is _pynetbox\_clear\_all\_tokens.py_ in the repo. It get a list of all the tokens then nukes them all except the one that we created when running this script; that token gets deleted at the end. This could be very destructive if run in production since it will delete all your production tokens as well.

```python {linenos=true}
import pynetbox
import yaml

ENV_FILE = "env.yml"

with open(ENV_FILE) as file:
    env_vars = yaml.safe_load(file)

nb_conn = pynetbox.api(url=env_vars['netbox_url'])

my_token = nb_conn.create_token(env_vars['username'], env_vars['password'])

all_tokens = nb_conn.users.tokens.all()

for token in all_tokens:
    if token.id == my_token.id:
        print("Don't delete your own token, silly person!")
        continue
    print(f"Deleting token {token.id}")
    token.delete()

my_token.delete()
```

Line 1 - 11 are the same as above. Import libraries, import data, connect to Netbox, etc.

Line 13 gets all the tokens so that we can delete them in lines 15 - 20. Line 16 needs a mention here. When we go through the list of tokens, if a token in the list has the same ID as the one we generated in line 11, then don't delete it. The rest we delete. If we happen to delete ours before finishing the list, then we'll be deauthed, so we need to skip ourselves.

The we delete our own token on line 22.

Be sure to send any extra firewood questions my way.
