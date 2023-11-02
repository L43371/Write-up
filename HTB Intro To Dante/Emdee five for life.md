### a webpage that we need to MD5 encode the text that was generated but there is a timer and by trying a manual encryption, it will not work. We will then create a python script to automate the encryption and send it over.

Python script

```
#!/bin/usr/python
import requests
import hashlib
import re

req = requests.session()
url = 'http://209.97.140.29:31298/'

### GET Rrequest

rget =  req.get(url)
html = rget.content # Save the GET content

### Strip HTML
def html_tags(html):
    clean = re.compile('<.*?>')
    return re.sub(clean, '', html)

### Split Random string

out1 = html_tags(html)
out2 = out1.split('string')[1]
out3 = out2.rstrip()

### MD5 Encrypt

mdHash = hashlib.md5(out3).hexdigest()


### Submit the encrypted hash

data = dict(hash=mdHash)
rpost = req.post(url=url, data=data)
print(rpost.text)
```

