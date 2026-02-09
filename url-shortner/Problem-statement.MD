* Is a URL shortening service that conversts long URLs into short unique linnks for easy tracking and shorting.

  Excalidraw document -> https://excalidraw.com/#json=h-PI310MJgs_e6mVpRx3v,FypLsV_Ad_OxPoZAWwMHqg
* Basic user journey.
  -------------------


  1. User submits a short url.
  2. System generates a unique short code.
  3. Stores  mapping in the database.
  4. Redirects short URL to original.


Estimating scale.

1. Daily Active Users ( DAU) 10M
2. Monthly active users ( MAU) 300M
3. New short urls/day - 100,000
4. Redirect requests/day - 50 M ( 5 per user)
   Memory requirements. ( Hot URL cache)
5. Cache top 1M most accessed URLS.
   2.Each mapping = 500bytes.
   Total memory = 500MB.
   Network bandwidth.

* 50M redirects/day * 700 bytes = 35GB per day.
* Avg through put 0.4MB/sec
* Peak throughput - 5MB/sec
  Storage requirements.
* 100k new URLS / day * 500bytes. = 50MB /day
* Yearly data = 18GB raw + overhead.
* ROund up to 50GB/year ( indexes, logs, backups.)

Functional requirements. 

1. Shorten a URL.
2. Redirect to original URL.
3. Prevent duplicated short URLS.
4. User authentication.

Non functional requirements.

1. High availability. - must be available with 99.9% uptime.
2. Performance and low latency - Redirection should happen in milliseconds. Shortening URLS must be near instatenous.
3. Scalability. Should handle millions / billions of URLS. Supporting high read volums. and moderate write volume.
4. Reliability. Ensure data persistence and no data loss even during failures using durable storage and back ups.

Bottleneck identiofication. 

1. High read volume - Focus on cache and fast DB reads.
2. Write throughput is moderate. - Ensure consistency.
3. Latency sensitivity in redirects. - Low latency infra is needed.
4. Plan for burst traffic with autoscaling and CDN.


High Level Design.

1. Create Short URl.  POST api/shorten

- Accepts a long url and returns a shortened one.

2. Redirect URL Get /api/:short-key

- Redirect to Original URL.
- Looks up the original Long URl using the short key.
- Returns a 302 HTTP Redirect to the Long URl.



High Level Design.

1. Create Short URl.  POST api/shorten

- Accepts a long url and returns a shortened one.

2. Redirect URL Get /api/:short-key

- Redirect to Original URL.
- Looks up the original Long URl using the short key.
- Returns a 302 HTTP Redirect to the Long URl.

3. Delete /api/url/:short_key

- Deletes the mapping if user is authenticated and owns the URL.
- Requires Bearer token for authorization.

4. User Registration /api/auth/registeremail + password as
5. User Login POST /api/auth/login/



HLD.

1. Api gateway - entry point for all clients, handles routing, rate limiting, auth.
2. URL shortner service.
3. Redirect Service. - high p4mance resolver for short keys -> long keys.
4. Database. - Persistent store for all mapping, users, metadata.
5. Cache layer. Redis/Memcached for top N frequently accessed URLS.
6. Auth Service. Manages user login. JWt tokens and sessions.


Tech & Infra Decisions.

1. Database.
   * SQL - for structed data
   * No SQL - e.g Redis for caching, DynamoDb for scalability.
   * Cache: Redis or memcached for high speed lookup.
2. Scalability.
   * horizontal scaling for URL generatipn services.
3. High availability.
   * Load balancer to distribute traffic across services instances.
   * Replication in Db to avoid single points of failure.
   * Failover ready infa using cloud managed DBs or services.
