---
toc: true
layout: post
description: Networking for Devs
categories: ["Cornell Explainers", "Networking for Devs"]
title: DNS for Developers
image: https://i.imgur.com/umsJdKF.png
hide: false
---

<style>
table {font-size:inherit; white-space:inherit}
table td, table th {width:100%}
</style>

# Networking for Devs - DNS FAQ

![](https://i.imgur.com/umsJdKF.png "The Domain Name System")

As a senior developer, it's clear some other technical staff lack a working knowledge of the Domain Name System.

Here's an explainer, using the [Cornell method](https://en.wikipedia.org/wiki/Cornell_Notes) - my favourite way to work through a subject quickly.

## Cornell Method notes

<table>
<tr>
<th> Q </th>
<th> A </th>
</tr>
<tr>
<td>
What is DNS and a DNS query?
</td>
<td>

Domain Name System is used to map human-friendly service names ('hosts') to the resource's numerical addresses (IP addresses)

A DNS query and how it is resolved is the process of translating the human-friendly computer host names into IP addresses. DNS 'resolution' and 'translation' are the same thing.

</td>
</tr>
<tr>
  <td>How does a DNS query get resolved?</td>
  <td>
  When a user enters https://blog.howapped.com, the browser initiates a DNS resolver which sequences the query that ultimately leads to a translation.
    
  The recursive resolver function looks in cache

1. If not in cache, it goes to the first of the authoritative nameservers. This is hardcoded in the OS. For example `8.8.8.8`
2. If translation not found, continue querying
3. Resolver -- queries --> internal cache
4. Resolver -- queries --> root nameserver
5. Resolver -- queries --> `.com` nameserver

  </td>
</tr>

<tr>
  <td>What is the root nameserver?</td>
  <td>
  When a client begins to resolve a DNS query, the remote resolution process starts with a request to a root nameserver. It will respond with a referral to further authoritative servers.

A root nameserver is a type of authoritative nameserver.

Another type of authoritative nameserver is a **TLD nameserver**.

For example, the request to translate `www.wikipedia.org` will receive a response containing addresses of `.org` authoritative TLD server servers.
Ao

  </td>
</tr>

<tr>
  <td>How can performance be increased?</td>
  <td>
  The are DNS caching servers which store resolved mappings for a specified period. This reduces DNS traffic across the internet.
  How long cache entries are stored is according to the TTL value for the DNS record in question.
  </td>
</tr>

<tr>
  <td>What are the various DNS records for?</td>
  <td>
  Besives translating names to IP addresses, DNS can instruct mail agents which is the best mail server to deliver email.

An MX record provides a mapping between a domain a mail exchanges.

  </td>
</tr>
<!-- -->

<tr>
  <td>Question?</td>
  <td>
  Answer
  </td>
</tr>
</table>
