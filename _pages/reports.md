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

Unless otherwise stated or agreed upon in written communication, a 45-day disclosure deadline will apply to all bugs and vulnerabilities found by glitchwitch.io research. All findings will be disclosed to the public once either a patch has been made broadly available or after 45 days from the initial report, regardless of the existence or availability of patches or workarounds. Extenuating circumstances, such as active exploitation, threats of an especially serious (or trivial) nature, or situations that require changes to an established standard may result in earlier or later disclosure. This disclosure policy is based on the [CERT Coordination Center (CERT/CC) Vulnerability Disclosure Policy.](https://vuls.cert.org/confluence/display/Wiki/Vulnerability+Disclosure+Policy)

## Report List

The following list includes some of the findings by GlitchWitch.io. Reports are assigned a unique "Glitch Witch Advisory" number for reference. All dates are recorded in UTC.

| --- | --- | :--- | --- |
| ID | Type | Affected Party | CVSS3 | Status |{% assign published = site.data.reports | where: "published","true" %}{% for item in published %}
| <a href="{% if item.link %}{{ item.link }}{% else %}{{ site.url }}/reports/{{ item.id | remove: 'GWA-2018-00' }}{% endif %}">{{ item.id }}</a> | <span title="{{item.type-long}}">{{ item.type }}</span> | <a href="{% if item.link %}{{ item.link }}{% else %}{{ site.url }}/reports/{{ item.id | remove: 'GWA-2018-00' }}{% endif %}/">{{ item.vendor }}</a> | {% if item.cvss %}<span title="{{item.cvss-string}}">{{ item.cvss }}</span>{% else %}N/A{% endif %} | {{ item.status }} |{% endfor %}

## Undisclosed Reports

Below is a list of reports either pending disclosure or permanently undisclosed. Some reports may be permanently undisclosed due to non-disclosure agreements resulting from bug bounty payouts or other circumstances.

| --- | --- | :--- | --- |
| ID | Type | Affected Party | CVSS3 | Status |{% assign unpublished = site.data.reports | where: "published","false" %}{% for item in unpublished %}
| {% if item.link %}<a href="{{ item.link }}">{{ item.id }}</a>{% else %}{{ item.id }}{% endif %} | <span title="{{item.type-long}}">{{ item.type }}</span> | {% if item.link %}<a href="{{ item.link }}/">{{ item.vendor }}</a>{% else %}{{ item.vendor }}{% endif %} | {% if item.cvss %}<span title="{{item.cvss-string}}">{{ item.cvss }}</span>{% else %}N/A{% endif %} | {{ item.status }} |{% endfor %}
