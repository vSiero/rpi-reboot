# rpi-reboot
# Check if google gateway is pingable and if not - reboots the raspberri pi

root@siero-rpi:/home/pi# cat /etc/network/chkconnect.sh 
#!/bin/bash
LOG_FILE=/etc/network/siero.log
start=`/bin/date +%s`
iterations=25

function log {
    echo `/bin/date "+%F %T"` $1 >>${LOG_FILE}

}

#def_gw=$(route | grep default | awk {'print $2'})
#echo $def_gw

counter=1
success=0
failures=0

until [ $counter -gt $iterations ]
do
    command=$(/bin/ping -w 1 -c 1 8.8.8.8 | grep "received" | /etc/alternatives/awk {'print $4'})
    if [ $command = "1" ]
	then ((success++))
        else ((failures++))
    fi
    ((counter++))
done

#echo OK: $success
#echo FAIL: $failures
#fail=$(( $failures * 100 / $counter ))
#succ=$(( $success * 100 / $counter ))
#echo "percentage: OK:$succ% Fail:$fail%"

end=`/bin/date +%s`
runtime=$((end-start))
#echo "Script execution time $runtime sec"

if [ $failures -eq $iterations ]
then
    log "PING failed. failures: $failures. script execution time: $runtime"
    /sbin/reboot
else
    log "PING success. OK: $success. script execution time: $runtime"
fi
