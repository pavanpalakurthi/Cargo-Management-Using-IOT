import time
import sys
import ibmiotf.application
import ibmiotf.device
import requests

#Provide your IBM Watson Device Credentials
organization = "pewl3m"
deviceType = "project"
deviceId = "2020"
authMethod = "token"
authToken = "1234567890"

def myCommandCallback(cmd):
        print("Command received: %s" % cmd.data)        
        if cmd.data['command']=='lighton':
                print("LIGHT ON")
        elif cmd.data['command'] == 'lightoff':
            print("LIGHT OFF")
                
try:
	deviceOptions = {"org": organization, "type": deviceType, "id": deviceId, "auth-method": authMethod, "auth-token": authToken}
	deviceCli = ibmiotf.device.Client(deviceOptions)
	#..............................................
	
except Exception as e:
	print("Caught exception connecting device: %s" % str(e))
	sys.exit()

deviceCli.connect()

while True:
        T=50;
        H=32;
        A=43;
        B=50
        L=17.3850;
        M=42.2475;
        #Send Temperature & Humidity to IBM Watson
        data ={'d': { 'temperature' : T, 'humidity': H,'airquality': A, 'Latitude':L, 'Longitude':M  }}
        #print data
        def myOnPublishCallback():
            print ("Published Temperature = %s C" % T, "humidity = %s gms/m3" % H,"airquality = %s ppm" % A,"........AND THE TRUCK IS AT LOCATION","Latitude = %s ° N" % L,"Longitude = %s ° E" % M , "to IBM Watson")
        success = deviceCli.publishEvent("event", "json", data, qos=0, on_publish=myOnPublishCallback)
        if not success:
            print("Not connected to IoTF")
        time.sleep(1)
        if (A>B):
                print("the air Quality is lower than the normal condition that is AIR QUALITY INDEX EXCEEDS '50ppm'!!!!.")
        def myCommandCallback(cmd):
                print("Command received: %s" % cmd.data)
                if cmd.data['command']=='lighton':
                        print("LIGHT ON")
        if (M==82.2475):
                print("THE TRUCK HAS REACHED THE DESTINY")

      
        deviceCli.commandCallback = myCommandCallback

# Disconnect the device and application from the cloud
deviceCli.disconnect
