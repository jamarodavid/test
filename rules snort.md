# Konfigurasi Rules Snort
Konfigurasi di file /etc/snort/rules/local.rules

## ICMP Detect
Konfigurasi snort untuk mendeteksi percobaan ping

alert icmp any any -> $HOME_NET any (msg:"ICMP connection attempt";sid:1000001;rev:1;classtype:icmp-event;)

-----------------------------------------------------------------------------------------------------------------
## Detect Error Based SQL Injection
Konfigurasi snort mendeteksi serangan SQL Injection

alert tcp any any -> any 80 (msg: "Error Based SQL Injection Detected"; content: "%27" ; sid:100000011; )
alert tcp any any -> any 80 (msg: "Error Based SQL Injection Detected"; content: "22" ; sid:100000012; )

-----------------------------------------------------------------------------------------------------------------
## Detect Boolean Based SQL Injection
Konfigurasi snort mendeteksi serangan SQL Injection dengan operator And/Or

alert tcp any any -> any 80 (msg: "AND SQL Injection Detected"; content: "and" ; nocase; sid:100000060; )
alert tcp any any -> any 80 (msg: "OR SQL Injection Detected"; content: "or" ; nocase; sid:100000061; )

-----------------------------------------------------------------------------------------------------------------
## Detect Encoded And/Or Based SQL Injection
Konfigurasi snort mendeteksi serangan SQL Injection dengan operator And/Or yang di encode

alert tcp any any -> any 80 (msg: "AND SQL Injection Detected"; content: "and" ; nocase; sid:100000008; )
alert tcp any any -> any 80 (msg: "OR SQL Injection Detected"; content: "or" ; nocase; sid:100000009; )

-----------------------------------------------------------------------------------------------------------------
## Detect Form Based SQL Injection
Konfigurasi snort mendeteksi serangan SQL Injection melalui login form

alert tcp any any -> any 80 (msg: "Form Based SQL Injection Detected"; content: "%27" ; sid:1000003; )

-----------------------------------------------------------------------------------------------------------------
## Detect Order by SQL Injection
Konfigurasi snort mendeteksi serangan SQL Injection melalui Order by

alert tcp any any -> any 80 (msg: "Order by SQL Injection"; content: "order" ; sid:1000005; )

-----------------------------------------------------------------------------------------------------------------
## Detect Union based SQL Injection
Konfigurasi snort mendeteksi serangan SQL Union Injection

alert tcp any any -> any 80 (msg: "UNION SELECT SQL Injection"; content: "union" ; sid:1000006; )

-----------------------------------------------------------------------------------------------------------------
## Detect XSS Header Attack
Konfigurasi snort mendeteksi serangan XSS

alert tcp any any -> any 80 (msg:"XSS Detected"; content:"<script>"; http_client_body; sid:111;)


-----------------------------------------------------------------------------------------------------------------
## ALL
alert icmp any any -> $HOME_NET any (msg:"ICMP connection attempt";sid:1000001;rev:1;classtype:icmp-event;)
alert tcp any any -> any 80 (msg: "Error Based SQL Injection Detected"; content: "%27" ; sid:100000011; )
alert tcp any any -> any 80 (msg: "Error Based SQL Injection Detected"; content: "22" ; sid:100000012; )
alert tcp any any -> any 80 (msg: "AND SQL Injection Detected"; content: "and" ; nocase; sid:100000060; )
alert tcp any any -> any 80 (msg: "OR SQL Injection Detected"; content: "or" ; nocase; sid:100000061; )
alert tcp any any -> any 80 (msg: "AND SQL Injection Detected"; content: "and" ; nocase; sid:100000008; )
alert tcp any any -> any 80 (msg: "OR SQL Injection Detected"; content: "or" ; nocase; sid:100000009; )
alert tcp any any -> any 80 (msg: "Form Based SQL Injection Detected"; content: "%27" ; sid:1000003; )
alert tcp any any -> any 80 (msg: "Order by SQL Injection"; content: "order" ; sid:1000005; )
alert tcp any any -> any 80 (msg: "UNION SELECT SQL Injection"; content: "union" ; sid:1000006; )
alert tcp any any -> any 80 (msg:"XSS Detected"; content:"<script>"; http_client_body; sid:111;)




# Jangan lupa runnya pakai : 

# sudo snort -A console -q -u snort -g snort -c /etc/snort/snort.conf -i enp0s8
