মাইক্রোটিক রাউটার ওএস এর স্ক্রিপ্টিং এর মাধ্যমে আমরা আমাদের অফিসের রাউটার কে কনফিগার করতে পারি যদিও এই একই কাজটা আমরা গ্রাফিক্যাল ইউজার ইন্টারফেস দিয়েও করতে পারি কিন্তু যারা টার্মিনাল ব্যবহারে অব্যস্থ তাদের উচিত কমান্ড বা স্ক্রিপ্টিং ব্যবহার করে কাজগুলো করা।
যাই হোক আমরা দেখবো কীভাবে আমরা কমান্ড লাইন ব্যবহার করে একটা স্ট্যাটিক আইপি সেট করতে পারি। 

ধরা যাক আমরা আমাদের ইন্টারনেট সার্ভিস প্রোভাইডার থেকে নিম্নুক্ত বিস্তারিত জিনিসগুলো পেয়েছি - 
```html
পাবলিক আইপি - ১৯২.১৬৮.৫০.০/৩০ 
গেটওয়ে - ১৯২.১৬৮.৫০.১ 
ডি এন এস - ৮.৮.৮.৮ 
```
এখন আমরা যেইভাবে আমাদের মাইক্রোটিক রাউটারটি কনফিগার করবো সেটার প্রক্রিয়া নিচে দেওয়া হল - \

ধাপ ১ : প্রথমেই আমাদের WAN এর একটা পোর্ট  সিলেক্ট করতে হবে যেই পোর্ট এর মাধ্যমে আমরা আমরা আমাদের আইএসপি থেকে প্রাপ্ত নেটওয়ার্ক সম্পর্কিত সেটআপ গুলো কনফিগার করবো। এর জন্যে প্রয়োজনীয় কমান্ড নিচে দেওয়া হল -
```sh
    # আমাদের মাইক্রোটিক রাউটার এর ইন্টারফেস ১ এ WAN কনফিগার করার প্রক্রিয়া
    ip address add address=192.168.50.2/30 interface=ether1
```

ধাপ ২ : এই পর্যায়ে  আমাদের LAN এর জন্যে একটা পোর্ট  সিলেক্ট করতে হবে এবং এই পোর্টে আমরা যেই আইপি সিরিজ আমার ইউজার দের দিতে চাই সেই আইপি এড্রেস বলে দিতে হবে। এর জন্যে প্রয়োজনীয় কমান্ড নিচে দেওয়া হল -
```sh
   # আমাদের মাইক্রোটিক রাউটার এর ইন্টারফেস ২ এ LAN কনফিগার করার প্রক্রিয়া এবং আইপি      ডিস্ট্রিবিউট করার প্রক্রিয়া।
   ip address add adress=192.168.1.0/24 interface=ehter2
```

ধাপ ৩ : এখন আমাদেরকে রাউট বলে দিতে হবে, এই রাউট সাধারণত ISP এন্ড এর আইপি বসাতে হয় অথবা ISP এন্ড থেকে প্রাপ্ত Gateway এর এডড্রেসটি বসাতে হয়, এর জন্যে প্রয়োজনীয় কমান্ড নিচে দেওয়া হল -
```sh
   # রাউট বা গেটওয়ে কনফিগার করার প্রক্রিয়া 
   ip route add gateway=192.168.50.1
```

### সোর্স রাউট কনফিগার করার প্রক্রিয়া 
ধাপ ৪ : এখন আমাদেরকে সোর্স রাউট কনফিগার করতে হবে অর্থাৎ ফায়ারওয়াল এ srcnat কে masqurate করে দিতে হবে যাতে করে আমাদের LAN এর ইউজার ইন্টারনেট পেতে পারে, এর জন্যে প্রয়োজনীয় কমান্ড নিচে দেওয়া হল -
```sh
   # সোর্স রাউট কনফিগার করার প্রক্রিয়া
   ip firewall nat add chain=srcnat action=masquerade
```

### DNS কনফিগার করার প্রক্রিয়া 
ধাপ ৫ : এখন আমাদেরকে নিম্নুক্ত কমান্ড ব্যবহার করে DNS কনফিগার করতে হবে এর জন্যে প্রয়োজনীয় কমান্ড সমূহ -
```sh
   # DNS কনফিগার করার প্রক্রিয়া 
   ip dns set servers=8.8.8.8 allow-remote-requests=yes   
```

উপরুক্ত ধাপগুলো ব্যবহার করে আমরা একটা মাইক্রোটিক রাউটার সেটআপ করতে পারি Static IP ব্যবহার করে  এবং  আমাদের ইউজারদেরকে ইন্টারনেট সেবা দিতে পারি। এখানে উল্লেখিত যে আমরা যেই আইপি সিরিজ (১৯২.১৬৮.১.০/২৪) আমাদের লোকাল ইউজারদের জন্যে ইন্টারফেস ২ এ সেটআপ করেছি সেখানে কিছু আইপি আমরা ব্যবহার করতে পারবো না। এই আইপি গুলো নিচে দেওয়া হল - 
```sh
   ১৯২.১৬৮.১.০ - নেটওয়ার্ক এড্রেস 
   ১৯২.১৬৮.১.১ - গেটওয়ে 
   ১৯২.১৬৮.১.২৫৬ - ব্রডকাস্ট এড্রেস 
```

আমরা যদি আমাদের স্ক্রিপ্টটাকে দেখতে চাই তাহলে -
```sh
    # আমাদের মাইক্রোটিক রাউটার এর ইন্টারফেস ১ এ WAN কনফিগার করার প্রক্রিয়া
    ip address add address=192.168.50.2/30 interface=ether1

   # আমাদের মাইক্রোটিক রাউটার এর ইন্টারফেস ২ এ LAN কনফিগার করার প্রক্রিয়া এবং 
       আইপি      ডিস্ট্রিবিউট করার প্রক্রিয়া।
    ip address add adress=192.168.1.0/24 interface=ehter2

   # রাউট বা গেটওয়ে কনফিগার করার প্রক্রিয়া 
   ip route add gateway=192.168.50.1

   # সোর্স রাউট কনফিগার করার প্রক্রিয়া
   ip firewall nat add chain=srcnat action=masquerade

   # DNS কনফিগার করার প্রক্রিয়া 
   ip dns set servers=8.8.8.8 allow-remote-requests=yes 
```


# HotSpot configuration
Configure IP
```sh
/ip address
add address=192.168.1.1/24  interface=ether1
add address=103.100.0.2/30  interface=ether2 
 
ADD IP pool
/ip pool
add name=hs-pool-1 ranges=192.168.1.10-192.168.1.254
 
Add DNS Server
/ip dns
set allow-remote-requests=yes servers=8.8.8.8
 
Add DHCP Server
/ip dhcp-server
add address-pool=hs-pool-1 authoritative=after-2sec-delay bootp-support=static disabled=no interface=ether1 lease-time=1h name=dhcp1
/ip dhcp-server config set store-leases-disk=5m
/ip dhcp-server network add address=192.168.1.0/24 comment="hotspot network" gateway=192.168.1.1


 
Add HOTSPOT
/ip hotspot profile
 
set default dns-name="" hotspot-address=0.0.0.0 html-directory=hotspot http-cookie-lifetime=3d http-proxy=0.0.0.0:0 login-by=cookie,http-chap name=default rate-limit="" smtp-server=0.0.0.0 split-user-domain=no use-radius=no
add dns-name=login.itcian.local hotspot-address=192.168.1.1 html-directory=hotspot http-cookie-lifetime=1d http-proxy=0.0.0.0:0 login-by=cookie,http-chap name=hsprof1 rate-limit="" smtp-server=0.0.0.0 split-user-domain=no use-radius=no
 

/ip hotspot
add address-pool=hs-pool-1 addresses-per-mac=1 disabled=no idle-timeout=5m interface=ether1 keepalive-timeout=none name=hotspot1 profile=hsprof1
 

/ip hotspot user profile 
set default idle-timeout=none keepalive-timeout=2m name=default shared-users=1 status-autorefresh=1m transparent-proxy=no 
add address-pool=hs-pool-1 advertise=no idle-timeout=none keepalive-timeout=2m name="1M Limit" open-status-page=always rate-limit=1M/1M shared-users=1 status-autorefresh=1m transparent-proxy=yes 
add address-pool=hs-pool-1 advertise=no idle-timeout=none keepalive-timeout=2m name="2M Limit" open-status-page=always rate-limit=2M/2M shared-users=1 status-autorefresh=1m transparent-proxy=yes 

/ip hotspot service-port set ftp disabled=yes ports=21 
/ip hotspot walled-garden ip add action=accept disabled=no dst-address=192.168.1.1 
/ip hotspot set numbers=hotspot1 address-pool=none 
/ip firewall nat add action=masquerade chain=srcnat disabled=no 
/ip hotspot user add disabled=no name=admin password=123 profile=default 
/ip hotspot user add disabled=no name=test1 password=12345 profile="1M Limit" server=hotspot1 
/ip hotspot user add disabled=no name=test2 password=12345 profile="2M Limit" server=hotspot1 

Add Default route
/ip route add gateway=103.100.0.1
```

