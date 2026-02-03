Title: System Design: Lesson 3 - How the Domain Name System works
Date: 2026-02-03
Category: Blog
Tags: system-design

Through this lesson, we’ll answer the following questions:

- How is the DNS hierarchy formed using various types of DNS name servers?
- How is caching performed at different levels of the Internet to reduce the querying burden over the DNS infrastructure?
- How does the distributed nature of the DNS infrastructure help its robustness?

Let’s get started.

## DNS hierarchy

As stated before, the DNS isn’t a single server that accepts requests and responds to user queries. It’s a complete infrastructure with name servers at different hierarchies.

There are mainly four types of servers in the DNS hierarchy:

- DNS resolver: Resolvers initiate the querying sequence and forward requests to the other DNS name servers. Typically, DNS resolvers lie within the premise of the user’s network. However, DNS resolvers can also cater to users’ DNS queries through caching techniques, as we will see shortly. These servers can also be called local or default servers.
- Root-level name servers: These servers receive requests from local servers. Root name servers maintain name servers based on top-level domain names, such as .com, .edu, .us, and so on. For instance, when a user requests the IP address of google.com, root-level name servers will return a list of top-level domain (TLD) servers that hold the IP addresses of the .io domain.
- Top-level domain (TLD) name servers: These servers hold the IP addresses of authoritative name servers. The querying party will get a list of IP addresses that belong to the authoritative servers of the organization.
- Authoritative name servers: These are the organization’s DNS name servers that provide the IP addresses of the web or application servers.

**DNS names are processed from right to left, unlike UNIX file paths that go from left to right. For example, when resolving google.com, the resolver first looks up .io, then educative. Visually, the DNS hierarchy forms a tree structure, starting from the root domain at the top and branching out to subdomains.**

## Iterative versus recursive query resolution
There are two ways to perform a DNS query:

- Iterative: The local server requests the root, TLD, and the authoritative servers for the IP address.
- Recursive: The end user requests the local server. The local server further requests the root DNS name servers. The root name servers forward the requests to other name servers.

*Note: Typically, an iterative query is preferred to reduce query load on DNS infrastructure.*

Fun fact: These days, we’ll find many third-party public DNS resolvers offered by Google, Cloudflare, OpenDNS, and many more. The interesting fact is that these public DNS servers may provide quicker responses than the local ISP DNS facilities.

## Caching
Caching refers to the temporary storage of frequently requested resource records. A record is a data unit within the DNS database that shows a name-to-value binding. Caching reduces response time to the user and decreases network traffic. When we use caching at different hierarchies, it can reduce a lot of querying burden on the DNS infrastructure. Caching can be implemented in the browser, operating systems, local name server within the user’s network, or the ISP’s DNS resolvers.

*Note: Even if there is no cache available to resolve a user’s query and it’s imperative to visit the DNS infrastructure, caching can still be beneficial. The local server or ISP DNS resolver can cache the IP addresses of TLD servers or authoritative servers and avoid requesting the root-level server.*

#### Why does DNS sacrifice strong consistency to achieve high performance and scalability?

**Solution: Certainly! DNS sacrifices strong consistency to achieve high performance and scalability because it handles a vast number of read operations compared to write operations. By propagating updates lazily, DNS ensures quick responses to queries without overwhelming its infrastructure. Keep exploring!**

## DNS as a distributed system
Although the DNS hierarchy facilitates the distributed Internet that we know today, it’s a distributed system itself. The distributed nature of DNS has the following advantages:

It avoids becoming a single point of failure (SPOF).
It achieves low query latency so users can get responses from nearby servers.
It gets a higher degree of flexibility during maintenance and updates or upgrades. For example, if one DNS server is down or overburdened, another DNS server can respond to user queries.
There are 13 logical root name servers (named letter A through M) with many instances spread throughout the globe. These servers are managed by 12 different organizations.

Let’s now go over how DNS is scalable, reliable, and consistent.

### Highly scalable
Due to its hierarchical nature, DNS is a highly scalable system. Roughly 1,000 replicated instances of 13 root-level servers are spread throughout the world strategically to handle user queries. The working labor is divided among TLD and root servers to handle a query and, finally, the authoritative servers that are managed by the organizations themselves to make the entire system work. As shown in the DNS hierarchy tree above, different services handle different portions of the tree enabling scalability and manageability of the system.

### Reliable
Three main reasons make the DNS a reliable system:

- Caching: The caching is done in the browser, the operating system, and the local name server, and the ISP DNS resolvers also maintain a rich cache of frequently visited services. Even if some DNS servers are temporarily down, cached records can be served to make DNS a reliable system.
- Server replication: DNS has replicated copies of each logical server spread systematically across the globe to entertain user requests at low latency. The redundant servers improve the reliability of the overall system.
- Protocol: Although many clients rely on the unreliable User Datagram Protocol (UDP) to request and receive DNS responses, it’s important to acknowledge that UDP also offers distinct advantages. It is much faster, and therefore, improves DNS performance. Furthermore, internet service reliability has improved since its inception, so UDP is usually favored over TCP. DNS queries are usually retransmitted at the transport layer if there’s no response for the previous one. Therefore, request-response might need additional round trips, which provides a shorter delay as compared to TCP, which needs a three-way handshake every time before data exchange.

*Note: DNS usually relies on UDP for quick, lightweight queries. However, when networks are congested or responses exceed 512 bytes, DNS switches to TCP for reliable delivery. TCP is also used for zone transfers, where consistent and complete data exchange between DNS servers is required. Modern clients may use DNS over HTTPS (DoH) or DNS over TLS (DoT) for added security and privacy.*

### Consistent
DNS uses various protocols to update and transfer information among replicated servers in a hierarchy. DNS compromises on strong consistency to achieve high performance because data is read frequently from DNS databases as compared to writing. However, DNS provides eventual consistency and updates records on replicated servers lazily. Typically, it can take from a few seconds up to three days to update records on the DNS servers across the Internet. The time it takes to propagate information among different DNS clusters depends on the DNS infrastructure, the size of the update, and which part of the DNS tree is being updated.

Consistency can suffer because of caching too. Since authoritative servers are located within the organization, it may be possible that certain resource records are updated on the authoritative servers in case of server failures at the organization. Therefore, cached records at the default/local and ISP servers may be outdated. To mitigate this issue, each cached record comes with an expiration time called time-to-live (TTL).

*To maintain high availability, the TTL value should be small. This is because if any server or cluster fails, the organization can update the resource records right away. Users will experience non-availability only for the time the TTL has not expired. However, if the TTL is large, the organization will update its resource records, whereas users will keep pinging the outdated server that would have crashed long ago. Companies that long for high availability maintain a TTL value as low as 120 seconds. Therefore, even in the case of a failure, the maximum downtime is a few minutes.*

### Aout some commands

#### nslookup
The Non-authoritative answer, as the name suggests, is the answer provided by a server that is not the authoritative server of Google. It isn’t in the list of authoritative nameservers that Google maintains. So, where does the answer come from? The answer is provided by second, third, and fourth-hand name servers configured to reply to our DNS query—for example, our university or office DNS resolver, our ISP nameserver, our ISP’s ISP nameserver, and so on. In short, it can be considered as a cached version of Google’s authoritative nameservers response. If we try multiple domain names, we’ll realize that we receive a cached response most of the time.
If we run the same command multiple times, we’ll receive the same IP addresses list but in a different order each time. The reason for that is DNS is indirectly performing load balancing. It’s an important term that we’ll gain familiarity with in the coming lessons.

#### dig
The Query time: 4 msec represents the time it takes to get a response from the DNS server. For various reasons, these numbers may be different in our case.
The 300 value in the ANSWER SECTION represents the number of seconds the cache is maintained in the DNS resolver. This means that Google’s ADNS keeps a TTL value of five minutes.


**Question**
DNS caching improves performance but introduces the risk of stale data. Suppose an organization updates its website’s IP address, but many users still access the old IP due to caching. Propose a strategy to minimize disruption in such scenarios.

**Solution: Certainly! To minimize disruption when updating DNS records, organizations can temporarily lower the TTL (Time To Live) value before making changes, ensuring clients refresh their cached data more frequently. Additionally, implementing a phased rollout across authoritative DNS servers helps control the update process smoothly. Using DNS-based traffic management can redirect users still accessing the old IP to a temporary server with a message or alternative service. Lastly, notifying users in advance about upcoming changes helps set expectations and reduces confusion.**
