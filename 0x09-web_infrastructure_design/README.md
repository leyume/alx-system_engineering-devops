#  0x09. Web infrastructure design

##  Tasks 

##  0. Simple web stack

##  Description

This is a simple web infrastructure that hosts a website that is reachable via `www.foobar.com`. There are no firewalls or SSL certificates for protecting the server's network. Each component (database, application server) has to share the resources (CPU, RAM, and SSD) provided by the server.

<img src="https://raw.githubusercontent.com/leyume/alx-system_engineering-devops/main/0x09-web_infrastructure_design/images/ALX0.png" />

## Specifics About This Infrastructure

+ What a server is.<br/>The Server is a dedicated computer that provides services to clients like desktop computer, workstations or mobile devices. It’s a centralised machine where all these clients connect to, over the internet or a local area network. It contains different features to make it provide the required services like the Web server, Application Server and Database as seen above..

+ The role of the domain name.<br/>To provide a human-friendly alias for an IP Address. The Domain Name, foobar.com, provide an easy way to recognize and memorize than using the numerical address, 8.8.8.8, to access Internet resources.
The DNS Record for www in www.foobar.com is A or AAAA Record The IP address and domain name alias is mapped in the Domain Name System (DNS)

+ The role of the web server.<br/>The Web Server is a software in the Server that organises and serves web contents. This software receives the request from the client and serve the web content (as a response) after the getting relevant information from the App server and Database. Examples include Nginx and Apache.

+ The role of the application server.<br/>is a server design to run specific applications like PHP, Java, NodeJS. It receive request from the Web server and the process the request (and connects to a Database if required) and sends a response back to the Web server to be served to the client.

+ The role of the database.<br/>The Database is an organized collection of structured information, or data, typically stored electronically in the Server. It provides the App server with required info to be delivered to the client.

+ What the server uses to communicate with the client (computer of the user requesting the website).<br/>Communication between the client and the server occurs over the internet network through the TCP/IP protocol suite.

## Issues With This Infrastructure

+ There are multiple SPOF (Single Point Of Failure) in this infrastructure.<br/>For example, if the MySQL database server is down, the entire site would be down.

+ Downtime when maintenance needed.<br/>When we need to run some maintenance checks on any component, they have to be put down or the server has to be turned off. Since there's only one server, the website would be experiencing a downtime.

+ Cannot scale if there's too much incoming traffic.<br/>It would be hard to scale this infrastructure becauses one server contains the required components. The server can quickly run out of resources or slow down when it starts receiving a lot of requests.


<br />

---

<br />

# 1: Distributed Web Infrastructure
## Description

This is a distributed web infrastructure that atttempts to reduce the traffic to the primary server by distributing some of the load to a replica server with the aid of a server responsible for balancing the load between the two servers (primary and replica).

<img src="https://raw.githubusercontent.com/leyume/alx-system_engineering-devops/main/0x09-web_infrastructure_design/images/ALX1.png" />

## Specifics About This Infrastructure

+ The distribution algorithm the load balancer is configured with and how it works.<br/>The HAProxy load balancer is configured with the *Round Robin* distribution algorithm. This algorithm works by using each server behind the load balancer in turns, according to their weights. It’s also probably the smoothest and most fair algorithm as the servers’ processing time stays equally distributed. As a dynamic algorithm, *Round Robin* allows server weights to be adjusted on the go.
+ The setup enabled by the load-balancer.<br/>The HAProxy load-balancer is enabling an *Active-Passive* setup rather than an *Active-Active* setup. In an *Active-Active* setup, the load balancer distributes workloads across all nodes in order to prevent any single node from getting overloaded. Because there are more nodes available to serve, there will also be a marked improvement in throughput and response times. On the other hand, in an *Active-Passive* setup, not all nodes are going to be active (capable of receiving workloads at all times). In the case of two nodes, for example, if the first node is already active, the second node must be passive or on standby. The second or the next passive node can become an active node if the preceding node is inactive.
+ How a database *Primary-Replica* (*Master-Slave*) cluster works.<br/>A *Primary-Replica* setup configures one server to act as the *Primary* server and the other server to act as a *Replica* of the *Primary* server. However, the *Primary* server is capable of performing read/write requests whilst the *Replica* server is only capable of performing read requests. Data is synchronized between the *Primary* and *Replica* servers whenever the *Primary* server executes a write operation.
+ The difference between the *Primary* node and the *Replica* node in regard to the application.<br/>The *Primary* node is responsible for all the write operations the site needs whilst the *Replica* node is capable of processing read operations, which decreases the read traffic to the *Primary* node.

## Issues With This Infrastructure

+ There are multiple SPOF (Single Point Of Failure).<br/>For example, if the Primary MySQL database server is down, the entire site would be unable to make changes to the site (including adding or removing users). The server containing the load balancer and the application server connecting to the primary database server are also SPOFs.
+ Security issues.<br/>The data transmitted over the network isn't encrypted using an SSL certificate so hackers can spy on the network. There is no way of blocking unauthorized IPs since there's no firewall installed on any server.
+ No monitoring.<br/>We have no way of knowing the status of each server since they're not being monitored.

<br />

---

<br />

# 2: Secured and Monitored Web Infrastructure

## Description

This is a 3-server web infrastructure that is secured, monitored, and serves encrypted traffic.

<img src="https://raw.githubusercontent.com/leyume/alx-system_engineering-devops/main/0x09-web_infrastructure_design/images/ALX2.png" />

## Specifics About This Infrastructure

+ The purpose of the firewalls.<br/>The firewalls are for protecting the network (web servers, anyway) from unwanted and unauthorized users by acting as an intermediary between the internal network and the external network and blocking the incoming traffic matching the aforementioned criteria. 
+ The purpose of the SSL certificate.<br/>The SSL certificate is for encrypting the traffic between the web servers and the external network to prevent man-in-the-middle attacks (MITM) and network sniffers from sniffing the traffic which could expose valuable information. The SSL certs ensure privacy, integrity, and identification.
+ The purpose of the monitoring clients.<br/>The monitoring clients are for monitoring the servers and the external network. They analyse the performance and operations of the servers, measure the overall health, and alert the administrators if the servers are not performing as expected. The monitoring tool observes the servers and provides key metrics about the servers' operations to the administrators. It automatically tests the accessibility of the servers, measures response time, and alerts for errors such as corrupt/missing files, security vulnerabilities/violations, and many other issues. 

## Issues With This Infrastructure

+ Terminating SSL at the load balancer level would leave the traffic between the load balancer and the web servers unencrypted.
+ Having one MySQL server is an issue because it is not scalable and can act as a single point of failure for the web infrastructure.
+ Having servers with all the same components would make the components contend for resources on the server like CPU, Memory, I/O, etc., which can lead to poor performance and also make it difficult to locate the source of the problem. A setup such as this is not easily scalable. 

<br />

---
<br />

# 3: Scaled Up Web Infrastructure
## Description

This web infrastructure is a scaled up version of the infrastructure described Secured and Monitored Web Infrastructure. In this version, all SPOFs have been removed and each of the major components (web server, application server, and database servers) have been moved to separate servers. The SSL protection isn't terminated at the load-balancer and each server's network is protected with a firewall and they're also monitored.

<img src="https://raw.githubusercontent.com/leyume/alx-system_engineering-devops/main/0x09-web_infrastructure_design/images/ALX3.png" />

## Specifics About This Infrastructure

+ The addition of a firewall between each server.<br/>This protects each server from unwanted and unauthorized users rather than protecting a single server.

+ Load balancer<br>
An additional load balance is added to act as a backup if the one them fails and the equally share the load and distribute the request to the servers.

+ Adding another server<br>
Another server is added to make the system more robust. The server structure is now different. The Database now reside on two servers, while the App server reside on the remaining two. This will allow appropriate servers to be used for each service, enabling proper monitoring, i.e knowing what needs to be scaled per time - the Database or the App server with the codebase. Each server still have Web server to make the server accessible by a client if needed. But the Server 1 and Server 3 are going to be the main entries for requests while the communicate with Server 2 or Server 4 if data is needed (created, read, updated or deleted) from the Database.
