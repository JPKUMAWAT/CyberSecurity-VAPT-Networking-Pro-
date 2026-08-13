

# 0. Formula Sheet

### IPv4

```text
IPv4 = 32 bits
```

### Host bits

```text
Host bits = 32 - CIDR
```

### Total addresses

```text
Total = 2^(host bits)
```

### Traditional usable hosts

```text
Usable = 2^(host bits) - 2
```

> Note: `/31` and `/32` are special cases and should not be treated using the normal `-2` rule.

### Number of equal subnets

```text
Subnets = 2^(borrowed bits)
```

### Borrowed bits

```text
New prefix - Original prefix
```

### Block size

```text
Block Size = 256 - relevant subnet-mask octet
```

---

# Part A — Beginner Level 🟢

## Questions 1–20

### Q1

How many bits are in an IPv4 address?

A. 16
B. 32
C. 64
D. 128

**Answer:** B

---

### Q2

How many bits are in each IPv4 octet?

A. 4
B. 8
C. 16
D. 32

**Answer:** B

---

### Q3

How many total addresses are in a `/24` network?

A. 24
B. 64
C. 128
D. 256

**Answer:** D

---

### Q4

How many host bits exist in `/24`?

A. 4
B. 8
C. 16
D. 24

**Answer:** B

---

### Q5

What is the subnet mask for `/24`?

A. 255.255.0.0
B. 255.255.255.0
C. 255.255.255.128
D. 255.255.255.192

**Answer:** B

---

### Q6

What is the subnet mask for `/25`?

A. 255.255.255.0
B. 255.255.255.128
C. 255.255.255.192
D. 255.255.255.224

**Answer:** B

---

### Q7

What is the subnet mask for `/26`?

A. 255.255.255.128
B. 255.255.255.192
C. 255.255.255.224
D. 255.255.255.240

**Answer:** B

---

### Q8

What is the subnet mask for `/27`?

A. 255.255.255.192
B. 255.255.255.224
C. 255.255.255.240
D. 255.255.255.248

**Answer:** B

---

### Q9

What is the subnet mask for `/28`?

A. 255.255.255.224
B. 255.255.255.240
C. 255.255.255.248
D. 255.255.255.252

**Answer:** B

---

### Q10

How many total addresses are in `/25`?

A. 64
B. 128
C. 256
D. 512

**Answer:** B

---

### Q11

How many traditional usable host addresses are in `/25`?

A. 124
B. 126
C. 128
D. 130

**Answer:** B

---

### Q12

How many total addresses are in `/26`?

A. 32
B. 64
C. 128
D. 256

**Answer:** B

---

### Q13

How many traditional usable hosts are in `/26`?

A. 60
B. 62
C. 64
D. 66

**Answer:** B

---

### Q14

How many total addresses are in `/27`?

A. 16
B. 32
C. 64
D. 128

**Answer:** B

---

### Q15

How many traditional usable hosts are in `/27`?

A. 28
B. 30
C. 32
D. 34

**Answer:** B

---

### Q16

How many total addresses are in `/28`?

A. 8
B. 16
C. 32
D. 64

**Answer:** B

---

### Q17

How many traditional usable hosts are in `/28`?

A. 12
B. 14
C. 16
D. 18

**Answer:** B

---

### Q18

What is the block size of `/26`?

A. 32
B. 64
C. 128
D. 192

**Answer:** B

---

### Q19

What is the block size of `/27`?

A. 16
B. 32
C. 64
D. 128

**Answer:** B

---

### Q20

What is the block size of `/28`?

A. 8
B. 16
C. 32
D. 64

**Answer:** B

---

# Part B — Network & Broadcast Address 🟢

## Questions 21–40

### Q21

Find the network address:

```text
192.168.1.70/26
```

A. 192.168.1.0
B. 192.168.1.64
C. 192.168.1.70
D. 192.168.1.128

**Answer:** B

---

### Q22

For:

```text
192.168.1.70/26
```

What is the broadcast address?

A. 192.168.1.63
B. 192.168.1.64
C. 192.168.1.127
D. 192.168.1.128

**Answer:** C

---

### Q23

Find the first usable host:

```text
192.168.1.70/26
```

A. 192.168.1.64
B. 192.168.1.65
C. 192.168.1.70
D. 192.168.1.71

**Answer:** B

---

### Q24

Find the last usable host:

```text
192.168.1.70/26
```

A. 192.168.1.125
B. 192.168.1.126
C. 192.168.1.127
D. 192.168.1.128

**Answer:** B

---

### Q25

Find the network address:

```text
192.168.10.100/27
```

A. 192.168.10.64
B. 192.168.10.96
C. 192.168.10.100
D. 192.168.10.128

**Answer:** B

---

### Q26

Broadcast address for:

```text
192.168.10.100/27
```

A. 192.168.10.95
B. 192.168.10.96
C. 192.168.10.127
D. 192.168.10.128

**Answer:** C

---

### Q27

First usable address for:

```text
192.168.10.100/27
```

A. .95
B. .96
C. .97
D. .100

**Answer:** C

---

### Q28

Last usable address for:

```text
192.168.10.100/27
```

A. .126
B. .127
C. .128
D. .129

**Answer:** A

---

### Q29

Network address:

```text
10.10.10.130/25
```

A. 10.10.10.0
B. 10.10.10.64
C. 10.10.10.128
D. 10.10.10.130

**Answer:** C

---

### Q30

Broadcast address:

```text
10.10.10.130/25
```

A. 10.10.10.127
B. 10.10.10.128
C. 10.10.10.254
D. 10.10.10.255

**Answer:** D

---

### Q31

Network address:

```text
172.16.5.200/28
```

A. 172.16.5.192
B. 172.16.5.200
C. 172.16.5.208
D. 172.16.5.224

**Answer:** A

---

### Q32

Broadcast address:

```text
172.16.5.200/28
```

A. .191
B. .207
C. .208
D. .223

**Answer:** B

---

### Q33

Network address:

```text
192.168.50.37/29
```

A. .32
B. .37
C. .38
D. .40

**Answer:** A

---

### Q34

Broadcast address:

```text
192.168.50.37/29
```

A. .37
B. .38
C. .39
D. .40

**Answer:** C

---

### Q35

Network address:

```text
192.168.100.145/28
```

A. .128
B. .144
C. .145
D. .160

**Answer:** B

---

### Q36

Broadcast address:

```text
192.168.100.145/28
```

A. .143
B. .144
C. .159
D. .160

**Answer:** C

---

### Q37

Network address:

```text
10.20.30.55/27
```

A. .0
B. .32
C. .55
D. .64

**Answer:** B

---

### Q38

Broadcast address:

```text
10.20.30.55/27
```

A. .31
B. .32
C. .63
D. .64

**Answer:** C

---

### Q39

Network address:

```text
172.16.10.250/30
```

A. .248
B. .249
C. .250
D. .252

**Answer:** A

---

### Q40

Broadcast address:

```text
172.16.10.250/30
```

A. .248
B. .249
C. .251
D. .251?

````

**Answer:** C

> `/30` has blocks of 4: `248–251`.

---

# Part C — Equal Subnetting 🟡

## Questions 41–60

### Q41

`192.168.1.0/24` is divided into 2 equal subnets.

What prefix is required?

A. /24  
B. /25  
C. /26  
D. /27

**Answer:** B

---

### Q42

`192.168.1.0/24` divided into 4 equal subnets:

A. /25  
B. /26  
C. /27  
D. /28

**Answer:** B

---

### Q43

`192.168.1.0/24` divided into 8 equal subnets:

A. /26  
B. /27  
C. /28  
D. /29

**Answer:** B

---

### Q44

`192.168.1.0/24` divided into 16 equal subnets:

A. /26  
B. /27  
C. /28  
D. /29

**Answer:** C

---

### Q45

How many `/26` subnets can be created from `/24`?

A. 2  
B. 4  
C. 8  
D. 16

**Answer:** B

---

### Q46

How many `/27` subnets can be created from `/24`?

A. 2  
B. 4  
C. 8  
D. 16

**Answer:** C

---

### Q47

How many `/28` subnets can be created from `/24`?

A. 4  
B. 8  
C. 16  
D. 32

**Answer:** C

---

### Q48

How many usable hosts does each `/26` subnet traditionally provide?

A. 30  
B. 62  
C. 64  
D. 126

**Answer:** B

---

### Q49

How many usable hosts does each `/27` subnet traditionally provide?

A. 14  
B. 30  
C. 32  
D. 62

**Answer:** B

---

### Q50

How many usable hosts does each `/28` subnet traditionally provide?

A. 6  
B. 14  
C. 16  
D. 30

**Answer:** B

---

### Q51

Which networks are created from:

```text
192.168.1.0/24 → /26
````

A.

```text
.0, .32, .64, .96
```

B.

```text
.0, .64, .128, .192
```

C.

```text
.0, .128
```

D.

```text
.1, .65, .129, .193
```

**Answer:** B

---

### Q52

Which networks are created from:

```text
192.168.1.0/24 → /27
```

A. 0,32,64,96,128,160,192,224
B. 0,64,128,192
C. 0,16,32,48
D. 1,33,65,97

**Answer:** A

---

### Q53

How many host bits are available in `/28`?

A. 2
B. 4
C. 8
D. 28

**Answer:** B

---

### Q54

How many host bits are available in `/30`?

A. 2
B. 4
C. 6
D. 30

**Answer:** A

---

### Q55

How many total addresses exist in `/30`?

A. 2
B. 4
C. 8
D. 16

**Answer:** B

---

### Q56

How many traditional usable hosts exist in `/30`?

A. 1
B. 2
C. 4
D. 6

**Answer:** B

---

### Q57

A subnet needs approximately 20 hosts. Which is the smallest traditional IPv4 subnet from these options?

A. /27
B. /28
C. /29
D. /30

**Answer:** A

---

### Q58

A subnet needs approximately 50 hosts. Which is appropriate?

A. /27
B. /26
C. /28
D. /29

**Answer:** B

---

### Q59

A subnet needs approximately 100 hosts.

A. /26
B. /25
C. /27
D. /28

**Answer:** B

---

### Q60

A subnet needs approximately 200 hosts.

A. /24
B. /25
C. /26
D. /27

**Answer:** A

---

# Part D — Intermediate Problems 🟡

## Questions 61–80

### Q61

Find all information for:

```text
192.168.20.75/26
```

**Answer:**

```text
Network:    192.168.20.64
First host: 192.168.20.65
Last host:  192.168.20.126
Broadcast:  192.168.20.127
```

---

### Q62

Find all information:

```text
10.10.10.200/27
```

**Answer:**

```text
Network:    10.10.10.192
First host: 10.10.10.193
Last host:  10.10.10.222
Broadcast:  10.10.10.223
```

---

### Q63

Find all information:

```text
172.16.100.33/28
```

**Answer:**

```text
Network:    172.16.100.32
First host: 172.16.100.33
Last host:  172.16.100.46
Broadcast:  172.16.100.47
```

---

### Q64

Find all information:

```text
192.168.10.150/29
```

**Answer:**

```text
Network:    192.168.10.144
First host: 192.168.10.145
Last host:  192.168.10.150
Broadcast:  192.168.10.151
```

---

### Q65

Find all information:

```text
10.0.0.17/30
```

**Answer:**

```text
Network:    10.0.0.16
First host: 10.0.0.17
Last host:  10.0.0.18
Broadcast:  10.0.0.19
```

---

### Q66

Find all information:

```text
192.168.5.130/25
```

**Answer:**

```text
Network:    192.168.5.128
First host: 192.168.5.129
Last host:  192.168.5.254
Broadcast:  192.168.5.255
```

---

### Q67

What is the block size of:

```text
255.255.255.248
```

**Answer:**

```text
256 - 248 = 8
```

---

### Q68

What is the CIDR prefix for:

```text
255.255.255.240
```

**Answer:**

```text
/28
```

---

### Q69

What is the CIDR prefix for:

```text
255.255.255.192
```

**Answer:**

```text
/26
```

---

### Q70

What is the CIDR prefix for:

```text
255.255.255.224
```

**Answer:**

```text
/27
```

---

### Q71

How many `/28` subnets can fit inside `/24`?

**Answer:**

```text
28 - 24 = 4 borrowed bits

2^4 = 16
```

**16 subnets**

---

### Q72

How many `/29` subnets fit inside `/24`?

**Answer:**

```text
29 - 24 = 5

2^5 = 32
```

**32 subnets**

---

### Q73

How many traditional usable hosts are available in a `/29`?

**Answer:**

```text
2^(32-29) - 2
= 2^3 - 2
= 8 - 2
= 6
```

**6 hosts**

---

### Q74

How many traditional usable hosts are available in `/25`?

**Answer:**

```text
2^7 - 2
= 128 - 2
= 126
```

**126 hosts**

---

### Q75

Which subnet contains:

```text
192.168.100.115/28
```

**Answer:**

```text
Block = 16

Ranges:
96–111
112–127
128–143
```

Therefore:

```text
Network = 192.168.100.112
Broadcast = 192.168.100.127
```

---

### Q76

Which subnet contains:

```text
10.1.1.190/27
```

**Answer:**

```text
Block = 32

160–191
```

Therefore:

```text
Network = 10.1.1.160
Broadcast = 10.1.1.191
```

---

### Q77

Which subnet contains:

```text
172.16.4.215/29
```

**Answer:**

```text
Block = 8

208–215
```

Therefore:

```text
Network = 172.16.4.208
Broadcast = 172.16.4.215
```

---

### Q78

Is this address a host address?

```text
192.168.1.64/26
```

**Answer:** No.

It is the **network address**.

---

### Q79

Is this address a host address?

```text
192.168.1.127/26
```

**Answer:** No.

It is the **broadcast address**.

---

### Q80

Is this a usable traditional host address?

```text
192.168.1.100/26
```

**Answer:** Yes.

It falls inside:

```text
192.168.1.65 – 192.168.1.126
```

---

# Part E — VLSM & Advanced Problems 🔴

## Questions 81–100

### Q81

You have:

```text
192.168.50.0/24
```

Need a subnet for approximately 100 hosts.

Which prefix?

**Answer:**

```text
/25
```

Traditional usable hosts:

```text
126
```

---

### Q82

From the same `/24`, you need approximately 50 hosts.

Which prefix?

**Answer:**

```text
/26
```

Usable:

```text
62
```

---

### Q83

Need approximately 20 hosts.

Which prefix?

**Answer:**

```text
/27
```

Usable:

```text
30
```

---

### Q84

Need approximately 10 hosts.

Which prefix?

**Answer:**

```text
/28
```

Usable:

```text
14
```

---

### Q85 — VLSM

Network:

```text
192.168.50.0/24
```

Requirements:

```text
A = 100 hosts
B = 50 hosts
C = 20 hosts
D = 10 hosts
```

Which should be allocated first?

A. D
B. C
C. B
D. A

**Answer:** D

> In VLSM, normally allocate the largest requirement first.

---

### Q86

For the previous question, what subnet should A receive?

A. /27
B. /26
C. /25
D. /28

**Answer:** C

---

### Q87

After allocating:

```text
192.168.50.0/25
```

where does the next available block begin?

A. .64
B. .100
C. .128
D. .192

**Answer:** C

---

### Q88

Allocate 50 hosts after:

```text
192.168.50.0/25
```

Which subnet can be used?

A. 192.168.50.64/26
B. 192.168.50.128/26
C. 192.168.50.192/26
D. 192.168.50.224/27

**Answer:** B

---

### Q89

After allocating:

```text
192.168.50.128/26
```

where does the next free address space begin?

A. .160
B. .192
C. .224
D. .255

**Answer:** B

---

### Q90

A 20-host subnet is needed after:

```text
192.168.50.0/25
192.168.50.128/26
```

Which is appropriate?

A. 192.168.50.192/27
B. 192.168.50.224/28
C. 192.168.50.240/29
D. 192.168.50.128/27

**Answer:** A

---

### Q91

A 10-host subnet is needed after the previous allocations.

Which is appropriate?

A. 192.168.50.192/27
B. 192.168.50.224/28
C. 192.168.50.240/29
D. 192.168.50.128/26

**Answer:** B

---

### Q92 — Complete VLSM Design

Design:

```text
192.168.50.0/24
```

for:

```text
Engineering → 100
HR           → 50
Security     → 20
Management   → 10
```

**Answer:**

```text
Engineering
192.168.50.0/25

HR
192.168.50.128/26

Security
192.168.50.192/27

Management
192.168.50.224/28
```

Remaining:

```text
192.168.50.240 – 192.168.50.255
```

---

### Q93

Why should VLSM allocation normally start with the largest network?

A. To encrypt traffic
B. To reduce fragmentation/wasted address space
C. To increase bandwidth
D. To change MAC addresses

**Answer:** B

---

### Q94

A `/24` network must be divided into at least 10 equal subnets.

What is the smallest suitable prefix?

A. /26
B. /27
C. /28
D. /29

**Answer:** C

Explanation:

```text
/27 → 8 subnets
/28 → 16 subnets
```

---

### Q95

A `/24` network must support at least 25 hosts per subnet.

Smallest suitable traditional subnet?

A. /27
B. /28
C. /29
D. /30

**Answer:** A

---

### Q96 — VAPT Scenario

During an authorized internal assessment, you discover:

```text
10.10.10.0/24 → User network
10.10.20.0/24 → Server network
```

Why is understanding these subnets useful?

A. It tells you the Wi-Fi password
B. It helps understand network segmentation and scope
C. It automatically bypasses the firewall
D. It decrypts HTTPS

**Answer:** B

---

### Q97 — Security Scenario

A company has:

```text
Guest VLAN → Internal Server VLAN
```

and the firewall incorrectly permits unrestricted traffic between them.

What security issue should be investigated?

A. Network segmentation/control weakness
B. DNS caching only
C. CPU overclocking
D. File compression

**Answer:** A

---

### Q98 — Routing Scenario

A router has:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

Which route is most specific for:

```text
10.10.10.50
```

A. /8
B. /16
C. /24
D. All are equally specific

**Answer:** C

> This introduces the concept of **Longest Prefix Match**.

---

### Q99 — Advanced Concept

What is route summarization?

A. Combining multiple networks into a larger summarized prefix
B. Encrypting routing traffic
C. Scanning every port
D. Converting IPv4 into MAC addresses

**Answer:** A

---

### Q100 — Final Challenge 🔥

You receive:

```text
10.20.30.142/27
```

Calculate:

```text
1. Subnet mask
2. Block size
3. Network address
4. First host
5. Last host
6. Broadcast
7. Total addresses
8. Traditional usable hosts
```

### Solution

#### 1. Subnet mask

```text
/27

255.255.255.224
```

#### 2. Block size

```text
256 - 224 = 32
```

#### 3. Find the range

```text
0
32
64
96
128
160
192
224
```

`142` falls between:

```text
128–159
```

#### 4. Network

```text
10.20.30.128
```

#### 5. First host

```text
10.20.30.129
```

#### 6. Last host

```text
10.20.30.158
```

#### 7. Broadcast

```text
10.20.30.159
```

#### 8. Total addresses

```text
2^(32-27)
= 2^5
= 32
```

#### 9. Traditional usable hosts

```text
32 - 2
= 30
```

### Final Answer

```text
Network:     10.20.30.128
First Host:  10.20.30.129
Last Host:   10.20.30.158
Broadcast:   10.20.30.159
Total IPs:   32
Usable:      30
Mask:        255.255.255.224
```

---

# 🧠 Super-Fast Subnetting Method

When you see:

```text
192.168.10.77/26
```

Don't panic.

Use these **5 steps**:

### Step 1 — CIDR → Mask

```text
/26
↓
255.255.255.192
```

### Step 2 — Block size

```text
256 - 192 = 64
```

### Step 3 — Write boundaries

```text
0, 64, 128, 192
```

### Step 4 — Find where 77 belongs

```text
64–127
```

### Step 5 — Calculate

```text
Network    = 64
First Host = 65
Last Host  = 126
Broadcast  = 127
```

So:

```text
Network    → 192.168.10.64
First Host → 192.168.10.65
Last Host  → 192.168.10.126
Broadcast  → 192.168.10.127
```

---

# 🔥 Must-Memorize Table

| CIDR | Mask            | Block | Total | Traditional Usable |
| ---- | --------------- | ----: | ----: | -----------------: |
| /24  | 255.255.255.0   |   256 |   256 |                254 |
| /25  | 255.255.255.128 |   128 |   128 |                126 |
| /26  | 255.255.255.192 |    64 |    64 |                 62 |
| /27  | 255.255.255.224 |    32 |    32 |                 30 |
| /28  | 255.255.255.240 |    16 |    16 |                 14 |
| /29  | 255.255.255.248 |     8 |     8 |                  6 |
| /30  | 255.255.255.252 |     4 |     4 |                  2 |

---

# 🧪 Recommended Practical Labs

## Lab 1 — Manual Subnetting

Solve these without a calculator:

```text
192.168.1.50/26
192.168.1.100/27
192.168.1.145/28
192.168.1.200/29
```

Find:

```text
Network
First host
Last host
Broadcast
```

---

## Lab 2 — Network Observation

On your own Kali VM:

```bash
ip addr
ip route
```

On Windows:

```powershell
ipconfig /all
route print
```

Identify your lab machine's:

```text
IP address
Prefix/subnet mask
Network
Default gateway
```

---

## Lab 3 — VLSM

Design:

```text
192.168.100.0/24
```

Requirements:

```text
Servers     → 60 hosts
Employees   → 30 hosts
Security    → 15 hosts
Management  → 6 hosts
```

Try to create the smallest efficient allocation.

---

# 🎯 Final Mental Model

Don't memorize subnetting as random numbers.

Think:

```text
CIDR
 ↓
Network bits + Host bits
 ↓
Host bits
 ↓
Total addresses
 ↓
Block size
 ↓
Subnet boundaries
 ↓
Network address
 ↓
Usable host range
 ↓
Broadcast address
```

And for VAPT:

```text
IP Range
   ↓
Subnet
   ↓
Network Boundary
   ↓
Routing / ACL / Firewall
   ↓
Reachable Hosts
   ↓
Open Ports
   ↓
Services
   ↓
Attack Surface
```

