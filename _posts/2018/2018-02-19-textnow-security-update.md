---
layout: post
title: "TextNow Security Update"
tagline: "TextNow Security Update and BlackBox Penetration Test by GlitchWitch.io"
description: "TextNow Security Update and BlackBox Penetration Test by GlitchWitch.io"
tags: [clients, daya breach, s3 bucket, pentest, responsible disclosure]
date:   2018-02-19
comments: false
image:
  feature: blog/4/feature.jpg
  hero: blog/4/header.jpg
---
__The following is a joint post made by TextNow and GlitchWitch.io__

## ﻿Summary
On September 30th, 2017, Security Consultant — [GlitchWitch.io](https://glitchwitch.io/) — discovered a misconfigured Amazon Web Services S3 storage bucket operated by [TextNow](https://textnow.com/). The bucket was configured for public access, but only reachable to those with the exact web addresses of the bucket.


TextNow was notified of the issue on October 1st, 2017, and corrected the issue immediately.

TextNow has confirmed that no consumer information was accessed.

## Potential Impact For Users
 - There is no evidence of unauthorized access.
 - The vulnerability could have exposed user ids and information if not addressed.

## Steps Taken to Ensure Customer Security
 - The configuration issue was immediately corrected, preventing further access to the bucket.
 - The way consumer information was stored was changed, removing the use of user ids in the hash of the files.
 - Over the following months, GlitchWitch.io worked closely with TextNow to perform a full [Black Box Security Assessment](https://glitchwitch.io/) of TextNow’s external facing systems.

## Statement From TextNow
TextNow appreciates GlitchWitch.io’s research and professionalism in reaching out to our team about their findings. Nothing is more important to us than safeguarding customer information.

This type of research helps improve the safety of the broader mobile ecosystem, which is something that we care deeply about at TextNow.

When the vulnerability was flagged, we acted immediately to conduct a thorough review of our systems to ensure the highest levels of security and privacy protections are in place.

TextNow has strict policies and procedures in place to protect user information from being disclosed to unauthorized parties. We safeguard user information in accordance with industry standard procedures and security standards, and maintain physical, electronic and procedural safeguards to protect user’s personal information.


We encourage researchers who find issues to report them to our security team at [security@textnow.com](mailto:security@textnow.com).
