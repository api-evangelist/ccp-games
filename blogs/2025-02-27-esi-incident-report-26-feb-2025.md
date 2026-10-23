---
title: "ESI Incident Report - 26 Feb 2025"
url: "https://developers.eveonline.com/blog/esi-incident-report-26-feb-2025"
date: "2025-02-27"
author: "The ESI Development Team"
feed_url: "https://developers.eveonline.com/feed.xml"
---
Yesterday just after downtime about 50% of the ESI endpoints did not return to their normal state. This incident was caused by a misconfiguration in the RabbitMQ broker that sits between ESI and the Monolith, and prevented the Monolith from receiving (and responding to) requests from ESI. This caused ESI requests to stall and error out after their configured timeout of 10 seconds.
