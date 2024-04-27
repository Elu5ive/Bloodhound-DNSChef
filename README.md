# Bloodhound-DNSChef
Workaround for DNS timeout, applied to HTB's Forest

Fxied with dnschef:

```python
./dnschef.py --fakeip 10.10.10.161
```

bloodhoundpy command:

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

how to make them work on dnschef:

```python
_ldap._tcp.gc._msdcs.htb.local
#becomes
*.*.*._msdcs.htb.local=0 5 5060 dc1.htb.local
```

```python
[SRV]
; FORMAT: priority weight port target
*.*.thesprawl.org=0 5 5060 sipserver.fake.com
*.*.*._msdcs.htb.local=0 5 5060 dc1.htb.local
*.*.*.*.htb.local.localdomain=0 5 5060 dc1.htb.local
*.*.*.htb.local=0 5 5060 dc1.htb.local
```

then run command:

```python
./dnschef.py --fakeip 10.10.10.161 --file dnschef.ini
```

and bloodhound py it:

```python
sudo /home/kali/.local/pipx/venvs/crackmapexec/bin/bloodhound-python -d htb.local -u 'svc-alfresco' -p 's3rvice' -ns 127.0.0.1 -c all --zip
```

