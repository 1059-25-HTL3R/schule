# Netzplan


## AS1

### AS-1-1

Border router zu AS2

| interface | IP-Address       |
| --------- | ---------------- |
| G0/0      | 172.168.12.1 /24 | ACHTUNG selbes netzt wie bei AS-1-5 |
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
| G0/0      | 172.168.12.1 /24 | ACHTUNG selbes netz wie bei AS-1-1 |
| G0/1      | 10.0.45.5 /24    |
| LO1       | 5.5.5.5 /32      |



## AS2

### Sub-AS-2-1

### AS-2-1-1

Border Router zu AS1 und Sub-AS2-2

| interface | IP-Address       |
| --------- | ---------------- |
| G0/0      | 172.168.12.2 /24 |
| G0/1      | 172.168.67.6 /24 |
| G0/2      | 10.0.67.6 /24    |
| LO1       | 6.6.6.6 /32      |

### AS-2-1-2

Border Router zu AS1 und Sub-AS2-2

| interface | IP-Address       |
| --------- | ---------------- |
| G0/0      | 172.168.12.2 /24 |
| G0/1      | 172.168.67.6 /24 |
| G0/2      | 10.0.67.7 /24    |
| LO1       | 7.7.7.7 /32      |


### AS-2-2-1

Border Router zu Sub-AS2-1 und AS3

| interface | IP-Address       |
| --------- | ---------------- |
| G0/0      | 172.168.23.2 /24 |
| G0/1      | 172.168.67.7 /24 |
| G0/2      | 10.0.67.6 /24    |
| LO1       | 8.8.8.8 /32      |


### AS-2-2-2

Border Router zu Sub-AS2-1

| interface | IP-Address       |
| --------- | ---------------- |
| G0/1      | 172.168.67.7 /24 |
| G0/2      | 10.0.67.7 /24    |
| LO1       | 9.9.9.9 /32      |