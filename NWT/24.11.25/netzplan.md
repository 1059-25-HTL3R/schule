# Netzplan


## AS1

### AS-1-1

Border router zu AS2

| interface | IP-Address       |
| --------- | ---------------- |
| G0/0      | 172.168.12.1 /24 | ACHTUNG selbes netzt wie bei AS-1-5
| G0/1      | 10.0.12.1 / 24   |
| LO1       | 1.1.1.1 /32      |

### AS-1-2

MPLS ROUTER

| interface | IP-Address     |
| --------- | -------------- |
| G0/0      | 10.0.23.2 /24  |
| G0/1      | 10.0.12.2 / 24 |
| LO1       | 2.2.2.2 /32    |

### AS-1-3

MPLS ROUTER

| interface | IP-Address     |
| --------- | -------------- |
| G0/0      | 10.0.23.3 /24  |
| G0/2      | 10.0.34.3 / 24 |
| LO1       | 3.3.3.3 /32    |

### AS-1-4

MPLS Router

| interface | IP-Address     |
| --------- | -------------- |
| G0/1      | 10.0.45.4 /24  |
| G0/2      | 10.0.34.4 / 24 |
| LO1       | 4.4.4.4 /32    |

### AS-1-5

Border router zu AS2

| interface | IP-Address       |
| --------- | ---------------- |
| G0/0      | 172.168.12.1 /24 | ACHTUNG selbes netz wie bei AS-1-1
| G0/1      | 10.0.0.1 / 24    |
| LO1       | 1.1.1.1 /32      |