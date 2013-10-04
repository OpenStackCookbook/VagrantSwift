Test Swift setup<br>
<br>
swift1 + keystone<br>
swift2 + keystone<br>
<br>
vagrant ssh swift1<br>
sudo -i<br>
. swiftrc<br>
swift stat<br>
<br>
Set up Container Sync<br>
<br>
swift -V2.0 -A http://swift2:5000/v2.0 -U service:swift -K swift post container2<br>
CONT2=$(swift -V2.0 -A http://swift2:5000/v2.0 -U service:swift -K swift stat | awk '/AUTH/ {print $2}')CONT1=$(swift stat | awk '/AUTH/ {print $2}')<br>
swift post -t "http://swift2:8080/v1/${CONT2}" -k 'secret' container1<br>
dd if=/dev/zero of=/tmp/example-10M bs=1M count=10<br>
swift upload container1 /tmp/example-10M<br>
swift -V2.0 -A http://swift2:5000/v2.0 -U service:swift -K swift stat container2<br>
