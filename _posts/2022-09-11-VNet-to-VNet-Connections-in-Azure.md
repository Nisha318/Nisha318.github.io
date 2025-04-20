---
title: "Creating VNet-to-VNet Connections in Microsoft Azure"
date: 2022-09-11
layout: single
classes: wide
author_profile: true
read_time: true
toc: true
toc_label: "Table of Contents"
toc_sticky: true
categories:
  - Blog
  - Cloud
tags:
  - Azure
  - VNet
  - VPN Gateway
  - Networking
excerpt: "This walkthrough details how to create secure VPN Gateway connections between two Azure Virtual Networks using VNet-to-VNet tunneling. Learn how to configure VPN Gateways, establish encrypted connections, and verify the status—all from the Azure portal."
header:
  image: /assets/images/azure/networking/vnet-00.png
  teaser: /assets/images/azure/vnet-to-vnet/vnet-00.png
  overlay_filter: "rgba(0, 0, 0, 0.5)"
---

## Introduction

Well, I am just having loads of fun! 😄 The Azure VPN Gateway has now entered the scene and with it we are presented with so many additional options for establishing connectivity between our Azure virtual networks and our on-premises networks.

While **VNet Peering** can provide us with one solution for bridging our VNets to allow communication across Azure-to-Azure virtual networks, there are some differences in the capabilities of VNet Peering versus VNet-to-VNet connections that should be considered when making decisions about Azure V-Nets and hybrid networking solution needs.

By deploying a VPN Gateway into a dedicated `GatewaySubnet` of our virtual networks, we can create one or more encrypted tunnel connections between the VPN Gateway of our Azure VNet and any of the following:

- Other virtual networks (VNet-to-VNet connections)
- Our on-premises VPN device (Site-to-Site connections)
- The client devices of our remote workers (Point-to-Site connections)

The VPN Gateway can be combined with other Azure networking services to provide more advanced solutions, such as functioning as a **Gateway Transit** to support routing in a **Hub and Spoke** network topology. It supports multiple protocols and offers various bandwidth speeds and high availability, subject to the selected Gateway SKU.

Over the weekend, I had an opportunity to lab up each of these different VPN Gateway connection types (with limitations) using the Azure Portal. In this article, I discuss how I was able to bridge the connection between my two Azure Virtual Networks by creating a **VNet-to-VNet connection** using VPN Gateways (tutorial source: Microsoft.com). The steps were:

- Creating and configuring VNet1
- Creating the VNet1 gateway
- Creating and configuring VNet4
- Creating the VNet4 gateway
- Configuring the VNet1 gateway connection
- Configuring the VNet4 gateway connection
- Verifying the connections

---

## Step 1: Create and Configure VNet1 and VPN Gateway 1

In the first step, I created the first virtual network, **VNet1**, in the **TestRG1** Resource Group, located in the **East US** Azure Region.

<img src="/assets/images/azure/networking/vnet-02.png">

### Configuration of VNet1

Next, I configured the IP addressing for the network, assigning it a network address space and a **FrontEnd** subnet from this space.

<img src="/assets/images/azure/networking/vnet-03.png">

With VNet1 in place, I deployed a **VPN Gateway** to a dedicated `GatewaySubnet`. Microsoft recommends using a CIDR `/27` block for future scalability. This gateway is of type `VPN` and **Route-Based**. I did not enable any HA or BGP options.

The gateway SKU selected was one of the more affordable options, offering lower bandwidth throughput compared to higher-tier SKUs.

<img src="/assets/images/azure/networking/vnet-04.png">

---

## Step 2: Create and Configure VNet4 and VPN Gateway 4

After completing VNet1, I configured **VNet4**, located in the **West US** Azure Region.


<img src="/assets/images/azure/networking/vnet-05.png">

### Configuration of VNet4

I configured the address space for VNet4 and added a **FrontEnd** subnet range from this space.

Then, I deployed a **Route-Based VPN Gateway** to the `GatewaySubnet` within VNet4.

At this point, both VNet1 and VNet4 had fully configured VPN Gateways.

<img src="/assets/images/azure/networking/vnet-06.png">

<img src="/assets/images/azure/networking/vnet-07.png">

---

## Step 3: Configure the Gateway Connections

This phase involved setting up the VNet-to-VNet connections from each VPN Gateway.

From **VNet1GW**, I created a connection to **VNet4GW** (the remote gateway). I entered a **Shared Key**—this key must match on both ends. The tunneling protocol selected was **IKEv2**, known for speed and reliability.

Then, I configured the **VNet4GW** connection to point back to **VNet1GW**, using the same Shared Key and IKEv2 protocol.

---

## Step 4: Verify Connectivity

I verified that the connection status showed **"Connected"** on both sides:

- From the perspective of **VNet4**
- And from the perspective of **VNet1**

---

## Final Thoughts

I enjoyed configuring the VPN Gateways and the VNet-to-VNet Connection for both virtual networks in this lab. It was fairly simple and informative. I also appreciated learning more about the capabilities that VPN Gateways enable for communication between Azure VNets and on-prem networks in hybrid cloud environments.

Thank you for reading!

---

## References

- [VPN Gateway documentation](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways)
- [Configure a VNet-to-VNet VPN gateway connection: Azure portal](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)

> If this post was helpful, please consider sharing it! 🚀
