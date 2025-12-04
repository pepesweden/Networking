conf t

! Konfigurera IKE Policy
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 2
 lifetime 3600
exit

! Aktivera ISAKMP på det externa gränssnittet
crypto isakmp enable outside

! Skapa Transform-set
crypto ipsec transform-set IPSEC-tunnel esp-aes-256 esp-sha-hmac

! Skapa Access-listor
access-list LAN_Traffic extended permit ip 192.168.10.0 255.255.255.0 192.168.20.0 255.255.255.0
access-list outside_access_in extended permit udp any host 198.51.100.1 eq 500
access-list outside_access_in extended permit udp any host 198.51.100.1 eq 4500
access-list outside_access_in extended permit esp any host 198.51.100.1

! Skapa Tunnel-group
tunnel-group 198.51.100.1 type ipsec-l2l
tunnel-group 198.51.100.1 ipsec-attributes
 pre-shared-key enhemlignyckel

! Skapa Crypto Map
crypto map IPSECVPN 1 match address LAN_Traffic
crypto map IPSECVPN 1 set peer 198.51.100.1
crypto map IPSECVPN 1 set transform-set IPSEC-tunnel
crypto map IPSECVPN interface outside

! Routing konfiguration (om nödvändigt)
route outside 192.168.20.0 255.255.255.0 198.51.100.1

! Tillämpa Access-listor på det externa gränssnittet
access-group outside_access_in in interface outside

end
