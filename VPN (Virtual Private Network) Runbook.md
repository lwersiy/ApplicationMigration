VPN (Virtual Private Network)

Step by Step guide to creating a VPN connection using Openswan

	1) Create a cloud base VPC in region A, create an onprem VPC in region B
	2) Provision instances in both VPCs with SSH and All ICMP ipv4 SG allowed.
	3) While on cloud VPC, create Customer GW (public ipv4 of onprem instance), VPG (attach to vpc), site to site VPN (onprem cidr).
	4) Download site to site VPN configuration with openswan.
	5) SSH into onprem instance and continue to follow instruction
	6) Sudo su
	7) yum install openswan -y
	8) vi /etc/sysctl.conf
	9) Copy 
	   net.ipv4.ip_forward = 1
	   net.ipv4.conf.default.rp_filter = 0
	   net.ipv4.conf.default.accept_source_route = 0
	And paste in vim, save changes and exit vim. Cat to double check
	10) Sysctl -p
	11) vi /etc/ipsec.conf      :q! if good
	12) vi /etc/ipsec.d/aws.conf
	13) Copy  and make changes to the highlighted
	conn Tunnel1
		authby=secret
		auto=start
		left=%defaultroute
		leftid=18.224.53.1
		right=3.138.234.19
		type=tunnel
		ikelifetime=8h
		keylife=1h
		phase2alg=aes128-sha1;modp1024
		ike=aes128-sha1;modp1024
		auth=esp delete this line
		keyingtries=%forever
		keyexchange=ike
		leftsubnet=<LOCAL NETWORK>   on prem cidr
		rightsubnet=<REMOTE NETWORK>  aws cloud vpc cidr
		dpddelay=10
		dpdtimeout=30
		dpdaction=restart_by_peer
	
	14) Paste the edited conn Tonnel1 and save and exit 
	15) Vi /etc/ipsec.d/aws.secrets
	16) Copy and paste the secrets (18.224.53.1 3.138.234.19: PSK "ChIZrcnb5kIspep4eub6iRq56ESCnb32")   then save and exit vim
	17) Run the following commands
	chkconfig ipsec on
	systemctl start ipsec
	systemctl status ipsec
	18) Go to aws console and make sure tunnel1 in VPN connection is up
	19) Enable route propagation in VPC (VPC-B-rt) 
	20) Check VPN site to site connection to make sure tunnel1 is UP.
	21) Ping your private ip and see if it works

Goodluck…
