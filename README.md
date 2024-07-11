# python-task

```bash
! cat logparser.py
#!/usr/bin/env  python3



import re


regex_host = r'(?P<host>.*?)'
regex_identity = r'(?P<identity>\S+)'
regex_user = r'(?P<user>\S+)'
regex_time = r'\[(?P<time>.*?)\]'
regex_request = r'\"(?P<request>.*?)\"'
regex_status = r'(?P<status>\d{3})'
regex_size = r'(?P<size>\S+)'
regex_referer = r'\"(?P<referer>.*?)\"'
regex_agent = r'\"(?P<agent>.*?)\"'
regex_space = r'\s'

pattern = regex_host + regex_space + regex_identity + regex_space + \
          regex_user + regex_space + regex_time + regex_space + \
		  regex_request + regex_space + regex_status + regex_space + \
		  regex_size + regex_space + regex_referer + regex_space + \
		  regex_agent


def parser(s):
	"""
	return type : dict()
	return format: {
                       host:str , identity:str , user:str ,
					   time:str ,request:str , status:str ,
					   size:str , referer:str, agent:str
					}
	returns None if failed.
	"""
	try:
		parts = re.match(pattern,s)
		return parts.groupdict()
	except Exception as err:
		print(err)
```

## sample output

```bash
import logparser

log = open('access.log','r')

for logLine in log:
    
  print(logLine)
  print()
    
  print(logLine.split())
  print()
    
  print(logparser.parser(logLine))
  print()
    
  break
    
    
    
log.close()
157.48.153.185 - - [19/Dec/2020:14:08:08 +0100] "GET /favicon.ico HTTP/1.1" 404 217 "http://www.almhuette-raith.at/apache-log/access.log" "Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36" "-"


['157.48.153.185', '-', '-', '[19/Dec/2020:14:08:08', '+0100]', '"GET', '/favicon.ico', 'HTTP/1.1"', '404', '217', '"http://www.almhuette-raith.at/apache-log/access.log"', '"Mozilla/5.0', '(Windows', 'NT', '6.3;', 'Win64;', 'x64)', 'AppleWebKit/537.36', '(KHTML,', 'like', 'Gecko)', 'Chrome/87.0.4280.88', 'Safari/537.36"', '"-"']

{'host': '157.48.153.185', 'identity': '-', 'user': '-', 'time': '19/Dec/2020:14:08:08 +0100', 'request': 'GET /favicon.ico HTTP/1.1', 'status': '404', 'size': '217', 'referer': 'http://www.almhuette-raith.at/apache-log/access.log', 'agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36'}
```

## IP hits

```bash
import logparser

log = open('access.log','r')

ipCounter = {}


for logLine in log:
    
  logParts = logparser.parser(logLine)

  remoteIp = logParts['host']
    
  if remoteIp not in ipCounter:
    
    ipCounter[remoteIp] = 1
    
  else:
    
    ipCounter[remoteIp] = ipCounter[remoteIp] + 1
    

log.close()


for ip in ipCounter:
    
  hit = ipCounter[ip]

  if hit >= 500:
        
    print("{:20} - {}".format(ip,hit))
```
 
 ```bash
 **Output:**
 45.145.161.12        - 680
45.132.51.36         - 846
45.138.145.131       - 856
45.144.0.179         - 946
194.156.95.52        - 676
45.153.227.31        - 876
45.138.4.22          - 880
87.247.143.24        - 736
```

## Hit received per day

```bash
ts = "19/Dec/2020:14:08:08 +0100"

d =  ts[0:11]

print(d)
19/Dec/2020
import logparser

log = open('access.log','r')

dayCounter = {}


for logLine in log:
    
  logParts = logparser.parser(logLine)

  day = logParts['time'][0:11]
    
  if day not in dayCounter:
    
    dayCounter[day] = 1
    
  else:
    
    dayCounter[day] = dayCounter[day] + 1
    

log.close()


for day in dayCounter:
    
  hits = dayCounter[day]

  print("{} - {}".format(day,hits))
  
  ```
  
  ## Hit received per day with status 200
  
  ```bash
  import logparser

log = open('access.log','r')

dayCounter = {}


for logLine in log:
    
  logParts = logparser.parser(logLine)

  day = logParts['time'][0:11]
    
  status = logParts['status']  
  
  if status == '200':
    
    if day not in dayCounter:
    
      dayCounter[day] = 1
    
    else:
    
      dayCounter[day] = dayCounter[day] + 1
    

log.close()


for day in dayCounter:
    
  hits = dayCounter[day]

  print("{} - {}".format(day,hits))
  ```
  
  
 ## Print UID of each user under the /etc/passwd
 
 ```bash
 nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false
_uucp:*:4:4:Unix to Unix Copy Protocol:/var/spool/uucp:/usr/sbin/uucico
_taskgated:*:13:13:Task Gate Daemon:/var/empty:/usr/bin/false
_networkd:*:24:24:Network Services:/var/networkd:/usr/bin/false
_installassistant:*:25:25:Install Assistant:/var/empty:/usr/bin/false
_lp:*:26:26:Printing Services:/var/spool/cups:/usr/bin/false
_postfix:*:27:27:Postfix Mail Server:/var/spool/postfix:/usr/bin/false
_scsd:*:31:31:Service Configuration Service:/var/empty:/usr/bin/false
_ces:*:32:32:Certificate Enrollment Service:/var/empty:/usr/bin/false
_appstore:*:33:33:Mac App Store Service:/var/db/appstore:/usr/bin/false
 ```
 
 ```bash
 file = open('/etc/passwd','r')

def make_fields(line):
  
  fields = line.rstrip().split(":")

  return {"name":fields[0] , 
          "password":fields[1] , 
          "uid":fields[2] , 
          "gid":fields[3] , 
          "comment":fields[4] ,
          "home":fields[5],
          "shell": fields[6]
         }

for line in file:
    
  if not line.startswith("#"):
    
    fields = make_fields(line)
    
    
    if int(fields['uid']) >= 200 and int(fields['uid']) <= 250:
        
        print('{} - {}'.format(fields['uid'],fields['name']))
```

```bash
200 - _softwareupdate
202 - _coreaudiod
203 - _screensaver
205 - _locationd
208 - _trustevaluationagent
210 - _timezone
211 - _lda
```
