# Rpi Pico

## Web server + WiFi

```python
import network
import socket
from time import sleep
from picozero import pico_temp_sensor, pico_led
import machine
import rp2
import sys

ssid = 'XXXX'
password = 'XXXX'

def open_socket(ip):
    # Open a socket
    address = (ip, 80)
    s = socket.socket()
    s.bind(address)
    s.listen(1)
    return s


def connect():
    #Connect to WLAN
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    wlan.connect(ssid, password)
    while wlan.isconnected() == False:
        if rp2.bootsel_button() == 1:
            sys.exit()
        print('Waiting for connection...')
        pico_led.on()
        sleep(0.5)
        pico_led.off()
        sleep(0.5)
    ip = wlan.ifconfig()[0]
    print(f'Connected on {ip}')
    pico_led.on()
    return ip

def serve(s):
    #Start a web server
    while True:
        conn, addr = s.accept()
        request = conn.recv(1024).decode()
        print('Request:', request)
        
        response = ['HTTP/1.1 200 OK', 'Content-Type: text/plain', '', 'yo yo']
        conn.send('\r\n'.join(response))
        conn.close()

ip = connect()
s = open_socket(ip)
serve(s)
```

## Triggering via STDIN over tty

```python
import select
import sys

import utime
import machine

switch_pin = machine.Pin(2, machine.Pin.OUT, value=1)  # GPIO2 as an output, default HIGH

def trigger_switch():
    switch_pin.value(0)  # "Press" the button (short to ground)
    utime.sleep(0.1)
    switch_pin.value(1)  # "Release" the button (disconnect)

while True:
    if select.select([sys.stdin],[],[],0)[0]:
        cmd = sys.stdin.readline().strip()
        print('stdin', cmd)
        #if cmd == "trigger":
        print('running trigger')
        trigger_switch()
    utime.sleep(0.1)
```
