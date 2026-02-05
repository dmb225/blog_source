Title: System Design: Lesson 3.2 - Global and local load balancing
Date: 2026-02-04
Category: Blog
Tags: system-design, load-balancers

## Introduction
From the previous lesson, it may seem like load balancing is performed only within the data center. However, load balancing is required at a global and a local scale. Let’s understand the function of each of the two:

- Global server load balancing (GSLB): GSLB involves the distribution of traffic load across multiple geographical regions.
- Local load balancing: This refers to load balancing achieved within a data center. This type of load balancing focuses on improving efficiency and better resource utilization of the hosting servers in a data center.
Let’s understand each of the two techniques below.

## Global server load balancing
GSLB ensures that globally arriving traffic load is intelligently forwarded to a data center. For example, power or network failure in a data center requires that all the traffic be rerouted to another data center. GSLB takes forwarding decisions based on the users’ geographic locations, the number of hosting servers in different locations, the health of data centers, and so on.

In the next lesson, we’ll also learn how GSLB offers automatic zonal failover. GSLB service can be installed on-premises or obtained through Load Balancing as a Service (LBaaS).

The GSLB can forward requests to three different data centers. Each local load balancing layer within a data center will maintain a control plane connection with the GSLB providing information about the health of the LBs and the server farm. GSLB uses this information to drive traffic decisions and forward traffic load based on each region’s configuration and monitoring information.

Now, we’ll discuss how the domain name system (DNS) can perform GSLB.

## Load balancing in DNS
We understand that DNS can respond with multiple IP addresses for a DNS query. In the lesson on DNS, we discussed that it’s possible to do load balancing through DNS while looking at the output of nslookup. DNS uses a simple technique of reordering the list of IP addresses in response to each DNS query. Therefore, different users get a reordered IP address list. It results in users visiting a different server to entertain their requests. In this way, DNS distributes the load of requests on different data centers. This is performing GSLB. In particular, DNS uses round-robin to perform load balancing.

However, round-robin has the following limitations:

Different ISPs have a different number of users. An ISP serving many customers will provide the same cached IP to its clients, resulting in uneven load distribution on end-servers.
Because the round-robin load-balancing algorithm doesn’t consider any end-server crashes, it keeps on distributing the IP address of the crashed servers until the TTL of the cached entries expires. Availability of the service, in that case, can take a hit due to DNS-level load balancing.

Despite its limitations, round-robin is still widely used by many DNS service providers. Furthermore, DNS uses short TTL for cached entries to do effective load balancing among different data centers.

*Note: DNS isn’t the only form of GSLB. Application delivery controllers (ADCs) and cloud-based load balancing (discussed later) are better ways to do GSLB.*

**Application delivery controllers (ADCs) are part of the application delivery network (ADN). They can be considered the superset of LBs offering various services, including load balancing. The primary task of ADCs is to perform web acceleration to reduce the load from the server farm. Some well-known services between layers 3 and 7 include caching, SSL offloading, proxy/reverse proxy services, IP traffic optimization, and many more. ADCs also implement GSLB.**

*Anycast is a networking technique where the same IP address is assigned to multiple servers in different locations. Traffic is then routed to the nearest server based on the network's routing table, improving response times. With this in mind, explain the role of anycast in global load balancing and its advantages compared to traditional Global Server Load Balancing (GSLB) mechanisms.*

### Anycast in load balancing
Anycast plays a key role in global load balancing by assigning the same IP address to multiple servers located in different regions. When a user makes a request, the network’s routing infrastructure directs the traffic to the nearest or best-performing server based on network topology, which helps reduce latency and improve response times.

The advantages of anycast compared to traditional GSLB mechanisms include faster failover, as traffic is automatically rerouted if a server or location becomes unreachable, and simplified DNS management since there’s no need for complex round-robin or geo-aware DNS configurations.

It’s especially effective for latency-sensitive applications like video streaming, gaming, or real-time communication.

## The need for local load balancers
DNS plays a vital role in balancing the load, but it suffers from the following limitations:

- The small size of the DNS packet (512 Bytes) isn’t enough to include all possible IP addresses of the servers.
- There’s limited control over the client’s behavior. Clients may select arbitrarily from the received set of IP addresses. Some of the received IP addresses may belong to busy data centers.
- Clients can’t determine the closest address to establish a connection with. Although DNS geolocation and anycasting-like solutions can be implemented, they aren’t trivial solutions.
- In case of failures, recovery can be slow through DNS because of the caching mechanism, especially when TTL values are longer.

To solve some of the above problems, we need another layer of load balancing in the form of local LB. In the next lesson, we’ll discuss different details about local load balancers.

## What is local load balancing?
Local load balancers reside within a data center. They behave like a reverse proxy and make their best effort to divide incoming requests among the pool of available servers. Incoming clients’ requests seamlessly connect to the LB that uses a virtual IP address (VIP).

### Can DNS be considered a global server load balancer (GSLB)?
Yes, DNS can be used as part of global server load balancing (GSLB), but it is typically implemented through custom DNS infrastructure. There are two common approaches to global traffic management (GTM):

- GTM through ADCs: Some Application Delivery Controllers (ADCs) support GSLB by forwarding requests based on real-time data such as server health and data center capacity.
- GTM through DNS: Organizations or DNS providers configure authoritative DNS servers that respond to client requests based on the IP address of the DNS resolver (which is usually close to the user). This allows them to direct traffic to the data center that is geographically or topologically nearest, or based on routing policies such as latency or availability. This is typically achieved via third-party managed DNS providers (e.g., AWS Route 53, Cloudflare) or custom DNS infrastructure that incorporates geolocation and health data.
