---
author:
  name: Linode Community
  email: docs@linode.com
description: 'Deploy a WireGuard Server on Linode with One-Click Apps.'
keywords: ['vpn','wireguard','tunnel']
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
published: 2019-03-28
modified_by:
  name: Linode
title: "Deploy WireGuard with One-Click Apps"
contributor:
  name: Linode
external_resources:
- '[WireGuard Quick Start](https://www.wireguard.com/quickstart/)'
- '[WireGuard Conceptual Overview](https://www.wireguard.com/#conceptual-overview)'
- '[WireGuard man page](https://manpages.debian.org/unstable/wireguard-tools/wg.8.en.html)'
---
![Deploy WireGuard with One-Click Apps](deploy-wireguard-with-oneclick-apps.png "Deploy WireGuard with One-Click Apps")

## WireGuard One-Click App

WireGuard is a simple, fast, and modern virtual private network (VPN) which utilizes state-of-the-art cryptography. It aims to be faster and leaner than other VPN protocols such as OpenVPN and IPSec, and it has a much smaller source code footprint.

Configuring WireGuard is as simple as configuring SSH. A connection is established by an exchange of public keys between server and client, and only a client whose public key is present in the server's configuration file is considered authorized. WireGuard sets up standard network interfaces which behave similarly to other common network interfaces, like `eth0`. This makes it possible to configure and manage WireGuard interfaces using standard networking tools such as ifconfig and ip.

The WireGuard One-Click App will create a Linode instance and set up a WireGuard network device named `wg0` on it. This device will have a simple configuration which can send and receive traffic to/from a single WireGuard peer (which will also be referred to as your WireGuard *client*).

{{< note >}}
The peer configurations provided by the One-Click App and this guide will allow you to directly connect your WireGuard server and client. This configuration will *not* enable forwarding of all of your client's traffic through the WireGuard server to the public Internet (though that arrangement is possible with WireGuard).

Your WireGuard configuration can be adjusted after you first set up your One-Click App; review the [WireGuard man page](https://manpages.debian.org/unstable/wireguard-tools/wg.8.en.html) for more information about the options that are available.
{{< /note >}}

## Deploy WireGuard with One-Click Apps

{{< content deploy-one-click-apps >}}

### WireGuard Options

The WireGuard One-Click form includes fields for your WireGuard client's [*public key*](https://www.wireguard.com/quickstart/#key-generation), and for your client's *endpoint IP* (which is your client's public IP address). If you have already set up your client and installed the WireGuard software on it prior to setting up your WireGuard One-Click App, then you may already have this information. If you do have the key and the endpoint IP for the client, you can enter them into the app's creation form, and your server's configuration will be pre-populated with those values.

{{< note >}}
Please note that a public key generated by WireGuard will not resemble [a public key that you might use for SSH connections](/docs/security/authentication/use-public-key-authentication-with-ssh/); when in doubt, leave the form's **Public Key (Client)** field blank.
{{< /note >}}

If you do not have the values for these fields, you can leave them empty. After the app is deployed, visit the [Configure and Connect your WireGuard Client and Server](#configure-and-connect-your-wireguard-client-and-server) section to set up your client and generate a key. The instructions in that section will also show you how to insert the key and the endpoint IP for your client into your server's configuration.

| **Field**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | **Description** |
|-----------|-----------------|
| **Port** | Set your WireGuard server's listening port number. The default is: `51820`. *Advanced Configuration*. |
| **Tunnel IP** | Your WireGuard server's tunnel IP address and subnet in CIDR notation. The default is: `10.0.1.1/24`. This is not the same as a private IP address that Linode can assign to your Linode instance; instead, this address is managed by the network that WireGuard creates. *Advanced Configuration*. |
| **WireGuard Public Key (Client)** | Your WireGuard client's public key. *Advanced Configuration*. |
| **Tunnel IP (Client)** | Your WireGuard client's tunnel IP address and subnet in CIDR notation. The default is: `10.0.1.2/24`. This is not the same as a private IP address that Linode can assign to your Linode instance; instead, this address is managed by the network that WireGuard creates. *Advanced Configuration*. |
| **Endpoint IP (Client)** | The Internet address of your WireGuard client. If your WireGuard client is another Linode, then the Endpoint IP is the public IP of that Linode, which is visible in the Linode's dashboard in the Linode Cloud Manager. *Advanced Configuration*. |

### Linode Options

After providing the app-specific options, provide configurations for your Linode server:

| **Configuration** | **Description** |
|-------------------|-----------------|
| **Select an Image** | Debian 9 is currently the only image supported by the WireGuard One-Click App, and it is pre-selected on the Linode creation page. *Required* |
| **Region** | The region where you would like your Linode to reside. In general, it's best to choose a location that's closest to you. For more information on choosing a DC, review the [How to Choose a Data Center](/docs/platform/how-to-choose-a-data-center) guide. You can also generate [MTR reports](/docs/networking/diagnostics/diagnosing-network-issues-with-mtr/) for a deeper look at the network routes between you and each of our data centers. *Required*. |
| **Linode Plan** | Your Linode's [hardware resources](/docs/platform/how-to-choose-a-linode-plan/#hardware-resource-definitions). You can use any size Linode for your WireGuard App. The Linode plan that you select should be appropriate for the amount of data transfer, users, and other stress that may affect the performance of your VPN. You can create your VPN on a Nanode 1GB or a Linode 2GB with low risk for performance hits, unless you expect intensive data transfer to happen on your VPN. *Required* |
| **Linode Label** | The name for your Linode, which must be unique between all of the Linodes on your account. This name will be how you identify your server in the Cloud Manager’s Dashboard. *Required*. |
| **Root Password** | The primary administrative password for your Linode instance. This password must be provided when you log in to your Linode via SSH. It must be at least 6 characters long and contain characters from two of the following categories: lowercase and uppercase case letters, numbers, and punctuation characters. Your root password can be used to perform any action on your server, so make it long, complex, and unique. *Required* |

When you've provided all required Linode Options, click on the **Create** button. **Your WireGuard app will complete installation anywhere between 2-5 minutes after your Linode has finished provisioning**.

## Getting Started after Deployment

### Configure and Connect your WireGuard Client and Server

After your One-Click App has provisioned your WireGuard server, you can proceed with setting up your WireGuard client and establishing a connection to the server.

If you did not provide a public key for WireGuard when you first set up your One-Click App, you will need to follow the next set of steps. These instructions will set up your client and inform your server of your client's public key. If you did provide a public key when deploying the One-Click App and have set up your client, skip to the second collection of steps in this section.

1.  Follow the [WireGuard Client](/docs/networking/vpn/set-up-wireguard-vpn-on-ubuntu/#wireguard-client) section of our WireGuard guide to generate a public/private keypair for your client, and to set up the WireGuard network interface configuration on your client.

1. [Connect to your One-Click App's Linode via SSH](/docs/getting-started/#connect-to-your-linode-via-ssh).

1. Bring down the `wg0` interface on the server:

        wg-quick down wg0

1. Open the `/etc/wireguard/wg0.conf` file in a text editor ([nano](/docs/quick-answers/linux/use-nano-to-edit-files-in-linux/), for example).

1. You will see a line that reads `PublicKey = ` under the `[Peer]` section. Append your client's public key to this line.

1. You will also see a line that reads `Endpoint = `. Append your client's Internet address to this line and then save the file. If your WireGuard client is also a Linode, user your Linode's public IP. If your client is on your home computer, visit a site like [whatismyip.com](https://www.whatismyip.com) to get your address.

1. Bring the `wg0` interface back up on the server:

        wg-quick up wg0

    {{< note >}}
`wg-quick` is a convenient wrapper for many of the common functions in `wg`. To learn more about all the available commands for each utility, issue the `wg --help` and `wg-quick --help` commands from your Linode's command line.
{{</ note >}}

You should now have your server configuration completed. At this point, you still need to complete your client's configuration; specifically, you need to add your server as a peer to the client:

1.  [Connect to your One-Click App's Linode via SSH](/docs/getting-started/#connect-to-your-linode-via-ssh).

1.  Just like your client, your server also has a public/private keypair of its own. The One-Click App script leaves a copy of these keys in the root user's home folder:

        ls /root

    {{< output >}}
wg-private.key	wg-public.key
{{< /output >}}

1.  Use the `cat` command to get the value of the server's WireGuard public key:

        cat /root/wg-public.key

1.  You should see a random string similar to:

    {{< output >}}
FngGVypEJ13KU8+OeBGG1sOd2i+aazsj7qPL3ZxacG8=
{{< /output >}}

1.  Copy the output of your server's public key, then use it to complete **steps 1 and 2** of the [Connect the Client and Server](/docs/networking/vpn/set-up-wireguard-vpn-on-ubuntu/#connect-the-client-and-server) section of our WireGuard guide. These steps will tell you to append `[Peer]` section to your client's existing WireGuard configuration and then how to enable the service on your client.

    Enter your server's WireGuard tunnel IP (using the `/24` CIDR notation) as the value for the `AllowedIPs` setting, and set the server's public IP address and WireGuard port to be the Endpoint. Here's an example template for a completed client configuration:

    {{< file >}}
[Interface]
PrivateKey = <Your client WireGuard private key>
Address = 10.0.1.2

[Peer]
PublicKey = <Your server WireGuard public key>
AllowedIPs = 10.0.1.1
Endpoint = <Your WireGuard server public IP>:51820
{{< /file >}}

     After you complete steps 1 and 2 from that section, you will have established the server as the client's peer.

### Test your WireGuard Client's Connection

This test should be performed once you have configured a WireGuard client and updated your WireGuard server to include the client's peer information:

1. Access your WireGuard client and ping the WireGuard server. Replace `10.0.1.1` with the tunnel IP address you assigned to the WireGuard server in the One-Click App creation form:

        ping 10.0.1.1

1. Use the WireGuard utility to verify your client's latest handshake:

        wg show

    The last two lines of the output from running the wg command should be similar to:

    {{< output >}}
latest handshake: 1 minute, 17 seconds ago
transfer: 98.86 KiB received, 43.08 KiB sent
{{</ output >}}

### Software Included

| **Software** | **Description** |
|--------------|-----------------|
| [**WireGuard**](https://www.wireguard.com) | VPN software. |
| [**UFW (UncomplicatedFireWall)**](https://wiki.ubuntu.com/UncomplicatedFirewall) | Firewall utility. The Port assigned during the [WireGuard Options](#wireguard-options) step of the deployment will allow outgoing and incoming traffic. |