---
layout: content
title: "Vulnerability Reports"
tagline: "Disclosure Policy &amp; Vulnerability Report List"
permalink: /reports/
image:
  social: sections/reports/reports-social.jpg
  feature:
  hero:
---
## Keeping you secure
GlitchWitch is an independent information security researcher dedicated to helping secure the web. They are committed to addressing and reporting security issues they find through a coordinated and constructive approach. They believe the best way to achieve this is through working closely with software vendors and clients through a process of coordinated disclosure to ensure vulnerabilities are promptly addressed, and that the public end-user is informed whenever possible.

## What is Coordinated Disclosure?
Coordinated disclosure is a vulnerability disclosure model in which a vulnerability is disclosed after a period of time or once the vulnerability is patched. When a vulnerability is identified, GlitchWitch will generally attempt to notify and work with the appropriate parties whenever possible. Details may be shared with the public after a set deadline, or sooner if a patch is released.

## Vulnerability Disclosure Policy
Unless otherwise stated or agreed upon in written communication a 15-day disclosure deadline will apply to all bugs, security issues, and vulnerabilities reported by GlitchWitch. All findings will be disclosed to the public once either a patch has been made broadly available or after 15 days from the initial report, regardless of the existence or availability of patches or workarounds. Extenuating circumstances, such as active exploitation, threats of an especially serious (or trivial) nature, or situations that require changes to an established standard may result in earlier or later disclosure.

>If for some reason you wish to extend the disclosure deadline of a finding reported to you, a deadline extension may be provided if requested, up to a maximum of 30 days from the initial discovery. Additionally vulnerability reports that are compensated through a bug bounty payout or non-disclosure contract may be exempt from public disclosure if mutually agreed upon.

## Compensation & Acknowledgement
Showing the world that you are a company that values and prioritizes the safety and security of your product builds trust and brand recognition. While compensation for a report outside of a designated bug bounty program is not strictly required, it is highly encouraged. Financially compensating researchers for their work creates positive relationships and helps ensure that continued research can be done to help keep the web safer and more secure. In addition to compensation, public acknowledgement can help further build trust not only within the security community, but with a vendors customers.

Looking to add value and further increase security? Consider [working together](/services) to perform a security assessment or targeted penetration test.

## Publicly Disclosed Reports

The following list includes previously disclosed findings. Reports are assigned a unique "Glitch Witch Advisory" number for reference. All dates are recorded in UTC.

| --- | --- | :--- | --- |
| ID | Type | Affected Party | CVSS3 | Status |{% assign published = site.data.reports | where: "published","true" %}{% for item in published %}
| <a href="{% if item.link %}{{ item.link }}{% else %}{{ site.url }}/reports/{{ item.id | remove: 'GWA-2018-00' }}{% endif %}">{{ item.id }}</a> | <span title="{{item.type-long}}">{{ item.type }}</span> | <a href="{% if item.link %}{{ item.link }}{% else %}{{ site.url }}/reports/{{ item.id | remove: 'GWA-2018-00' }}{% endif %}/">{{ item.vendor }}</a> | {% if item.cvss %}<span title="{{item.cvss-string}}">{{ item.cvss }}</span>{% else %}<span title="Not Available">NA</span>{% endif %} | {{ item.status }} |{% endfor %}

{% include sections/cta-reports-list.html %}

## Undisclosed Reports

Below is a list of reports either pending disclosure or permanently undisclosed. Some reports may be permanently undisclosed due to non-disclosure agreements resulting from bug bounty payouts or other circumstances.

| --- | --- | :--- | --- |
| ID | Type | Affected Party | CVSS3 | Status |{% assign unpublished = site.data.reports | where: "published","false" %}{% for item in unpublished %}
| {% if item.link %}<a href="{{ item.link }}">{{ item.id }}</a>{% else %}{{ item.id }}{% endif %} | <span title="{{item.type-long}}">{{ item.type }}</span> | {% if item.link %}<a href="{{ item.link }}/">{{ item.vendor }}</a>{% else %}{{ item.vendor }}{% endif %} | {% if item.cvss %}<span title="{{item.cvss-string}}">{{ item.cvss }}</span>{% else %}<span title="Not Available">NA</span>{% endif %} | {{ item.status }} |{% endfor %}
