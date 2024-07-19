searched for any exploit: https://github.com/Ridter/redis-rce
![[Pasted image 20240605185536.png]]

![[Pasted image 20240605185838.png]]
![[Pasted image 20240605185626.png]]

compiled .so file using: https://github.com/n0b0dyCN/redis-rogue-server
![[Pasted image 20240605190007.png]]
![[Pasted image 20240605190047.png]]

first try didn't work:
![[Pasted image 20240605190130.png]]
changed listening port: didn't work
![[Pasted image 20240605190200.png]]
then changed to port 80: worked..! 
but for reverse shell gave another port didn't work.
![[Pasted image 20240605190430.png]]
![[Pasted image 20240605190456.png]]

gave port 80 for reverse shell, worked.
![[Pasted image 20240604162001.png]]
![[Pasted image 20240604162038.png]]

![[Pasted image 20240604161936.png]]
![[Pasted image 20240604161913.png]]