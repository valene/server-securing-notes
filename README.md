###Notes of simple server securing commands
===========================================

# The following are simple list of commands (for personal rememberance) on securing some of the VPS.

* Listing currently use ip-tables rule

    `iptables -L (or) iptables -S`

* Flush currently stored rules 

    iptables -F

* Location

    /etc/iptables/rules.v{4,6}  --depends on ip v4 or v6

* below is a basic set for creating a ssh and webserver 

    iptables -A INPUT -i lo -j ACCEPT
    iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    iptables -A INPUT -p tcp -m multiport --dports 80,443 -j ACCEPT
    --use port 110, 143 etc for mailservers
    iptables -A TCP -p tcp --dport 3306 -d 127.0.0.1 -j ACCEPT  --incase webserver needs to communicate with database server (mysql on default port)
    iptables -A INPUT -j DROP
    iptables-save 

* Additional details to remmeber

   -A INPUT is a rule for incoming connection blocks, 
   use -A OUTPUT to block outcoming connections (usually i trust users so no outgoing block)
   iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT -- eth1 is internal network eth0 is extenal interface)

* block a particular ip

   iptables -A INPUT -s 15.15.15.51 -j DROP

* full subnet rules (example ssh to and from only a particular subnet

   iptables -A INPUT -p tcp -s 192.168.0.0/24 --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
   iptables -A OUTPUT -p tcp --sport 22 -m conntrack --ctstate ESTABLISHED -j ACCEPT

* Don't forget fail2ban

     pacman || apt-get || yum || dnf || (whatever distro dependent package manager) fail2ban

* fail2ban  jail.local

     /etc/fail2ban/jail.local 

* service fail2ban (don't forget to start service and make sure it is running)

     systemctl start fail2ban.service  (or) whatever is the fail2ban unit name

* dependent on distros iptables-persistent 

     iptables-restore -t < /etc/iptables/rules.v4
     systemctl restart iptables-persistent (or) systemctl start iptables-persistent

