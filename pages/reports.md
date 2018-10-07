---
layout: content
title: "Vulnerability Reports"
tagline: "Disclosure Policy &amp; Vulnerability Report List"
permalink: /reports/
image:
  feature: 
  hero: 
---
## What is Responsible Disclosure?
Responsible disclosure is a vulnerability disclosure model in which a vulnerability is disclosed after a period of time or once the vulnerability is patched. You can read more about this at [BugCrowd: What is Responsible Disclosure?](https://www.bugcrowd.com/resource/what-is-responsible-disclosure/)

## Disclosure Policy

Unless otherwise stated or agreed upon in written communication, a 45-day disclosure deadline will apply to all bugs and vulnerabilities found by glitchwitch.io research. All findings will be disclosed to the public once either a patch has been made broadly available or after 45 days from the initial report, regardless of the existence or availability of patches or workarounds. Extenuating circumstances, such as active exploitation, threats of an especially serious (or trivial) nature, or situations that require changes to an established standard may result in earlier or later disclosure.

## Report List

The following list includes some of the findings by GlitchWitch.io since late 2017. All report dates are recorded in UTC.

| --- | --- | :--- | --- |
| ID | Type | Vendor | CVSS3 | Status |{% for item in site.data.reports %}
| <a href="{% if item.link %}{{ item.link }}{% else %}{{ site.url }}/reports/{{ item.id | remove: 'GW000' | remove: 'GW00' | remove: 'GW0' }}{% endif %}">{{ item.id }}</a> | <span title="{{item.type-long}}">{{ item.type }}</span> | <a href="{% if item.link %}{{ item.link }}{% else %}{{ site.url }}/reports/{{ item.id | remove: 'GW000' | remove: 'GW00' | remove: 'GW0' }}{% endif %}">{{ item.vendor }}</a> | {% if item.cvss %}<span title="{{item.cvss-string}}">{{ item.cvss }}</span>{% else %}N/A{% endif %} | {{ item.status }} |{% endfor %}
