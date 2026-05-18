# knowledge-of-Linux
---
НАСТРОЙКА IPSEC И GRE НА MIKROTIK
---
Настройка GRE туннеля
На L-FW:

        /interface gre add name=gre-tun1 local-address=10.10.10.1 remote-address=20.20.20.100 mtu=1400
        /ip address add address=10.5.5.1/30 interface=gre-tun1
        
На R-FW:

        /interface gre add name=gre-tun1 local-address=20.20.20.100 remote-address=10.10.10.1 mtu=1400
        /ip address add address=10.5.5.2/30 interface=gre-tun1

Настройка IPsec
На L-FW:

        /ip ipsec proposal add name="phase2" auth-algorithms=sha256 enc-algorithms=aes-128-cbc pfs-group=modp2048
        /ip ipsec peer add name="R-FW" address=20.20.20.100/32 profile=default exchange-mode=main
        /ip ipsec identity add peer="R-FW" auth-method=pre-shared-key secret="KKKEP-2026"
        /ip ipsec policy add src-address=10.10.10.1/32 dst-address=20.20.20.100/32 protocol=gre action=encrypt peer="R-FW" proposal=phase2
        
На R-FW:

        /ip ipsec proposal add name="phase2" auth-algorithms=sha256 enc-algorithms=aes-128-cbc pfs-group=modp2048
        /ip ipsec peer add name="L-FW" address=10.10.10.1/32 profile=default exchange-mode=main
        /ip ipsec identity add peer="L-FW" auth-method=pre-shared-key secret="KKKEP-2026"
        /ip ipsec policy add src-address=20.20.20.100/32 dst-address=10.10.10.1/32 protocol=gre action=encrypt peer="L-FW" proposal=phase2
