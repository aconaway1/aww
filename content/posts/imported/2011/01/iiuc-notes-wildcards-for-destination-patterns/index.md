---
title: "IIUC Notes - Wildcards for Destination Patterns"
date: 2011-01-18
categories: 
  - "voice"
tags: 
  - "ccna"
  - "destination-pattern"
  - "iiuc"
  - "notes"
  - "regex"
  - "voice"
  - "wildcard"
---

As always, feel free to correct anything that needs correcting or add anything that needs adding.  There is a lot more to the full definition of wildcards, but these are the basics.  Note to \*nix guys:  This isn't regex as you understand it.  Yes, the use of curly braces would be nice, but we don't get that here.

**T**:  Represents anywhere from 0 to 32 digits

> destination-patter 9T  <- matches a 9 followed by 0 - 32 other digits

**Period** : Represents a single digit

> destination-pattern 3...   <- matches a 4-digit number that begins with a 3  
> destination-pattern 91802.......   <- Matches a 12-digit number starting with 91802

**Plus** : Matches from 1 to 32 instances of the previous digit or pattern

> destination-pattern 85+   <- matches an 8 followed by 1 to 32 5s  
> destination-pattern 1+  <- matches 1 to 32 instances of the digit 1

**Percent** or **question mark** :  Matches from 0 to 32 instances of the previous digit or pattern

> destination-pattern 74%   <- matches a 7 followed by 0 to 32 4s  
> destination-pattern 84

**Brackets** : Matches a range or group of digits

> destination-pattern \[2-4\]...   <- matches a 4-digit number that begins with 2, 3, or 4  
> destination-pattern \[159\]...   <- matches a 4-digit number that begins with 1, 5 or 9

**Parenthesis** :  Groups digits together to match with a +, ?, or %

> destination-pattern (61)+   <- matches 61, 6161, 616161...up to 32 61s  
> destination-pattern(555)+   <- matches 555, 555555...up to 32 555s

Remember to think about digit stripping if you're using these on POTS dial peers.  The directive _no digit-strip_ may help you out.
