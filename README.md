# HOME-SECURITY-SYSTEM-USING-RASPBERRY-PI
import smtplib, email, os
from email.mime.base import MIMEBase
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email import encoders
import time
import cv2
# from picamera import PiCamera
from time import sleep
from datetime import datetime
import RPi.GPIO as GPIO
from gpiozero import MotionSensor
#***************************** GPIO setup ************************
GPIO.setwarnings(False)
GPIO.setmode(GPIO.BOARD)
GPIO.setup(11, GPIO.IN)
#*************************** Email parameters **************************
subject='Security Alert: A motion has been detected'
bodyText="""\
Hi,
A motion has been detected in your room.
Please check the attachement sent from rasperry pi security system.
Regards
Team Home Security Projet VITAP
"""
SMTP_SERVER='smtp.gmail.com'
SMTP_PORT=587
USERNAME='respi.atlantis@gmail.com'
PASSWORD='lwtbhdsuzhct'
RECIEVER_EMAIL='praneeth.21bce9379@vitapstudent.ac.in'
#*************************** Video filename and path ********************
filename_part1="surveillance"
file_ext=".jpg"
now = datetime.now()
current_datetime = now.strftime("%d-%m-%Y_%H:%M:%S")
filename=filename_part1+"_"+current_datetime+file_ext
filepath="/home/pi/Desktop/"
def send_email():
print("Mailing...")
message=MIMEMultipart()
message["From"]=USERNAME
message["To"]=RECIEVER_EMAIL
message["Subject"]=subject
message.attach(MIMEText(bodyText, 'plain'))
attachment=open(filepath+filename, "rb")
mimeBase=MIMEBase('application','octet-stream')
mimeBase.set_payload((attachment).read())
encoders.encode_base64(mimeBase)
mimeBase.add_header('Content-Disposition', "attachment; filename= " +filename)
message.attach(mimeBase)
text=message.as_string()
session=smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
session.ehlo()
session.starttls()
session.ehlo()
session.login(USERNAME, PASSWORD)
session.sendmail(USERNAME, RECIEVER_EMAIL, text)
session.quit
print("Email sent")
def remove_file():
if os.path.exists(filepath+filename):
 os.remove(filepath+filename)
else:
 print("file does not exist")
#**************************** Initiate pi Camera*************************
#camera=PiCamera()
#camera.vflip = True
#********************* Main code for method call ************************
while True:
i = GPIO.input(11)
#if i==1:
 #camera.start_preview(fullscreen=True, window = (1250,10,640,480))
 #camera.capture(filename)
 #camera.stop_preview()
if i==1:
 print("Motion Detected")
 cap = cv2.VideoCapture(0)
# Capture frame
 ret, frame = cap.read()
 if ret:
 cv2.imwrite(filepath+filename, frame)
 cap.release()
 sleep(2)
 send_email()
 sleep(2)
# remove_file()
