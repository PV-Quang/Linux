#cloud-config
package_update: false
package_upgrade: false

write_files:
  - path: /usr/local/sbin/deploy.sh
    owner: root:root
    permissions: "0755"
    content: |
      #!/usr/bin/env bash
      set -euo pipefail

      exec > >(tee -a /var/log/pre-log.log) 2>&1

      echo "[INFO] Start at $(date)"

      rm -rf /etc/netplan/*

      cat > /etc/netplan/network.yaml <<'EOF'
      network:
        version: 2
        ethernets:
          ens192:
            dhcp4: false
            dhcp6: false
            addresses:
              - 10.0.0.10/24
            routes:
              - to: default
                via: 10.0.0.1
            nameservers:
              addresses:
                - 8.8.8.8
      EOF

      netplan apply

      systemctl enable --now systemd-resolved.service
      systemctl restart systemd-resolved.service
      ln -sf /var/run/systemd/resolve/resolv.conf /etc/resolv.conf
      hostnamectl set-hostname mailcow
      sed -i "2i 127.0.1.1 ${HOSTNAME_FQDN}" /etc/hosts
      sed -i '3i\\' /etc/hosts



      echo "[INFO] Waiting for network..."
      for i in $(seq 1 30); do
        if ping -c1 -W1 8.8.8.8 >/dev/null 2>&1; then
          echo "[INFO] Network OK"
          break
        fi
        sleep 2
      done

      apt update -y && apt upgrade -y


runcmd:
  - [ bash, -lc, "/usr/local/sbin/deploy.sh" ]

power_state:
  mode: reboot
  message: "Reboot after cloud-init provisioning"
  timeout: 10

  condition: true
