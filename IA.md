26/10 : Demande : quel iso utiliser pour un projet de soc et quels outils pour un NIDS ?
        Gardé   : Outils de type suricata, wazzouh, wireshark
        Corrigé : Choix d'un debian 13 pour le projet
        Rejeté  : Ubuntu server car trop difficile à configurer et pas d'interface graphique de base

8/11  : Demande : Qu'est ce que c'est les rules ETO
        Gardé   : ETO rules sont des règles de détection pour suricata

11/11 : Demande : Donnes moi un script python pour interpretation et creation de logs depuis suricata vers wireshark
        Gardé   : rien
        Rejeté  : tout

12/11 : Demande : Génère moi un script bash qui fait un résumé de chaque log séparément
        Gardé   : 

#!/bin/bash
# Résumé des alertes Suricata (hors événements techniques SURICATA)
LOG=${1:-/var/log/suricata/eve.json}
FILTRE='select(.event_type=="alert" and (.alert.signature|startswith("SURICATA")|not))'

echo "=== Total d'alertes ==="
jq -r "$FILTRE | .alert.signature" "$LOG" | wc -l

echo -e "\n=== Alertes par signature ==="
jq -r "$FILTRE | .alert.signature" "$LOG" | sort | uniq -c | sort -rn

echo -e "\n=== Top IP sources ==="
jq -r "$FILTRE | .src_ip" "$LOG" | sort | uniq -c | sort -rn | head -5

echo -e "\n=== Alertes par sévérité ==="
jq -r "$FILTRE | .alert.severity" "$LOG" | sort | uniq -c

echo -e "\n=== 5 dernières alertes ==="
jq -r "$FILTRE | \"\(.timestamp) \(.src_ip) -> \(.dest_ip) : \(.alert.signature)\"" "$LOG" | tail -5