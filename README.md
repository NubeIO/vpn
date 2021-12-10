# VPN
Setup and install guides.

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
