---
title: "Using Python Logging to Figure Out What You Did Wrong"
date: 2023-02-26
categories: 
  - "automation"
  - "python"
tags: 
  - "automation"
  - "devops"
  - "logging"
  - "neteng"
  - "python"
  - "troubleshooting"
---

As a warning to everyone, I am not a developer. I am a network engineer who is trying to do some automation stuff. Some of what I’m doing sounds logical to me, but I would not trust my own opinions for production work. I’m sure you can find a Slack channel or Mastodon instance with people who can tell you how to do things properly.

I use too many print statements to figure out what's going on. Get an object and print it to screen to make sure it's right. Do a calculation and print the result. There are so many print statements in my code that I had to start using a debug variable to tell it when to print stuff. I even use that technique in my functions.

```python {linenos=true}
# Don't do stuff like this
def myFunc(string_to_return, debug=False):
    if debug:
        print(f"Returning \"{string_to_return}\"")
    return string_to_return

local_debug = True
string_to_send = "Aaron wastes a lot of time with print statements."

if local_debug:
    print(f"I'm sending \"{string_to_send}\"")
myString = myFunc(string_to_send, debug=True)
print(myString)
```

It's painful to look at this code. I need a better solution, and I found [Python's logging](https://docs.python.org/3/library/logging.html) module.

Very simply, you associate your messages with one of five logging levels (debug, info, warning, error, critical) and declare that you want to see messages at or above that level. It's very much like syslog, so you probably already know how all this will work.

How about a simple example? Let's write some code that goes through the sites\_to\_load file to make sure each site is configured with a time zone. That is, look for the key time\_zone in the YAML file. We'll use the info level to keep track of the status of the code and send error-level messages if something goes wrong.

I'm running [Python 3.9.10](https://www.python.org/downloads/release/python-3910/) for today. All my code is in [my GitHub repo](https://github.com/aconaway1/blog-pynetbox).

```python {linenos=true}
# logging_check_sites_1.py
import yaml
import logging

SITES_FILE = "sites.yml"

logging.basicConfig(level=logging.DEBUG)

with open(SITES_FILE) as file:
    sites_to_load = yaml.safe_load(file)
    
for site in sites_to_load:
    if not "time_zone" in site.keys():
        logging.error(f"The site {site['name']} does not have a time zone configured.")
```

Line 7 sets the level we want to see. Note that `logging.DEBUG` is an integer definition included with the module. Obviously, you have `logging.INFO`, `logging.WARNING`, `logging.ERROR`, and `logging.CRITICAL` as well.

Line 14 sends an error message to the logging module. Since we said we wanted to see debug messages, we'll see this message printed. If we had set the level to critical, nothing would be printed at all.

Let's do something more advanced. How about check physical address and time zone, but this time we have debug messages that help us track what we're doing. We also want to add a timestamp to the messages so we know when things happen. And, just to finish it off, let's log everything to a file called check\_sites.log.

```python {linenos=true}
# logging_check_sites_2.py
import yaml
import logging

SITES_FILE = "sites.yml"
LOG_FILE = "check_sites.log"
LOG_FORMAT = '%(asctime)s - %(levelname)s - %(message)s'

logging.basicConfig(filename=LOG_FILE,
                    level=logging.DEBUG,
                    format=LOG_FORMAT)

with open(SITES_FILE) as file:
    sites_to_load = yaml.safe_load(file)
    
for site in sites_to_load:
    if not "name" in site.keys():
        logging.critical(f"Found a site with no name:{site}")
        continue
    logging.debug(f"Checking config for {site['name']}.")
    if not "physical_address" in site.keys():
        logging.error(f"The site {site['name']} does not have a physical address configured.")
    else:
        logging.debug(f"Site {site['name']} has a physical address configured.")
    if not "time_zone" in site.keys():
        logging.error(f"The site {site['name']} does not have a time zone configured.")
    else:
        logging.debug(f"Site {site['name']} has a time zone configured.")
```

Line 6 defines the log file we're going to use. We'll see that in a bit.

Line 7 defines a string that we'll pass as the format we want to use. In this case, we want to see the time (asctime), the logging level, and the message. There are lots of other attributes you can use, so check out [this list](https://docs.python.org/3/library/logging.html#logrecord-attributes) to see what you want to use and how to use them properly.

Lines 9 - 11 configure the logging module. I think you can figure out what each argument is. :

_Note that files are opened in append mode like this. There is a way to change that, but that's beyond the scope of what we're doing today._

We've actually got three different levels of logging message in this one. Lines 20, 24, and 28 are our debug messages that fire off so we can follow along at home. Lines 22 & 26 are our error messages that we record when things are missing. Line 18 gives us a critical message when the name is missing from the record.

Here's the `check_sites.log` file after we run this terrible code.

```
2023-02-26 09:44:16,728 - DEBUG - Checking config for NYC.
2023-02-26 09:44:16,728 - DEBUG - Site NYC has a physical address configured.
2023-02-26 09:44:16,728 - DEBUG - Site NYC has a time zone configured.
2023-02-26 09:44:16,728 - DEBUG - Checking config for CHI.
2023-02-26 09:44:16,728 - DEBUG - Site CHI has a physical address configured.
2023-02-26 09:44:16,728 - ERROR - The site CHI does not have a time zone configured.
2023-02-26 09:44:16,728 - DEBUG - Checking config for STL.
2023-02-26 09:44:16,728 - DEBUG - Site STL has a physical address configured.
2023-02-26 09:44:16,729 - ERROR - The site STL does not have a time zone configured.
2023-02-26 09:44:16,729 - DEBUG - Checking config for DEN.
2023-02-26 09:44:16,729 - DEBUG - Site DEN has a physical address configured.
2023-02-26 09:44:16,729 - DEBUG - Site DEN has a time zone configured.
2023-02-26 09:44:16,729 - DEBUG - Checking config for PHX.
2023-02-26 09:44:16,729 - DEBUG - Site PHX has a physical address configured.
2023-02-26 09:44:16,729 - ERROR - The site PHX does not have a time zone configured.
2023-02-26 09:44:16,729 - DEBUG - Checking config for LAX.
2023-02-26 09:44:16,729 - DEBUG - Site LAX has a physical address configured.
2023-02-26 09:44:16,729 - DEBUG - Site LAX has a time zone configured.
2023-02-26 09:44:16,729 - CRITICAL - Found a site with no name:{'description': 'DELETE ME'}
```

It looks like the whole thing ran in 2 ms, but that's not true. This just shows that 2 ms elapsed between the first messages and the last.

We can check the error and critical messages to see what went poorly. It looks like Chicago, Saint Louis, and Phoenix don't have time zones configured. It also looks like someone added an incomplete site to the end of the file to generate a critical message for us. How kind of them.

We didn't see it here, but there are times when you may see logs from imported modules show up. I saw this when I was [updating my token deletion script to use logging instead of print statements](https://github.com/aconaway1/blog-pynetbox/blob/master/pynetbox_clear_all_tokens_logging.py). I was seeing log messages from the connectionpool module in urllib3.

```
2023-02-26 10:01:41,229 - connectionpool - http://x.x.x.x:8000 "POST /api/users/tokens/provision/ HTTP/1.1" 201 407
2023-02-26 10:01:41,287 - connectionpool - http://x.x.x.x:8000 "GET /api/users/tokens/?limit=0 HTTP/1.1" 200 484
2023-02-26 10:01:41,287 - pynetbox_clear_all_tokens_logging - Skipping 228 since it the one I'm using right now.
2023-02-26 10:01:41,339 - connectionpool - http://x.x.x.x:8000 "DELETE /api/users/tokens/228/ HTTP/1.1" 204 0
```

I'm not deep enough in that code to tell you how and why, but just don't freak out when you see it. You may want to include `%(pathname)s` or `%(module)s` to help figure out what's going on with that.

Send any ways to replace email questions to me.

#python #automation #neteng #devops #logging #troubleshooting
