# IoT Zero-Trust Network 

- **ESP32-Geräte** fungieren als IoT-Knoten im industriellen Netzwerk und übernehmen Aufgaben wie die Datenerfassung (z. B. Sensordaten) oder die Steuerung von Maschinen. Über das SDN (Software-Defined Networking) wird jedes Gerät dynamisch segmentiert, um den Zugriff auf andere Netzbereiche einzuschränken und Sicherheitsrisiken zu minimieren.

- Der **Raspberry Pi 5** dient als **zentraler Gateway** und übernimmt die Steuerung des Netzwerkverkehrs über den SDN-Controller. Er setzt Sicherheitsrichtlinien durch, überwacht die Kommunikation und entscheidet, welche Geräte auf welche Ressourcen zugreifen dürfen.
    - Der SDN-Controller überwacht den gesamten Netzwerkverkehr und stellt sicher, dass nur autorisierter Datenverkehr zwischen den ESP32-Geräten und dem Raspberry Pi zugelassen wird. **Flow-Regeln** definieren, welche Geräte miteinander kommunizieren dürfen, basierend auf den Zero-Trust-Richtlinien.
 
- Eine **Public Key Infrastructure (PKI)** stellt die **sichere Authentifizierung** der ESP32-Geräte und des Raspberry Pi sicher. Jedes Gerät erzeugt ein **Schlüsselpaar** (privater und öffentlicher Schlüssel) und erhält ein **x.509-Zertifikat** von einer zentralen **Certificate Authority (CA)**, die auf dem Raspberry Pi eingerichtet ist.
    - Das Root-Schlüsselpaar der PKI wird auf einem isolierten Raspberry Pi Pico gespeichert, der über USB mit dem Raspberry Pi verbunden ist. Der Pi selbst agiert als Intermediate CA. Der private Schlüssel der Intermediate CA wird in einem isolierten Container verwahrt und genutzt.
      
- Die Kommunikation zwischen den Geräten wird durch **TLS (Transport Layer Security)** verschlüsselt, um sicherzustellen, dass nur authentifizierte Geräte miteinander Daten austauschen und die Vertraulichkeit der Daten gewahrt bleibt.
  
- Der **Open Policy Agent (OPA)** verwaltet zentral und dynamisch die **Zugriffsrichtlinien** für das Netzwerk. Der SDN-Controller setzt die von OPA getroffenen Entscheidungen um, die auf Kriterien wie Gerätetypen, Netzwerksegmenten oder zeitbasierten Bedingungen basieren. Diese Richtlinien können in Echtzeit angepasst werden, um den aktuellen Anforderungen oder Sicherheitsbedingungen gerecht zu werden. Beispielsweise könnte OPA festlegen, ob ein Gerät in einem bestimmten Zustand auf eine Ressource zugreifen darf.
  
- Der **Raspberry Pi 5** fungiert zudem als **Firewall** und steuert zusammen mit dem SDN-Controller den Netzwerkverkehr zwischen den ESP32-Geräten. Tools wie **iptables** oder **ufw** können auf dem Raspberry Pi konfiguriert werden, um zusätzliche Sicherheitsregeln zu definieren, die die SDN-Flow-Regeln ergänzen.
  
- Die verschiedenen Anwendungen auf dem Raspberry Pi laufen in isolierten Containern, um eine zusätzliche Sicherheitsschicht zu gewährleisten und Interferenzen zwischen den Anwendungen zu vermeiden. Die Orchestrierung der Container wird mit Docker Swarm übernommen.
  
- System-Komponenten:
    1. Raspberry Pi
        1. vSwitch (nativ) 
        2. Firewall (nativ)
        3. SDN-Controller (Container) 
        4. OPA (Container) 
        5. PKI (Container)  
            1. Subordiante CA (Container)  
    2. Raspberry Pi Pico
        1. Root CA 
    3. ESP32s
