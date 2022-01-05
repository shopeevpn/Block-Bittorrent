# Block-Bittorrent
How to Block Bittorrent Traffic with IPtables
IPTABLES is a user-space application program that allows a system administrator to configure the tables provided by the Linux kernel firewall (implemented as different Netfilter modules) and the chains and rules it stores.

Setup the IPtables Rules
To block Bittorrent traffic with IPTABLES you can edit /etc/sysconfig/iptables (CentOS) and include the following:

vim /etc/sysconfig/iptables
First you will want to add the chain right above the first rules

:RH-Firewall-1-INPUT - [0:0]
Then add the following above the COMMIT line

# Torrent ALGO Strings using Boyer-Moore
```
-A RH-Firewall-1-INPUT -m string --algo bm --string "BitTorrent" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "BitTorrent protocol" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "peer_id=" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string ".torrent" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "announce.php?passkey=" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "torrent" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "announce" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "info_hash" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string "/default.ida?" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string ".exe?/c+dir" -j DROP
-A RH-Firewall-1-INPUT -m string --algo bm --string ".exe?/c_tftp" -j DROP
```

# Torrent Keys
```
-A RH-Firewall-1-INPUT -m string --string "peer_id" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "BitTorrent" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "BitTorrent protocol" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "bittorrent-announce" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "announce.php?passkey=" --algo kmp -j DROP
```

# Distributed Hash Table (DHT) Keywords
```
-A RH-Firewall-1-INPUT -m string --string "find_node" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "info_hash" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "get_peers" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "announce" --algo kmp -j DROP
-A RH-Firewall-1-INPUT -m string --string "announce_peers" --algo kmp -j DROP 
```
Restart IPtables
``
/etc/init.d/iptables restart
``
View IPtables/Verify the Rules
``
iptables -L
``
Sample Output
```
Chain RH-Firewall-1-INPUT (0 references)
target     prot opt source               destination         
DROP       all  --  anywhere             anywhere            STRING match "BitTorrent" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "BitTorrent protocol" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "peer_id=" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match ".torrent" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "announce.php?passkey=" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "torrent" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "announce" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "info_hash" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "/default.ida?" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match ".exe?/c+dir" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match ".exe?/c_tftp" ALGO name bm TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "peer_id" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "BitTorrent" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "BitTorrent protocol" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "bittorrent-announce" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "announce.php?passkey=" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "find_node" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "info_hash" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "get_peers" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "announce" ALGO name kmp TO 65535 
DROP       all  --  anywhere             anywhere            STRING match "announce_peers" ALGO name kmp TO 65535
```
```
#!/bin/bash
# Block Torrent algo string using Boyer-Moore (bm)
iptables -A FORWARD -m string --algo bm --string "BitTorrent" -j DROP
iptables -A FORWARD -m string --algo bm --string "BitTorrent protocol" -j DROP
iptables -A FORWARD -m string --algo bm --string "peer_id=" -j DROP
iptables -A FORWARD -m string --algo bm --string ".torrent" -j DROP
iptables -A FORWARD -m string --algo bm --string "announce.php?passkey=" -j DROP
iptables -A FORWARD -m string --algo bm --string "torrent" -j DROP
iptables -A FORWARD -m string --algo bm --string "announce" -j DROP
iptables -A FORWARD -m string --algo bm --string "info_hash" -j DROP
iptables -A FORWARD -m string --algo bm --string "/default.ida?" -j DROP
iptables -A FORWARD -m string --algo bm --string ".exe?/c+dir" -j DROP
iptables -A FORWARD -m string --algo bm --string ".exe?/c_tftp" -j DROP
# Block Torrent keys
iptables -A FORWARD -m string --algo kmp --string "peer_id" -j DROP
iptables -A FORWARD -m string --algo kmp --string "BitTorrent" -j DROP
iptables -A FORWARD -m string --algo kmp --string "BitTorrent protocol" -j DROP
iptables -A FORWARD -m string --algo kmp --string "bittorrent-announce" -j DROP
iptables -A FORWARD -m string --algo kmp --string "announce.php?passkey=" -j DROP
# Block Distributed Hash Table (DHT) keywords
iptables -A FORWARD -m string --algo kmp --string "find_node" -j DROP
iptables -A FORWARD -m string --algo kmp --string "info_hash" -j DROP
iptables -A FORWARD -m string --algo kmp --string "get_peers" -j DROP
iptables -A FORWARD -m string --algo kmp --string "announce" -j DROP
iptables -A FORWARD -m string --algo kmp --string "announce_peers" -j DROP 
```
```
#!/bin/bash
#BlockTorrent
sudo iptables -A FORWARD -m string --string "BitTorrent" --algo bm --to 65535 -j DROP
sudo iptables -A FORWARD -m string --string "BitTorrent protocol" --algo bm --to 65535 -j DROP
sudo iptables -A FORWARD -m string --string "peer_id=" --algo bm --to 65535 -j DROP
sudo iptables -A FORWARD -m string --string ".torrent" --algo bm --to 65535 -j DROP
iptables -A FORWARD -m string --string "announce.php?passkey=" --algo bm --to 65535 -j DROP
iptables -A FORWARD -m string --string "torrent" --algo bm --to 65535 -j DROP
iptables -A FORWARD -m string --string "announce" --algo bm --to 65535 -j DROP
iptables -A FORWARD -m string --string "info_hash" --algo bm --to 65535 -j DROP
iptables -A OUTPUT -p tcp --dport 50321 -j DROP
iptables -A OUTPUT -p udp --dport 50321 -j DROP
iptables -A OUTPUT -d account.sonyentertainmentnetwork.com -j DROP
iptables -A OUTPUT -d auth.np.ac.playstation.net -j DROP
iptables -A OUTPUT -d auth.api.sonyentertainmentnetwork.com -j DROP
iptables -A OUTPUT -d auth.api.np.ac.playstation.net -j DROP
iptables -N LOGDROP > /dev/null 2> /dev/null
iptables -F LOGDROP
iptables -A LOGDROP -j LOG --log-prefix "LOGDROP "
iptables -A LOGDROP -j DROP
iptables -A FORWARD -m string --algo bm --string "BitTorrent" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string "BitTorrent protocol" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string "peer_id=" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string ".torrent" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string "announce.php?passkey=" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string "torrent" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string "announce" -j LOGDROP
iptables -A FORWARD -m string --algo bm --string "info_hash" -j LOGDROP
iptables -A FORWARD -m string --string "get_peers" --algo bm -j LOGDROP
iptables -A FORWARD -m string --string "announce_peer" --algo bm -j LOGDROP
iptables -A FORWARD -m string --string "find_node" --algo bm -j LOGDROP 
iptables -A OUTPUT -p icmp --icmp-type echo-request -j DROP
iptables -A INPUT -p tcp --tcp-flags ALL NONE -j DROP
iptables -A INPUT -p tcp --tcp-flags ALL ALL -j DROP
iptables -A INPUT -f -j DROP
iptables -A INPUT -p tcp ! --syn -m state --state NEW -j DROP
iptables -A INPUT -m string --string "BitTorrent" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "BitTorrent protocol" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "peer_id=" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string ".torrent" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "announce.php?passkey=" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "torrent" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "announce" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "info_hash" --algo bm --to 65535 -j DROP
iptables -A INPUT -m string --string "peer_id" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "BitTorrent" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "BitTorrent protocol" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "bittorrent-announce" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "announce.php?passkey=" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "find_node" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "info_hash" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "get_peers" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "announce" --algo kmp --to 65535 -j DROP
iptables -A INPUT -m string --string "announce_peers" --algo kmp --to 65535 -j DROP
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -I OUTPUT -p tcp --dport 1723 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 6881:6889 -j DROP
iptables -A FORWARD -m string --algo bm --string "BitTorrent" -j DROP
iptables -A FORWARD -p tcp --dport 6881:6889 -j DROP
iptables -D FORWARD -m string --algo bm --string "BitTorrent" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string "BitTorrent protocol" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string "peer_id=" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string ".torrent" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string "announce.php?passkey=" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string "torrent" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string "announce" -j LOGDROP
iptables -D FORWARD -m string --algo bm --string "info_hash" -j LOGDROP
iptables -A FORWARD -m string --string "get_peers" --algo bm -j DROP
iptables -A FORWARD -m string --string "announce_peer" --algo bm -j LOGDROP
iptables -A FORWARD -m string --string "find_node" --algo bm -j LOGDROP
iptables -A FORWARD -p udp -m string --algo bm --string "BitTorrent" -j DROP
iptables -A FORWARD -p udp -m string --algo bm --string "BitTorrent protocol" -j DROP
iptables -A FORWARD -p udp -m string --algo bm --string "peer_id=" -j DROP
iptables -A FORWARD -p udp -m string --algo bm --string ".torrent" -j DROP
iptables -A FORWARD -p udp -m string --algo bm --string "announce.php?passkey=" -j DROP
iptables -A FORWARD -p udp -m string --algo bm --string "torrent" -j DROP 
iptables -A FORWARD -p udp -m string --algo bm --string "announce" -j DROP
iptables -A FORWARD -p udp -m string --algo bm --string "info_hash" -j DROP 
iptables -A FORWARD -p udp -m string --algo bm --string "tracker" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "BitTorrent" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "BitTorrent protocol" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "peer_id=" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string ".torrent" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "announce.php?passkey=" -j DROP  
iptables -A INPUT -p udp -m string --algo bm --string "torrent" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "announce" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "info_hash" -j DROP 
iptables -A INPUT -p udp -m string --algo bm --string "tracker" -j DROP  
iptables -I INPUT -p udp -m string --algo bm --string "BitTorrent" -j DROP 
iptables -I INPUT -p udp -m string --algo bm --string "BitTorrent protocol" -j DROP 
iptables -I INPUT -p udp -m string --algo bm --string "peer_id=" -j DROP 
iptables -I INPUT -p udp -m string --algo bm --string ".torrent" -j DROP 
iptables -I INPUT -p udp -m string --algo bm --string "announce.php?passkey=" -j DROP  
iptables -I INPUT -p udp -m string --algo bm --string "torrent" -j DROP 
iptables -I INPUT -p udp -m string --algo bm --string "announce" -j DROP
iptables -I INPUT -p udp -m string --algo bm --string "info_hash" -j DROP 
iptables -I INPUT -p udp -m string --algo bm --string "tracker" -j DROP  
iptables -D INPUT -p udp -m string --algo bm --string "BitTorrent" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string "BitTorrent protocol" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string "peer_id=" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string ".torrent" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string "announce.php?passkey=" -j DROP  
iptables -D INPUT -p udp -m string --algo bm --string "torrent" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string "announce" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string "info_hash" -j DROP 
iptables -D INPUT -p udp -m string --algo bm --string "tracker" -j DROP  
iptables -I OUTPUT -p udp -m string --algo bm --string "BitTorrent" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string "BitTorrent protocol" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string "peer_id=" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string ".torrent" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string "announce.php?paskey=" -j DROP  
iptables -I OUTPUT -p udp -m string --algo bm --string "torrent" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string "announce" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string "info_hash" -j DROP 
iptables -I OUTPUT -p udp -m string --algo bm --string "tracker" -j DROP
iptables -D INPUT -m string --algo bm --string "BitTorrent" -j DROP 
iptables -D INPUT -m string --algo bm --string "BitTorrent protocol" -j DROP 
iptables -D INPUT -m string --algo bm --string "peer_id=" -j DROP
iptables -D INPUT -m string --algo bm --string ".torrent" -j DROP 
iptables -D INPUT -m string --algo bm --string "announce.php?passkey=" -j DROP  
iptables -D INPUT -m string --algo bm --string "torrent" -j DROP 
iptables -D INPUT -m string --algo bm --string "announce" -j DROP
iptables -D INPUT -m string --algo bm --string "info_hash" -j DROP
iptables -D INPUT -m string --algo bm --string "tracker" -j DROP 
iptables -D OUTPUT -m string --algo bm --string "BitTorrent" -j DROP
iptables -D OUTPUT -m string --algo bm --string "BitTorrent protocol" -j DROP
iptables -D OUTPUT -m string --algo bm --string "peer_id=" -j DROP
iptables -D OUTPUT -m string --algo bm --string ".torrent" -j DROP
iptables -D OUTPUT -m string --algo bm --string "announce.php?passkey=" -j DROP 
iptables -D OUTPUT -m string --algo bm --string "torrent" -j DROP
iptables -D OUTPUT -m string --algo bm --string "announce" -j DROP
iptables -D OUTPUT -m string --algo bm --string "info_hash" -j DROP
iptables -D OUTPUT -m string --algo bm --string "tracker" -j DROP 
iptables -D FORWARD -m string --algo bm --string "BitTorrent" -j DROP
iptables -D FORWARD -m string --algo bm --string "BitTorrent protocol" -j DROP
iptables -D FORWARD -m string --algo bm --string "peer_id=" -j DROP
iptables -D FORWARD -m string --algo bm --string ".torrent" -j DROP
iptables -D FORWARD -m string --algo bm --string "announce.php?passkey=" -j DROP
iptables -D FORWARD -m string --algo bm --string "torrent" -j DROP
iptables -D FORWARD -m string --algo bm --string "announce" -j DROP
iptables -D FORWARD -m string --algo bm --string "info_hash" -j DROP
iptables -D FORWARD -m string --algo bm --string "tracker" -j DROP
```
