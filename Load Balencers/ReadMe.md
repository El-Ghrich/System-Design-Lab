> **The first golden rule for scalability: every server contains exactly the same codebase and does not store any user-related data, like sessions or profile pictures, on local disc or memory.**
> 

Load Balencers distribute client requests to computing resources (servers, databases …) evenly, to prevent overwhelming a component, And to ensure the golden rule, when having a multi server architecture we must pay attention to some things like :

- **Sessions:** Use JWT (stateless) or Redis (centralized state)
- **Files:** Object storage (S3) + CDN, never local disk

## **Load Balancer Types**

### **Layer 4 (Transport)**

- **Operates on:** TCP/UDP level
- **Sees:** IP addresses + ports only
- **Use Cases:** High-performance needs (databases, gaming, VoIP)
- Example : Request comes to `VIP:443` → LB routes to `Server1:443` or `Server2:443`

### **Layer 7 (Application)**

- **Operates on:** HTTP/HTTPS level
- **Sees:** URLs, headers, cookies, content
- **Use Cases:** Web apps, microservices, content-based routing
- Example : (Nginx)

```
 location /api/ {
        proxy_pass http://backend-api-pool;  # Plain HTTP to backend
    }
```

### **SSL Termination**

The load balancer handles **SSL/TLS encryption/decryption** instead of passing encrypted traffic to backend servers. This can help reduce the heavy load of crypto, and **Centralize certificate management** plus a security benifit.

## Load Balencing Algorithmes

There are two primary approaches to [load balancing](https://www.cloudflare.com/learning/performance/what-is-load-balancing/). ***Dynamic load balancing*** uses algorithms that take into account the current state of each server and distribute traffic accordingly. ***Static load balancing*** distributes traffic without making these adjustments. Some static algorithms send an equal amount of traffic to each server in a group, either in a specified order or at random.

### **Dynamic load balancing algorithms**

1. ***Least connection** :*  Checks which servers have the fewest connections open at the time and sends traffic to those servers. This assumes all connections require roughly equal processing power.

```nix
upstream backend {
    least_conn;  # Send to server with fewest active connections
    server 192.168.1.10:3000;
    server 192.168.1.11:3000;
    server 192.168.1.12:3000;
}
```

1. ***Resource-based** :*  Distributes load based on what resources each server has available at the time. Specialized software (called an "agent") running on each server measures that server's available CPU and memory, and the load balancer queries the agent before distributing traffic to that server

```nix
# This requires specialized load balancers or custom solutions
# Not typically available in simple load balancers like NGINX

# Pseudo-configuration:
upstream backend {
    resource_based;
    server 192.168.1.10:3000 max_cpu=80%;  # Don't send if CPU > 80%
    server 192.168.1.11:3000 max_memory=90%; # Don't send if memory > 90%
    server 192.168.1.12:3000 max_iops=1000;  # Don't send if disk busy
}
```

### **Static load balancing algorithms**

Static algorithms make routing decisions **based on predefined rules** without considering current server state, load, or performance.

1. **Round-Robin :** Distributes requests sequentially to each server in rotation.

```nix
upstream backend {
    # Simple round robin
    server 192.168.1.10:3000;
    server 192.168.1.11:3000;
    server 192.168.1.12:3000;
}
```

1.  **Weighted Round Robin : just like :** Round robin, but servers with higher weights get more requests.

```nix
upstream backend {
    server 192.168.1.10:3000 weight=3;  # Gets 3 requests
    server 192.168.1.11:3000 weight=2;  # Gets 2 requests
    server 192.168.1.12:3000 weight=1;  # Gets 1 request
}
```

# Reverse Proxy

A proxy is a device or server that acts on behalf of other devices. It sits between two entities and performs a service

A Reverse proxy server sits in front of web servers and forwards client (e.g. web browser) requests to those web servers. It acts as a single entry point, hiding the internal infrastructure from clients

### **Key Responsibilities:**

- SSL termination
- Request routing
- Caching
- Security (WAF)

**Example:** NGINX handling SSL, rewriting URLs, serving static files