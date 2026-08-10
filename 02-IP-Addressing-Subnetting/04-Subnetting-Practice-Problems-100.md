# 04 – Subnetting Practice Problems – 100 Questions

> Goal: Build strong subnetting skills for Networking, VAPT, SOC, Pentesting and interviews.
>
> Difficulty:
> 🟢 Beginner = Q1–25
> 🟡 Intermediate = Q26–50
> 🟠 Advanced = Q51–75
> 🔴 VAPT / Interview = Q76–100

---

# 📚 How to Use This File

For every subnetting question, try to find:

1. Network Address
2. Broadcast Address
3. First Usable IP
4. Last Usable IP
5. Number of Total Addresses
6. Number of Usable Hosts
7. Subnet Mask
8. CIDR Prefix
9. Number of Subnets (when applicable)

### Important Formula

Usable hosts:

`2^host_bits - 2`

Total addresses:

`2^host_bits`

---

# 🟢 Beginner Level — Q1 to Q25

## Q1
What is the subnet mask of `/24`?

A. 255.255.0.0  
B. 255.255.255.0  
C. 255.255.255.128  
D. 255.0.0.0

---

## Q2
How many total IPv4 addresses exist in a `/24` network?

A. 128  
B. 254  
C. 256  
D. 512

---

## Q3
How many usable host addresses are available in a normal `/24` network?

A. 252  
B. 254  
C. 256  
D. 255

---

## Q4
What is the subnet mask of `/25`?

A. 255.255.255.0  
B. 255.255.255.128  
C. 255.255.255.192  
D. 255.255.0.0

---

## Q5
How many total addresses exist in `/25`?

A. 64  
B. 126  
C. 128  
D. 256

---

## Q6
How many usable hosts exist in `/25`?

A. 126  
B. 127  
C. 128  
D. 124

---

## Q7
What is the subnet mask of `/26`?

A. 255.255.255.128  
B. 255.255.255.192  
C. 255.255.255.224  
D. 255.255.255.0

---

## Q8
How many total addresses are in `/26`?

A. 32  
B. 64  
C. 128  
D. 256

---

## Q9
How many usable hosts are in `/26`?

A. 62  
B. 64  
C. 60  
D. 63

---

## Q10
What is the subnet mask of `/27`?

A. 255.255.255.192  
B. 255.255.255.224  
C. 255.255.255.240  
D. 255.255.255.128

---

## Q11
How many total addresses are available in `/27`?

A. 16  
B. 30  
C. 32  
D. 64

---

## Q12
How many usable hosts are available in `/27`?

A. 30  
B. 31  
C. 32  
D. 28

---

## Q13
What is the subnet mask of `/28`?

A. 255.255.255.224  
B. 255.255.255.240  
C. 255.255.255.248  
D. 255.255.255.192

---

## Q14
How many usable hosts are available in `/28`?

A. 14  
B. 16  
C. 30  
D. 12

---

## Q15
What is the subnet mask of `/29`?

A. 255.255.255.240  
B. 255.255.255.248  
C. 255.255.255.252  
D. 255.255.255.224

---

## Q16
How many usable hosts does `/29` provide?

A. 6  
B. 8  
C. 14  
D. 4

---

## Q17
What is the subnet mask of `/30`?

A. 255.255.255.248  
B. 255.255.255.252  
C. 255.255.255.254  
D. 255.255.255.240

---

## Q18
How many usable hosts does `/30` provide?

A. 2  
B. 4  
C. 6  
D. 8

---

## Q19
Which address represents a `/24` network?

A. 192.168.1.0  
B. 192.168.1.1  
C. 192.168.1.255  
D. 192.168.1.254

---

## Q20
Which is normally the broadcast address of `192.168.1.0/24`?

A. 192.168.1.0  
B. 192.168.1.1  
C. 192.168.1.254  
D. 192.168.1.255

---

## Q21
For `192.168.1.0/24`, what is the first usable IP?

A. 192.168.1.0  
B. 192.168.1.1  
C. 192.168.1.254  
D. 192.168.1.255

---

## Q22
For `192.168.1.0/24`, what is the last usable IP?

A. 192.168.1.253  
B. 192.168.1.254  
C. 192.168.1.255  
D. 192.168.1.1

---

## Q23
Which CIDR provides exactly 14 usable hosts?

A. /27  
B. /28  
C. /29  
D. /30

---

## Q24
Which CIDR provides exactly 62 usable hosts?

A. /25  
B. /26  
C. /27  
D. /28

---

## Q25
Which CIDR provides exactly 126 usable hosts?

A. /24  
B. /25  
C. /26  
D. /23


# 🟡 Intermediate Level — Q26 to Q50

## Q26
Find the network address:

`192.168.10.25/24`

---

## Q27
Find the broadcast address:

`192.168.10.25/24`

---

## Q28
Find the first and last usable IP:

`192.168.10.25/24`

---

## Q29
Find the network address:

`192.168.10.130/25`

---

## Q30
Find the broadcast address:

`192.168.10.130/25`

---

## Q31
Find the network address:

`192.168.10.70/26`

---

## Q32
Find the broadcast address:

`192.168.10.70/26`

---

## Q33
Is `192.168.10.65/26` a network address?

---

## Q34
Is `192.168.10.63/26` a broadcast address?

---

## Q35
Find the network address:

`192.168.10.100/27`

---

## Q36
Find the broadcast address:

`192.168.10.100/27`

---

## Q37
Find the usable range:

`192.168.10.100/27`

---

## Q38
Find the network address:

`10.10.10.75/28`

---

## Q39
Find the broadcast address:

`10.10.10.75/28`

---

## Q40
Find the usable host range:

`10.10.10.75/28`

---

## Q41
Find the network address:

`172.16.5.200/29`

---

## Q42
Find the broadcast address:

`172.16.5.200/29`

---

## Q43
Find the usable range:

`172.16.5.200/29`

---

## Q44
Which subnet contains:

`192.168.1.75/26`?

---

## Q45
Which subnet contains:

`192.168.1.190/27`?

---

## Q46
Which subnet contains:

`10.0.0.130/25`?

---

## Q47
Which subnet contains:

`172.16.10.45/28`?

---

## Q48
Are these two hosts in the same `/24` subnet?

`192.168.1.20`

`192.168.1.200`

---

## Q49
Are these hosts in the same `/26` subnet?

`192.168.1.20`

`192.168.1.70`

---

## Q50
Are these hosts in the same `/27` subnet?

`192.168.1.33`

`192.168.1.60`


# 🟠 Advanced Level — Q51 to Q75

## Q51
You need a subnet supporting at least 50 hosts.

Which CIDR should you choose?

A. /27  
B. /26  
C. /25  
D. /28

---

## Q52
You need at least 100 usable hosts.

Which CIDR is suitable?

A. /26  
B. /25  
C. /27  
D. /28

---

## Q53
You need at least 500 usable hosts.

Which CIDR is suitable?

A. /22  
B. /23  
C. /24  
D. /25

---

## Q54
You need at least 1000 usable hosts.

Which CIDR is suitable?

A. /21  
B. /22  
C. /23  
D. /24

---

## Q55
Divide:

`192.168.1.0/24`

into 2 equal subnets.

Find both network addresses.

---

## Q56
Divide:

`192.168.1.0/24`

into 4 equal subnets.

Find all network addresses.

---

## Q57
Divide:

`192.168.1.0/24`

into 8 equal subnets.

Find all network addresses.

---

## Q58
What prefix is produced when `/24` is divided into 4 equal subnets?

---

## Q59
What prefix is produced when `/24` is divided into 8 equal subnets?

---

## Q60
How many `/26` subnets can be created from a `/24` network?

---

## Q61
How many `/27` subnets can be created from a `/24` network?

---

## Q62
How many `/28` subnets can be created from a `/24` network?

---

## Q63
What is the block size of `/26`?

---

## Q64
What is the block size of `/27`?

---

## Q65
What is the block size of `/28`?

---

## Q66
Find the subnet containing:

`192.168.50.145/28`

---

## Q67
Find the subnet containing:

`192.168.50.222/27`

---

## Q68
Find the subnet containing:

`10.20.30.75/26`

---

## Q69
Find the subnet containing:

`172.16.100.250/28`

---

## Q70
Find:

Network  
Broadcast  
First Host  
Last Host

For:

`192.168.100.75/26`

---

## Q71
Find:

Network  
Broadcast  
First Host  
Last Host

For:

`10.10.10.200/27`

---

## Q72
Find:

Network  
Broadcast  
First Host  
Last Host

For:

`172.16.20.150/28`

---

## Q73
Which subnet contains `192.168.1.200/29`?

---

## Q74
Which subnet contains `192.168.1.250/30`?

---

## Q75
Determine whether these two IPs belong to the same `/27` subnet:

`192.168.1.34`

`192.168.1.62`


# 🔴 VAPT / Interview Level — Q76 to Q100

## Q76
A company has:

`192.168.10.0/24`

You need:

- Web subnet: 100 hosts
- Internal subnet: 50 hosts
- Management subnet: 20 hosts

Design suitable subnet sizes.

---

## Q77
Why should a network be subnetted during a security assessment?

Give at least 3 reasons.

---

## Q78
A pentester discovers:

`10.10.10.64/26`

Find:

- Network
- Broadcast
- First Host
- Last Host
- Usable Hosts

---

## Q79
A server has:

`192.168.1.126/25`

Is it a valid usable host address?

Explain.

---

## Q80
Is this a valid host?

`192.168.1.127/25`

Explain why.

---

## Q81
Is this a valid host?

`192.168.1.128/25`

Explain why.

---

## Q82
You find:

`10.0.0.0/30`

How many usable hosts are available?

Why is `/30` commonly useful for point-to-point links?

---

## Q83
A network uses:

`172.16.0.0/16`

How many `/24` networks can be created?

---

## Q84
A network uses:

`192.168.0.0/16`

How many `/24` networks can be created?

---

## Q85
How many `/28` networks can be created from:

`192.168.1.0/24`?

---

## Q86
During reconnaissance you discover:

`192.168.10.10/24`

and

`192.168.20.10/24`

Are they in the same subnet?

---

## Q87
During VAPT you discover:

`10.10.1.10/16`

and

`10.20.1.10/16`

Are they in the same subnet?

---

## Q88
Two systems have:

`192.168.1.10/26`

`192.168.1.50/26`

Can they communicate directly at Layer 2 without routing?

Explain.

---

## Q89
Two systems have:

`192.168.1.10/26`

`192.168.1.70/26`

Are they in the same subnet?

---

## Q90
A pentester identifies:

`192.168.1.0/28`

How many addresses are available?

How many usable hosts?

---

## Q91
What is the security advantage of separating servers into different VLANs/subnets?

---

## Q92
A company needs 30 hosts per subnet.

Which prefix is the most efficient?

A. /24  
B. /25  
C. /27  
D. /28

---

## Q93
A company needs 14 hosts per subnet.

Which prefix is most efficient?

A. /26  
B. /27  
C. /28  
D. /29

---

## Q94
A company needs 6 hosts per subnet.

Which prefix is most efficient?

A. /28  
B. /29  
C. /30  
D. /27

---

## Q95
What is wrong with this assumption?

"192.168.1.1 is always the gateway."

---

## Q96
A pentester sees:

`192.168.1.0/24`

and scans:

`192.168.1.0`

Why might the result not represent a normal endpoint?

---

## Q97
A vulnerability scanner reports:

`10.10.10.0/24`

What does `/24` tell you?

---

## Q98
A VAPT report says:

> Target: 10.10.20.64/26

What range should the tester understand as the subnet boundary?

---

## Q99
Why is understanding subnetting important during network pentesting?

Mention at least 5 practical reasons.

---

## Q100 — FINAL CHALLENGE

You receive this network:

`10.10.0.0/24`

Requirements:

- DMZ → 60 hosts
- Internal → 50 hosts
- Security Tools → 30 hosts
- Management → 20 hosts
- Testing Lab → 10 hosts

Design the network using appropriate subnet sizes.

For each subnet provide:

1. Network address
2. CIDR
3. Subnet mask
4. Broadcast address
5. First usable IP
6. Last usable IP
7. Usable host count

---

# ✅ ANSWER KEY

## Q1–25

| Q | Answer |
|---|---|
| 1 | B — 255.255.255.0 |
| 2 | C — 256 |
| 3 | B — 254 |
| 4 | B — 255.255.255.128 |
| 5 | C — 128 |
| 6 | A — 126 |
| 7 | B — 255.255.255.192 |
| 8 | B — 64 |
| 9 | A — 62 |
| 10 | B — 255.255.255.224 |
| 11 | C — 32 |
| 12 | A — 30 |
| 13 | B — 255.255.255.240 |
| 14 | A — 14 |
| 15 | B — 255.255.255.248 |
| 16 | A — 6 |
| 17 | B — 255.255.255.252 |
| 18 | A — 2 |
| 19 | A |
| 20 | D |
| 21 | B |
| 22 | B |
| 23 | B — /28 |
| 24 | B — /26 |
| 25 | B — /25 |

---

# 🧠 Important Subnetting Table

| CIDR | Mask | Total | Usable | Block Size |
|---|---|---:|---:|---:|
| /24 | 255.255.255.0 | 256 | 254 | 256 |
| /25 | 255.255.255.128 | 128 | 126 | 128 |
| /26 | 255.255.255.192 | 64 | 62 | 64 |
| /27 | 255.255.255.224 | 32 | 30 | 32 |
| /28 | 255.255.255.240 | 16 | 14 | 16 |
| /29 | 255.255.255.248 | 8 | 6 | 8 |
| /30 | 255.255.255.252 | 4 | 2 | 4 |

---

# 🧠 Subnetting Shortcut

Remember:

```text
/24 → 256
/25 → 128
/26 → 64
/27 → 32
/28 → 16
/29 → 8
/30 → 4
