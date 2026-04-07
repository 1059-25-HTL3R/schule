# Remote Desktop Services (RDS)

## Guides

[Quick Setup Guide](https://woshub.com/deploy-remote-desktop-services-rds-farm-windows-server/)

[Microsoft RDS Book](Virtualizing%20Desktops%20and%20Apps%20with%20Windows%20Server%202012%20R2.pdf) (Ch. 8 - 12)

## Theory

### Standard RDS Deployment Architectures

[MS Learn](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) (Basic vs. HA)

### RDS Components

The RDS role in Windows Server includes the following components:

* **Remote Desktop Session Host (RDSH)** – RDS session hosts. These are the main workhorses of an RDS farm on which user apps run;
* **Remote Desktop Connection Broker (RDCB)** – an RDS connection broker. It is used to manage an RDS farm, distribute the workload, reconnect users to their sessions, store  RDS collection settings, and published RemoteApps;
* **Remote Desktop Gateway (RDGW)** – provides secure access to the RDS farm from the Internet;
* **RD Web Access (RDWA)** – a web interface to access remote desktops and RemoteApps;
* **Remote Desktop Licensing (RD Licensing)** – a licensing service for managing RDS licenses (CALs).
