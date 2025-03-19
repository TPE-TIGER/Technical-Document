# Add Custom Firewall Rules on AIG

Document Version: V1.0

### Change Log

| Version | Date       | Content           |
| ------- | ---------- | ----------------- |
| 1.0     | 2025-03-18 | document created. |

##### Applicable Products
| Product | Version |
| ------- | ------- |
| AIG-302 | 1.0.0, 1.1.0 |

## Purpose

The built-in firewall settings in the product only support basic allow and forward rules. However, since the system manages firewall rules using chains, users can apply custom rules outside of the predefined chains without interference from the system.

This document provides guidance on how to configure additional firewall rules while ensuring compatibility with the system’s built-in settings.

## Steps

1. Create a nftables rules file. The following example shows how to configuring masquerading, replace the source IP of packets leaving the host through the `${WAN}` interface to the IP set on `${WAN}`.
    ```bash
    $ cat /home/moxa/nftrules.sh
    #!/bin/sh

    # add table if not exist
    nft add table inet nat

    # add chain if not exist
    nft add chain inet nat user-postrouting-test { type nat hook postrouting priority 100 \; }

    # add required rules
    nft add rule inet nat user-postrouting-test oif "${WAN}" masquerade
    nft add rule inet nat POSTROUTING jump user-postrouting-test
    ```
    Modify the chain (`user-postrouting-test`) and interface `${WAN}` in the example as needed.
    > Note: Chains with the prefix `moxa-` may be used by the system. Please avoid using them to prevent rules from being flushed unexpectedly.

2. Edit `/etc/rc.local` to write the rules into nftables.
    ```bash
    $ cat>>/etc/rc.local<<EOF
    #!/bin/sh

    sleep 30

    /home/moxa/nftrules.sh
    EOF
    ```

3. Add and enable `rc-local` service to make sure the firewall rules apply after booting.
    ```bash
    $ chmod a+x /home/moxa/nftrules.sh
    $ chmod a+x /etc/rc.local
    $ cat>>/etc/systemd/system/rc-local.service<<EOF
    [Unit]
    Description=/etc/rc.local
    ConditionPathExists=/etc/rc.local

    [Service]
    Type=forking
    ExecStart=/etc/rc.local start
    TimeoutSec=0
    StandardOutput=tty
    RemainAfterExit=yes
    SysVStartPriority=99

    [Install]
    WantedBy=multi-user.target
    EOF
    $ systemctl daemon-reload
    $ systemctl enable rc-local
    ```