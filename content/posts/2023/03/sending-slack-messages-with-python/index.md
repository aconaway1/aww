---
title: "Sending Slack Messages with Python"
date: 2023-03-15
categories: 
  - "automation"
  - "netbox"
  - "pynetbox"
  - "python"
tags: 
  - "automation"
  - "logging"
  - "netbox"
  - "pynetbox"
  - "python"
  - "slack"
---

Here's a quick summary of what we've talked about in the last few posts -- all with [Python](https://www.python.org/).

- We've [asked Netbox to provide some info](https://aconaway.com/2022/12/11/querying-netbox-with-pynetbox/) using [pynetbox](https://pynetbox.readthedocs.io/en/latest/).

- We've [added stuff to Netbox](https://aconaway.com/2023/01/17/adding-stuff-to-netbox-with-pynetbox/) using pynetbox.

- We've [updated](https://aconaway.com/2023/01/25/updating-stuff-on-netbox-with-pynetbox/) and [deleted stuff](https://aconaway.com/2023/02/24/deleting-stuff-from-netbox-with-pynetbox/) in Netbox using pynetbox.

- We've [logged our messages with Python logging](https://aconaway.com/2023/02/26/using-python-logging-to-figure-out-what-you-did-wrong/).

This is all fine and dandy, but I would guess that you're not the only engineer in the company and production maintenance scripts don't run off of your laptop. We need a way to let a group of people know what's happening when one of your scripts is run. And please don't say email. Email has been worthless for alerting for over a decade, and there are better ways to do it. Search your feelings...you know it to be true!

At this point, we all have some magic messaging tool that someone in upper management decided we needed. There are others out there, but I would guess that the majority of companies are using Microsoft Teams or Slack with some Webex Teams sprinkled in there. These are great tools with lots of features and are probably not yet overused to point of making users ignore the messages, so they are great candidates for telling others what you broke through automation.

It's also a good place to keep track of the history of the chaos you've caused. Instead of having a log sitting on a disk on a server somewhere, the log messages are recorded for posterity for everyone to see at any time. This obviously could be good or bad, but it's better than someone calling you at 3am asking if your tasks have done something egregious. And, yes, logs are a part of IT life, and auditors will want to see them every year or two when they come onsite. We all have to keep our logs, but we can still send updates via Slack (or whatever) as well.

We're going to talk about Slack here because it's free for me to use, and I've already got it set up. The concepts are the same in MS Teams or WE Teams, though. Like...pretty much exactly the same.

We're only going to talk about plaintext updates to the channel -- just as we did with print statements and logging handlers. You can do fancier stuff like text formatting and sections and actions and polls if you want, and there are libraries out there that will make the fancier things easier for you. For now, though, we're keeping it simple. Maybe we can do that later, but, for now, let's just send messages as we would to a log or the screen.

The first thing to do is to set up a channel for an incoming webhook. I have a Slack workspace for myself and created a channel called `#automation-testing` where I want these messages to land. I went into the channel config and added an app called `Incoming Webhooks`, . When it's installed, Slack provides a long URL. Copy this down somewhere so you have it since this is where you're going to send your updates. There's sort of a "security through obscurity" thing going on, so there's no authentication involved. \*\*cough\*\* Security! \*\*cough\*\*

Anyone who has this URL can post to your channel, so it needs to be kept safe. You shouldn't put it directly in your code. I wound up putting it in the `device_creds.yml` file along with the username and password for logging into the gear. The key I used is `slack_url`. My mother tells me I'm very creative. We'll import all the credential information into so we can use that URL later. And make sure that creds file is in your `.gitignore` file so it doesn't get published to your repository. Ask me about the email I got from Slack the other day that said "We see you published a webhook URL to GitHub, so we're regenerating the URL for you." Oops. Glad they were looking out for me.

To some code, I guess. Let's refactor an easy one we've already done. How about the one where we delete all the Netbox API tokens that we're not actively using? We won't change the logic; we're going to just upgrade from print statements to Slack updates...and maybe advance a little toward being a "proper" Python developer.

I'm running Python `3.9.10` today. All this code is available on [my Github repo](https://github.com/aconaway1/blog-pynetbox) for you to freely steal. I reiterate that I am not a developer and make no guarantees with this code. You should ask someone who knows what they're doing to review any of this before you put it into production. I am also learning as I go, so I've noticed my own code is changing as times moves along. Don't freak out if there's some different structure or actual comments compared to the last time we looked at this code.

```python {linenos=true}
# pynetbox_clear_all_tokens_slack.py
"""
Deletes all API tokens from Netbox
"""
import yaml
import requests
import pynetbox

def send_to_slack(message: str, slack_url: str):
    """
    Send a message to Slack

    Args:
        message (str): The message text
        slack_url (str): The URL to post to

    Returns:
        bool: Whether or not the message was sent successfully
    """
    headers = {"text": message}
    post_result = requests.post(url=slack_url, json=headers, timeout=10)
    if post_result.status_code == 200:
        return True
    return False

ENV_FILE = "env.yml"
CREDS_FILE = "device_creds.yml"

def main():
    """
    Run this
    """
    with open(ENV_FILE, encoding="UTF-8") as file:
        env_vars = yaml.safe_load(file)

    with open(CREDS_FILE, encoding="UTF-8") as file:
        creds = yaml.safe_load(file)

    nb_conn = pynetbox.api(url=env_vars['netbox_url'])

    my_token = nb_conn.create_token(env_vars['username'], env_vars['password'])

    all_tokens = nb_conn.users.tokens.all()

    send_to_slack(message="Looking for old tokens in Netbox.",
                                 slack_url=creds['slack_url'])
    found_old_tokens = False

    for token in all_tokens:
        if token.id == my_token.id:
            send_to_slack(message="Don't delete your own token, silly person!",
                                         slack_url=creds['slack_url'])
            continue
        send_to_slack(message=f"Deleting token {token.id}",
                                     slack_url=creds['slack_url'])
        token.delete()
        found_old_tokens = True

    my_token.delete()

    if not found_old_tokens:
        send_to_slack(message="Found no old tokens to delete.",
                      slack_url=creds['slack_url'])

if __name__ == "__main__":
    main()

```

Like I said, the basic function of the script is the same. We've only added some Slack functionality to replace the print statements, which are done through the `send_to_slack` function defined in line 9. There we set up the JSON body, send the post to the given URL, and return a boolean based on the status code we get back. Not too difficult here.

Some of the minor changes include importing in the creds from YAML on line 36. The `slack_url` value from that dictionary will be sent to the function for posting. We've also added a tracking variable called `found_old_token`s to see if we found something to delete. If we didn't, we'll published a Slack message that says we didn't find any...just so we know the process finished and didn't crash. I like closure.

I do need to mention that we did some restructuring of the script to make it more [Pythonic](https://docs.python-guide.org/writing/style/). See line 65 where we did the whole `__name__` thing, which makes us look fancy. This just says to run the function `main()` if this script is called from the command line. It's not really important for functionality here, but it's good practice for the future. Things like this will help us when we start taking all this code and putting into a custom module later.

We also included some [type hints](https://realpython.com/lessons/type-hinting/) in the `send_to_slack` function. What are these? I mean, they tell you what type of variable to use, but I'm not sure what they really do for us here. Maybe when we've got a fully-developed system for automatically maintaining our Netbox data we can see a benefit. I think I just watch too many YouTube videos on Python at this point.

Logging to Slack is a lot better in my opinion. I like to let everyone know what's going on. I also like to yell at them at 3am when they call me because they didn't use the tools properly even though I gave them instructions. Most importantly, though, is the fact that it's not email.

As an afterthought, here's proof that this code actually does something. Not proof that it works optimally or that it even works well. Just works.

<figure>

![](images/image.png)

<figcaption>

Screenshot showing a Slack update for deleting tokens

</figcaption>

</figure>

Send any air traffic scanners question my way.

#python #netbox #pynetbox #automation #slack #logging
