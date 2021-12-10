# VPN
Setup and install guides.

## Openvpn client configuration generator (gvcf)
### Usage
```
Usage: /usr/local/bin/gvcf.php <-n client_name><-c customer_name>[-hvOVSDUR][-t type][-D client_description]
 [-d device_vpn_ip_pool_network][-u user_vpn_ip_pool_network][-I device_ssh_ip][-P device_ssh_port]
 [-N device_ssh_username][-X device_ssh_password]
  Where:
    h = Show help
    v = Verbose
    O = Overwrite existing configs/cert/key
    V = Do not create client VPN config
    S = Do not create client specific config
    D = Do not add client to DB
    t = Type (device|user). Default to device
    n = Client common name
    D = Client description
    c = Customer name
    d = Device VPN IP pool network. Default to 10.8.1
    u = User VPN IP pool network. Defalt 10.8.200
    U = Upload VPN client configuration into device
    R = Restart device VPN client service
    a = VPN Server IP address
    p = VPN Server Port
    I = Device SSH IP address
    P = Device SSH Port. Default to 22
    N = Device SSH Username. Default to pi
    X = Device SSH Password.
    C = Check customer device certificate expiry.
    x = Generate and update device config with expired certificate.
    m = Certificate due to expire in n days. Default to 1.
    L = Check from list of online devices.
    F = Check from list of devices in a CSV file.
    B = Bulk generate and install device configs from a CSV file.
    l = List of devices in a CSV file for bulk update/install.
    G = When used with bulk (B), generate and install otherwise no changes.
```
### Generating a device .ovpn configuration file
Generate the OpenVPN configuration file for the device with the assigned name device123 of customer with the name cba. A client-customer mapping will be created. The generated configuration file will not be uploaded or installed to the device. The location or path of the generated configuration file and the device assigned VPN IP address is shown in the output.

Command:
```
/usr/local/bin/gvcf.php -c cba -n device123
```

Output:
```
Starting /usr/local/bin/gvcf.php
- Generating client VPN configuration.
-- Generated: /etc/openvpn-cba/client/configs/devices/device123.ovpn
- Generating client specific configuration.
-- Start VPN IP Pool: 10.8.1
-- End VPN IP Pool: 10.8.200
-- Clients found: 21
-- Last assigned IP: [10.8.1.81]
-- Next Available IP: [10.8.1.85]
- Adding client/customer mapping to database.
-- Client(device) 'device123' added under Customer 'cba'.
```
### Generating a device .ovpn configuration file and uploading into the device
Generate the device OpenVPN configuration file and upload to device device123. The device VPN IP address is shown in the output.

Command:
```
/usr/local/bin/gvcf.php -c cba -n device123 -U -R -I 10.8.1.69 -N pi -X PASSWORD
```
Output:
```
Starting /usr/local/bin/gvcf.php
- Generating client VPN configuration.
-- Generated: /etc/openvpn-cba/client/configs/devices/device123.ovpn
- Generating client specific configuration.
-- Start VPN IP Pool: 10.8.1
-- End VPN IP Pool: 10.8.200
-- Clients found: 21
-- Last assigned IP: [10.8.1.81]
-- Next Available IP: [10.8.1.85]
- Adding client/customer mapping to database.
-- Client(device) 'device123' added under Customer 'cba'.
- Uploading client VPN configuration (/etc/openvpn-cba/client/configs/devices/device123.ovpn) to device.
- Restarting client VPN.
Done.
```

### Generating a user .ovpn configuration file
Generate the OpenVPN configuration file of user user123 under customer cba. The location or path of the generated OpenVPN configuration file and the user assigned VPN IP address is shown in the result.

Command:
```
/usr/local/bin/gvcf.php -c cba -n user123 -t user
```
Output:
```
Starting /usr/local/bin/gvcf.php
- Generating client VPN configuration.
-- Generated: /etc/openvpn-cba/client/configs/users/user123.ovpn
- Generating client specific configuration.
-- Start VPN IP Pool: 10.8.200
-- End VPN IP Pool: 10.8.255
-- Clients found: 2
-- Last assigned IP: [10.8.200.13]
-- Next Available IP: [10.8.200.17]
- Adding client/customer mapping to database.
-- Client(user) 'user123' added under Customer 'cba'.
Done.
```

## Installing .ovpn configuration file into a device

```
1. SSH to the device.
2. Update the system package list.
sudo apt update

3. Install the OpenVPN client.
sudo apt install openvpn

4. Upload the device OpenVPN configuration file into /etc/openvpn.

5. Rename the .ovpn file into client.conf file.

mv /etc/openvpn/customer-device_name /etc/openvpn/client.conf

6. Start OpenVPN client to connect to the VPN.
sudo systemctl start openvpn@client

7. Enable the OpenVPN client to connect at startup
sudo systemctl enable openvpn@client


Testing
1. Check if the device is able to connect to the VPN using ifconfig and ping commands.
sudo ifconfig

The result of ifconfig command must include the openvpn tunnel interface with an assigned IP address (e.g. 10.8.x.x/24)

2. Ping 10.8.0.1

The Server VPN IP address must be reachable and responds to PING requests.
```
