A content delivery network (CDN) is a globally distributed network of proxy servers, serving content from locations closer to the user. Generally, static files such as HTML/CSS/JS, photos, and videos are served from CDN, although some CDNs such as Amazon's CloudFront support dynamic content. The site's DNS resolution will tell clients which server to contact.

Serving content from CDNs can significantly improve performance in two ways:

- Users receive content from data centers close to them (faster)
- Your servers do not have to serve requests that the CDN fulfills

## Push & Pull CDNs

**Push CDNs**, require you to push your content to it in order to be server to the user.
**Pull CDNs** on the other hand, pull the files themselves and cashe them for later

### **Key Parameters:**

1. **TTL (Time-To-Live):**
    - **For Pull CDNs:** How long the CDN caches a file before checking your server for a new version. A shorter TTL means users see updates faster but puts more load on your origin server.
2. **Cache Invalidation:**
    - **For Push CDNs:** If you change a file, you must **re-push** it to the CDN (or use an API to purge the old version).
    - **For Pull CDNs:** You either wait for the TTL to expire or actively **purge** the cache to force a refresh.

### **CDN Strategyies**

**1. Reverse Proxy CDN (Cloudflare Model)**

- **Concept:** Put your **entire website/domain** behind the CDN. It acts as a smart gateway.
- **Setup:** Change your DNS to point to the CDN, Point the CDN to your app. Zero code changes.
- **How it Works:** All traffic for `yourdomain.com` goes to the CDN first. It serves cached assets and forwards dynamic requests to your origin server (Railway).
- **Pros:**
    - **Zero Code Changes:** The easiest setup.
    - **Full-Site Benefits:** Caching, DDoS protection, and security for your entire app.
- **Cons:**
    - **Less Control:** Cache rules are applied broadly via page rules, not per file-type.

**2. Direct CDN Linking**

- **Concept:** Host your *static assets* (JS, CSS, images) on a separate, high-performance CDN domain.
- **Setup:** You must change your code to point asset URLs to the CDN (e.g., `src="https://cdn.yourdomain.com/logo.png"`).
- **How it Works:** The browser loads your HTML from your main domain but fetches static files directly from the CDN domain, bypassing your server completely.
- **Pros:**
    - **Maximum Performance:** Optimized for static file delivery.
    - **Granular Control:** Precise TTL and cache settings for each asset type.
- **Cons:**
    - **Code Changes Required:** You must structure your app to use the CDN URLs from the start.

# Application (Readov.com) **My own thoughts**

I have been monitoring the HTTP logs in railway of my web app Readov.com, and I have noticed someone is trying to access “hidden pages”, (/admin.php,  /config.php, … ). Not for one or two days, but it’s been for a week now and he’s spaming my app.

Although I have a rate limiter with some strict rules, and it’s doing a great job, but it’s not enough. I tried to find a solution.

The first solution is that I blocked some IPs from requesting my app, I got them from the HTTP logs, serveral IPs. But I knew after it that he’s probably using a vpn, the ip is changing so this didnt work.

The second solution is using a reverse proxy, aka Cloudflare. This is my savior. when I searched about it I thought : “Every web app should use this” . it can filter suspecious requests (like the ones I want to block), servers as a CDN serving static files without any changes on the code, and protecting from Ddos Attacks and other benifits So this is the key. 

I’m gonna try to share with you the screenshots of the steps I followed to link my web app to Cloudflare .

![image.png](/Static/Railway_logs.png)

IT WOOOORKED ^^

![image.png](/Static/Blocked.png)

So basically here are the steps I did to overcome this problem :

1. Connected my domain to cloudflare: By addding the nameservers cloudflare gave me to NameCheap, Which resulted in :
    1. Cloudflare being my DNS provider
    2. All requests now go through Cloudflare before hitting my real server
2. Cloudflare became My reverse proxy
3. Cloudflare automatically imported your DNS records. So I didnt have to connect it manually to Railway
4. I created a WAF Security Firewall Rule
    
    **Block requests trying to access sensitive files or system folders.**
    
    Examples:
    
    - `.php`, `.env`, `.sql`, `.bak`, `.json`
    - `/admin`, `/config`, `/.git`, `/phpmyadmin`

Quick note: CloudFlare is used here as a pull CDN !