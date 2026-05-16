# knowledge-of-Linux
---
НАСТРОЙКА IPSEC И GRE НА MIKROTIK
---
1. Настройка первой фазы (IKE Phase 1)
На L-FW (172.16.1.1):

        /ip ipsec profile add name="Phase1-profile" \
          hash-algorithm=sha1 \
          enc-algorithm=3des \
          dh-group=modp2048 \
          nat-traversal=yes

        /ip ipsec peer add address=172.16.1.2/32 \
          exchange-mode=main \
          profile="Phase1-profile" \
          name="R-FW"
На R-FW (172.16.1.2):

        /ip ipsec profile add name="Phase1-profile" \
          hash-algorithm=sha1 \
          enc-algorithm=3des \
          dh-group=modp2048 \
          nat-traversal=yes

        /ip ipsec peer add address=172.16.1.1/32 \
          exchange-mode=main \
          profile="Phase1-profile" \
          name="L-FW"
          
2. Настройка второй фазы (IPsec Phase 2 / ESP)
На L-FW и R-FW (конфигурация одинаковая):

        /ip ipsec proposal add name="Phase2-proposal" \
          auth-algorithms=sha256 \
          enc-algorithms=aes-128-cbc \
          pfs-group=modp2048 \
   
4. Настройка Identity и Policy (Аутентификация и политики)
На L-FW (172.16.1.1):

        /ip ipsec identity add \
          peer="R-FW" \
          auth-method=pre-shared-key \
          secret="KKKEP-2026"

        /ip ipsec policy add \
          src-address=172.16.1.1/32 \
          dst-address=172.16.1.2/32 \
          protocol=gre \
          action=encrypt \
          proposal="Phase2-proposal" \
          peer="R-FW" \
          tunnel=yes\
   
На R-FW (172.16.1.2):

      /ip ipsec identity add \
        peer="L-FW" \
        auth-method=pre-shared-key \
        secret="KKKEP-2026"

      /ip ipsec policy add \
        src-address=172.16.1.2/32 \
        dst-address=172.16.1.1/32 \
        protocol=gre \
        action=encrypt \
        proposal="Phase2-proposal" \
        peer="L-FW" \
        tunnel=yes
        
4. Настройка GRE-туннеля
На L-FW:

        /interface gre add name=gre-tunnel \
          remote-address=172.16.1.2 \
           local-address=172.16.1.1

        /ip address add address=10.5.5.1/30 interface=gre-tunnel

На R-FW:

      /interface gre add name=gre-tunnel \
          remote-address=172.16.1.1 \
          local-address=172.16.1.2

      /ip address add address=10.5.5.2/30 interface=gre-tunnel
