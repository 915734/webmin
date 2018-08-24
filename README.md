########################### Configure Webmin with bind9 ###########################
# Update repo
        apt-get update
# Install webmin dependency on ubuntu
        apt-get install perl libnet-ssleay-perl openssl libauthen-pam-perl libpam-runtime libio-pty-perl apt-show-versions python
# Clone webmin & unzip webmin
# Install Webmin
        dpkg --install webmin_1.840_all.deb
# Install bind9 using webmin control plane https://host-ip-address:10000

########################### Configure Samba Domain Conterller ###########################
# Install Samba server dependency
        apt-get install attr build-essential libacl1-dev libattr1-dev libblkid-dev libgnutls-dev libreadline-dev python-dev libpam0g-dev python-dnspython gdb pkg-config libpopt-dev libldap2-dev dnsutils libbsd-dev attr krb5-user docbook-xsl libcups2-dev acl ntp ntpdate winbind
# Install Samba Package
        apt-get install samba smbclient
# Make sure IP address
# Make sure Hostname
# Make sure Domain Nme in resolve.conf
# Remove Samba Conf file
        rm -rf /etc/samba/smb.conf
# Verify database version using bellow command and make sure that database version is enable in this file. ->  /var/lib/samba/private/named.conf
        named -V
# Add bellow line -> /etc/bind/named.conf.options
        listen-on-v6 { any; };
        auth-nxdomain yes;
        empty-zones-enable no;
        tkey-gssapi-keytab "/var/lib/samba/private/dns.keytab";
# Add bellow line end of file -> /etc/apparmor.d/usr.sbin.named
  /usr/lib/x86_64-linux-gnu/ldb/** rwmk,
  /usr/lib/x86_64-linux-gnu/samba/** rwmk,
  /var/lib/samba/private/dns/** rwmk,
  /var/lib/samba/private/named.conf r,
  /var/lib/samba/private/dns.keytab r,
  /var/tmp/* rw,
  /dev/urandom rw,
# Add bellow line -> /etc/bind/named.conf
        include "/var/lib/samba/private/named.conf";
# Configure Samba Domain Contoller Using below command
samba-tool domain provision --use-rfc2307 --interactive
-> Using DNS backend:- BIND9_DLZ
# Verify bellow configure in this file "/etc/samba/smb.conf" if any configuration missing so add menualy
[global]
        workgroup = PRASHANT
        realm = PRASHANT.COM
        netbios name = SRV
        server role = active directory domain controller
        server services = s3fs, rpc, nbt, wrepl, ldap, cldap, kdc, drepl, winbindd, ntp_signd, kcc, dnsupdate
        idmap_ldb:use rfc2307 = yes

        ldap server require strong auth = no

[netlogon]
        path = /var/lib/samba/sysvol/prashant.com/scripts
        read only = No

[sysvol]
        path = /var/lib/samba/sysvol
        read only = No
        allow dns updates = nonsecure and secure
        dns forwarder = 10.0.0.11
        printing = CUPS
        printcap name = /dev/null

########################### Configure ntp server ###########################
# Remove existing time location
        rm -rf /etc/localtime
# Create soft link with your country time zone
        ln -s /usr/share/zoneinfo/Asia/Calcutta /etc/localtime
# set timezome
        timedatectl set-timezone "Asia/Kolkata"
