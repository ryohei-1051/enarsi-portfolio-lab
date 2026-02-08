Name	Interface	IP Address	subnet
Underlay (NBMA): 192.0.2.0/24			
HQ1	g0/0	192.0.2.1	255.255.255.0
ISP	g0/0	192.0.2.2	255.255.255.0
DC1	g0/0	192.0.2.3	255.255.255.0
BR1	g0/0	192.0.2.4	255.255.255.0
BR2	g0/0	192.0.2.5	255.255.255.0
		192.0.2.6-254	 - 
Overlay (Tunnel0): 10.10.10.0/24			
HQ1	Tunnel0	10.10.10.1	255.255.255.0
ISP	Tunnel0	10.10.10.2	255.255.255.0
DC1	Tunnel0	10.10.10.3	255.255.255.0
BR1	Tunnel0	10.10.10.4	255.255.255.0
BR2	Tunnel0	10.10.10.5	255.255.255.0
		10.10.10.6-254	 - 
Loopback (10.255.X.X)			
HQ1	lo0	10.255.1.1	255.255.255.255
ISP	lo0		255.255.255.255
DC1	lo0	10.255.3.3	255.255.255.255
BR1	lo0	10.255.4.4	255.255.255.255
BR2	lo0	10.255.5.5	255.255.255.255
	lo0	10.255.X.X	 - 
<img width="311" height="442" alt="image" src="https://github.com/user-attachments/assets/b41d9e7d-09bc-4da2-ab35-db88eec9db01" />

