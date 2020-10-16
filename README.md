# Step by Step: Connecting Raspberry Pi 4 to AWS IoT
## Raspberry Pi 4 Prerequisites
### Configure Raspberry Pi
- Installation of the latest [Raspberry Pi OS](https://www.raspberrypi.org/downloads/) from SD Card.
- Install latest updates and upgrades
```
sudo apt-get update
sudo apt-get upgrade 
```

### Test Sense Hat Accessory
- Install Sense Hat.
`sudo apt-get install sense-hat`
- Attach the sensor when the raspberry is turned off. Check [Getting Started Page](https://www.okdo.com/getstarted/) for more details.
- Test that the sensor is properly connected with ![sense_hat.py](/scripts/sense_hat.py).

_Important_: When raspberry pi turns on, the sensor lights up in a rainbow color pattern. It should turn off after sometime. If the display doesn’t turn off, try the following steps from the command line.
```
cd /boot
sudo nano config.txt
```
Add to the bottom of the config.txt file the following line
`dtoverlay=sense-hat`

Save and exit nano

Reboot `sudo reboot`

### Test Camera Accessory
- Enable camera from raspberry pi configuration.
- Reboot Raspberry
- Attach the camera when the raspberry is turned off. Check [Getting Started Page](https://projects.raspberrypi.org/en/projects/getting-started-with-picamera/2) for more details.
- Test that the camera is properly connected with ![camera_picture.py](/scripts/camera_picture.py) or ![camera_video.py](/scripts/camera_video.py).

## AWS Prerequisites
### An AWS Account
if you don’t have it. You can create it [here](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html?nc2=h_ct&src=default).

### Create IoTUser
- Create Group: “IoTAdmin” with AWSIoTFullAccess policy
- Create user for “IoTUser” with AWS Management Console access and user group IoTAdmin.

### Registry of a (IoT) thing on AWS
- Login to AWS Console with your “IoTUser” user.
- Go to IoT Core. You can search All services under “Internet of Things” category. This will take you to AWS IoT.

#### Create a policy. 
- From the left panel select Secure → Policies and click on the “Create a policy” button.
- Name it “IoT-rpi-policy”. 
- Specified the following values:
```
  Action: iot:*
  Resource ARN: *
  Effect: Allow
```
- Click on the “Create” button
  
#### Create the (IoT) Thing
- From the left panel, select Manage → Things and click on the “Register a thing” button.
- Click on the “Create a single thing” button.
- Name your thing “IoT-rpi” and leave the other fields with default values
- Click on the “Next” button.

#### Create certificates and keys
- Click on the ”Create certificate” button.
- Download the certificate and both keys (public and private).
- Download root CA certificates (especially Amazon Root CA 1).
- Back on AWS IoT. Click the “Activate” button.
- Click on the “Attach policy” button.
- Check the policy that we created previously “IoT-rpi-policy”.
- Click the “Register Thing” button.  

## Connecting Raspberry Pi 4 to AWS IoT 
### Install the required tools and libraries for the AWS IoT Device SDK
- Before you install an AWS IoT Device SDK, update the operating system.
```
sudo apt-get update
sudo apt-get upgrade
```
- Install required libraries
```
sudo apt-get install cmake
sudo apt-get install libssl-dev
```
- Make sure git is installed.

`git --version`

The previous command should return something similar to 

`git version 2.20.1`

_If your device's operating system doesn't come with Git installed, you'll need to install it before moving forward._

### Install AWS IoT Device SDK with python
- Make sure Python is installed.

`python3 --version`

The previous command should return something similar to 

`Python 3.7.3`

Otherwise, you'll need to install it before moving forward.
- Make sure pip3 is installed.

`pip3 --version`

The previous command should return something similar to 

`pip 18.1 from /usr/lib/python3/dist-package/pip (python 3.7)`

Otherwise, you'll need to install it before moving forward.
- Install the current AWS IoT Device SDK for Python
```
cd ~
python3 -m pip install awsiotsdk
```

### Install certificate files in the device
- Create a "certs" folder
```
cd ~
mkdir certs
```
- Into the ~/certs directory, copy the private key, device certificate, and root CA 1 certificate that you created earlier.

### Run sample application
- To run the sample app you need your iot endpoint. You can get it from your AWS Management Console. Go to IoT Core service → Manage → Things and click on your IoT thing’s name. You will find you endpoint on the interact tab.
- Navigate to the sample app directory and run your sample application.  
```
cd ~/aws-iot-device-sdk-python-v2/samples
python3 pubsub.py --topic topic_1 --root-ca ~/certs/AmazonRootCA1.pem --cert ~/certs/[your-certificate-code]-certificate.pem.crt --key ~/certs/[your-certificate-code]-private.pem.key --endpoint [your-iot-endpoint]
```

### View messages from the sample app in the AWS IoT console
- Open the MQTT client in the AWS IoT console. You can finding on your IoT Thing → Activity tab.
- Name it “topic_1”.
- Click the “Subscribe to topic” button.
- Run again the sample app
```
python3 pubsub.py --topic topic_1 --root-ca ~/certs/AmazonRootCA1.pem --cert ~/certs/[your-certificate-code]-certificate.pem.crt --key ~/certs/[your-certificate-code]-private.pem.key --endpoint [your-iot-endpoint]
```
- Messages show appear on the terminal and MQTT Client.
