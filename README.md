## Test Container Sync Swift Setup
#### Supporting vagrant scripts
swift1 + keystone
swift2 + keystone
### Quick Start
vagrant ssh swift1
sudo -i
. swiftrc
swift stat
#### Set up Container Sync
```bash
. swiftrc

CONT2=$(swift -V2.0 -A http://swift2:5000/v2.0 -U cookbook:admin -K openstack stat | awk '/AUTH/ {print $2}')
CONT1=$(swift stat | awk '/AUTH/ {print $2}')

swift -V2.0 -A http://swift2:5000/v2.0 -U cookbook:admin -K openstack post -t "http://172.16.0.221:8080/v1/${CONT1}/container1" -k 'secret' container2
swift post -t "http://172.16.0.222:8080/v1/${CONT2}/container2" -k 'secret' container1

dd if=/dev/zero of=/tmp/example-10M bs=1M count=10
swift upload container1 /tmp/example-10M

swift -V2.0 -A http://swift2:5000/v2.0 -U cookbook:admin -K openstack stat container2
```
