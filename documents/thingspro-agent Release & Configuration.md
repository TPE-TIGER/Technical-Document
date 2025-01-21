# thingspro-agent Release & Configuration

  Document Version: 1.0

  ### Change Log

  | Version | Date       | Content         |
  | ------- | ---------- | --------------- |
  | 1.0     | 2024-06-17 | Initial version for AIG-302 v1.0 |

  ### AIG-302

  | AIG Version | IoT Edge Version | thingspro-agent version | Module URL                           | Create Option                                                |
  | ----------- | ---------------- | ----------------------- | ------------------------------------ | ------------------------------------------------------------ |
  | 1.0         | 1.4.20           | 3.0.1                   | moxa2019/thingspro-agent:3.0.1-armhf | `{"HostConfig": {"Binds": ["/var/thingspro/data/azureiotedge/:/var/thingspro/cloud/setting/", "/run/tpe/azureiotedge/:/run/tpe/azureiotedge/", "/var/thingspro/data/:/var/thingspro/data/"]}}` |

