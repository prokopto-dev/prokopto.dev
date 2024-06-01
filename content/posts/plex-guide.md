+++ 
draft = true
date = 2024-06-01T11:55:26-04:00
title = "Plex, Docker, And A Robust Setup"
description = ""
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++

## Disclaimer! No Piracy, SVP

I, nor this article, do not suggest, nor intend to condone piracy in any form. Copyright holders work hard to provide the world with content worth consuming. They deserve compensation for their work, and that includes for digital representations of their work.

Don't pirate content.

What you do with these tools is your business, not mine, but I don't support your collecting pirated content.

You can use tools to back up existing digital and analogue media into files that can be served by these systems, and that is probably the best use-case for this.

The fact that you could in theory pirate content, and that many do, is merely a coincidence.

It is also straight up *VERY* illegal to charge for access to copyrighted content, regardless of whether it is pirated or not. This guide won't teach you how to legally set up a pay-for-streaming service, because I'm not going to teach you how to contract with companies to get licenses to distribute content.

This is just for private use, for you and your family, within the bounds of your own circle; not for commerical sale.

# Media Servers

So, I'm not an IT professional by trade.

Nor am I much of any one particular thing by trade.

But I am a bit of an obsessive when it comes to having projects I don't need to ever think about again, or that I can pick back up with little-to-no-effort.

So, in the spirit of that, I have been working hard on my home media server. In this post, which is just a how-to-guide, I'll go over a few topics, and provide as much detail on how to get started on your own (via my own set up process, or how I'd do it now that I know all the hiccups).

## Introduction

When people see, or I mention, my media server setup, some are impressed; others have done it themselves and are merely happy to see someone else in the community doing it.

It has allowed me to access many of my movies from anywhere, and ensure I can continue to watch them after the DVD-players and BlueRay players that once played them fail or are decomissioned.

It has provided a slick interface to let me use my Apple TV, iPhone, and other devices to track, monitor, and maintain such services/servers as needed to run these.

And it's provided a fun project to learn about networking, video transcoding, containerization, and system administration.

Hopefully, this article will guide those interested towards that process, and you'll become more appreciative of the effort that main-stream streaming services have to go to when providing content to millions of customers at the scale and quality that they do. It's not easy or trivial.

This guide should help make it at least a little easier to start your own small scale server.

## Terminology

I'm going to use a few technical terms in this article. They're just shorthand for things that you'll probably be familiar with if you found this article. There are better descriptions than what follows, but this will get you through if you've never done more than surf the web and stream movies.

- `host` : A "computer"; something that can run programs, and generally has an operating system on it; it should be able to be interacted with by other `hosts`, so isn't really about the hardware, though is euphemistically sometimes used that way.
- `device` : The physical hardware of some variety; may not necessarily be able to run programs, so isn't interchangable with `host`, but is a reference almost exclusively to the hardware.
  - `device (2)`: Unfortunately, because of sloppy naming conventions, there is a euphemistic usage of `device` that references the **representation** of a physical device in the operating system. I apologize on behalf of technologists everywhere.
- `operating system`: A series of little programs and services that lets people run more programs and services on `hosts`/hardware. There's a lot to learn about what makes an operating system an "operating system", but "Windows", "Mac OSX", and "Linux" are examples of types of operating systems.
- `server` : Sometimes used interchangably with `host`, but technically a `server` is a type of program that *"serves"* something, like content. In the most literal sense, it *serves* (or provides) a *service*.
- `NAS` : Short for "Network Attached Storage", it is a `server` that provides a few programs/services, focused around serving larger amounts of data to other `hosts`; it can sometimes do more than that, too. Importantly, like `server`, it is a euphemism for the hardware `host` as well, at times.
- `program`: A computer application; stuff that runs on an operating system.
- `service` : A type of program, generally not directly interacted with by most users, that helps provide content or a certain behavior.
  - `service (2)` : A product provided by some company, that generally involves responding to requests and providing personalized results, content, or behavior based on the types of requests.
- `docker` : A program/service, which does some fancy things to make it simple to have a healthy environment to run another program inside of it.
- `container` : The units that `docker` runs, that "*contain*" a program/series of programs; it is the (ideally) healthy environment that you run the programs in.
- `internet connection` : This is going to sound stupid, but I'm defining it anyways because of the next term: An "internet connection" means a connection to the wider "world wide web".
- `network connection` : This is less restrictive than the previous term, and means the ability, via a network interface (see next definition) to connect to other `hosts`.
- `network interface` : Sometimes interchangably used with "network card". It technically is strictly a reference to the *digital* representation of a piece of "network hardware" that a host communicates through.
- `network switch` : A multiplexing (that is, in this case, a way to provide multiple physical wire connections from a single source) device for network connections. For home users its often a little box that you plug one wire in that has internet, then plug in more wires to other computers/hosts to give them internet from that one place.
- `RAID` : "Redundant Array of Inexpensive Disks" - one two main methods for using disks together in concert to provide more storage options. Often involves complex methods for moving data between or storing data on multiple disks at once, to speed things up or make it less likely that if a disk fails, you will lose all your data.
- `JBOD` :  "Just a Bunch Of Disks" - the other method for providing more storage. This is exactly as its described.

## (Hardware) Necessary Equipment

You'll atleast one thing to get started with the whole setup. You can stop after the first couple sections and be fine, and not require more for the bare minimum. But for a fully automated process of maintaining a library, you'll need to set up a little more.

This is what I'd recommend in general terms; there will be specific suggestions from the time this article was written though in the sections that follow:

- A Host (Computer/Server) that can run `docker` and `plex-media-server` (preferrably one you can keep on almost all the time)
  - It should have an internet connection, or at least, internal network connection
  - See [Plex Media Server Downloads Page](https://www.plex.tv/media-server-downloads/) for a list, but it includes
    - Windows hosts
    - Linux hosts
    - Mac OSX hosts
    - NAS hosts

## (Hardware) Optional Equipment

If you want something that's pretty easy to maintain and expandable, there's a couple things more that you'll want:

- A separate `NAS` device from the hosting device; especially one that provides a RAID method (JBOD if you're feeling risky)
- A high-speed network switch and ethernet cables to connect the `NAS` to the host that is running `Plex`

This way, you can keep growing the storage capacity.

## Preamble Before We Get Started

There are, at this point, if you have everything above, no more things you ***NEED*** to purchase. You've got what you want, and it'll work just fine. There are places in this guide that paying for this or that product or service will improve the quality, speed, or ease of the setup. You can do this for as little as $200 USD, or spend as much as you wish. You could even out-perform (hypothetically) actual streaming services in performance and quality, with the right setup. I don't such a setup, and I don't think one needs it.

So at any point if you see me suggest such a thing, know it is merely optional (hopefully I'll mention that, too).

## Getting Started