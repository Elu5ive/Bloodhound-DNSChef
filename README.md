# Bloodhound-DNSChef
Workaround for DNS timeout, applied to HTB's Forest

in this case, the DC sits on 10.10.10.161


on the domain: htb.local


and we found a service account with the credentials:

Username: svc-alfresco

Password: s3rvice


Fixed with dnschef:

Run DNSChef to get the SRV you need to add to dnschef.ini:

```python
./dnschef.py --fakeip 10.10.10.161
```

bloodhound-python command:

```python
sudo /home/kali/.local/pipx/venvs/crackmapexec/bin/bloodhound-python -d htb.local -u 'svc-alfresco' -p 's3rvice' -ns 127.0.0.1 -c all --zip
```

Take the outputs from the dnschef tab and add them to:

```python
sudo mousepad dnschef.ini
```

under SRV (or whatever they are)

output:

```python
(17:31:42) [*] 127.0.0.1: proxying the response of type 'SRV' for _ldap._tcp.gc._msdcs.htb.local
(17:31:42) [*] 127.0.0.1: proxying the response of type 'SRV' for _ldap._tcp.gc._msdcs.htb.local.localdomain
(17:31:42) [*] 127.0.0.1: proxying the response of type 'SRV' for _kerberos._tcp.dc._msdcs.htb.local
```

condensed:

```python
_ldap._tcp.gc._msdcs.htb.local
_ldap._tcp.gc._msdcs.htb.local.localdomain
_kerberos._tcp.dc._msdcs.htb.local
```
NOTE: If you get multiple/duplicates for this output and are unsure, you can always add all of them, and DNSChef will tell you that you have duplicates that you should remove!*

how to make them work on dnschef:

```python
_ldap._tcp.gc._msdcs.htb.local
#becomes
*.*.*._msdcs.htb.local=0 5 5060 dc1.htb.local
```

dnschef.ini SRV section becomes:
```python
[SRV]
; FORMAT: priority weight port target
*.*.thesprawl.org=0 5 5060 sipserver.fake.com
*.*.*._msdcs.htb.local=0 5 5060 dc1.htb.local
*.*.*.*.htb.local.localdomain=0 5 5060 dc1.htb.local
*.*.*.htb.local=0 5 5060 dc1.htb.local
```

then run dnschef again:

*this is where DNSChef would tell you that you have duplicates that should be removed.

```python
./dnschef.py --fakeip 10.10.10.161 --file dnschef.ini
```

and bloodhound-python it to pull the data (and save them to a zip):

```python
sudo /home/kali/.local/pipx/venvs/crackmapexec/bin/bloodhound-python -d htb.local -u 'svc-alfresco' -p 's3rvice' -ns 127.0.0.1 -c all --zip
```
