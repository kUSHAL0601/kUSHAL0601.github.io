---
title: Connecting Components - The socket way
date: 2021-06-21T01:41:15+05:30
lastmod: 2021-06-21T01:41:15+05:30
author: MajAK
author: MajAK
avatar: /img/me.png
# authorlink: https://author.site
cover: /img/Socket.png
categories:
  - GSOC
tags:
  - sockets
  - backend
  - notification
# nolastmod: true
draft: false
---

__How I created GUI notification service for OWASP SecureTea using sockets, but with a twist!__
<!--more-->

## The Context - Problem at Hand
I spent my time contributing to SecureTea in the summer of 2020. [SecureTea](https://github.com/OWASP/SecureTea-Project) is an open source project under OWASP which provides a one-stop security solution for various devices (_personal computers / servers / IoT devices_). It contains three major components : A Realtime App hosted in Angular (_<b>GUI</b>_), a Python app (_<b>SecureTea-Core</b>_) and a Flask App (_<b>ServerApp</b>_). As one can guess, _SecureTea-Core_ is the _heart_ of the solution. It contains various modules implemented for security as well as to get notified about pertaining issues. It could be triggered to run from the _GUI App_ as well as _CLI_. The ServerApp was set up to provide _Realtime data_ corresponding to health metrics and usage of the device being monitored. Now, my task was to introduce a system so that there was some kind of Authentication in the GUI and send notifications/updates of the SecureTea-Core to the GUI. The idea was that the GUI component could be hosted at an enterprise level and various users could connect their local devices/cloud devices to it by hosting the ServerApp (locally/per cluster) and installing __securetea__ library on them. The Authentication part was easy to address. We are going to discuss how I built a simple communication service to achieve __Real Time Notifications__.

## Initial Approach - A Naive one
My initial thought process was to open up a socket connection from SecureTea-Core to GUI and notifications could directly be sent via the socket. This would solve the task of notifications being realtime and provide some level of security. But it had a few __shortcomings__:
### Scalability
 When it comes to scalability, the system will not be able to autoscale, unless the new version is installed on the target system every time scalability/ load balancing might be needed. Also considering the use case scenario where a lot of notifications need to be sent to GUI, it consumes a lot of compute power(might slow down/ crash other jobs running on device) as well as blocks the execution of other features.
### Failure 
 On failure occurring in the Socket part of the system, the system would crash which would affect the overall system security, since the securetea also prevents attacks to some scale!
### Retry pattern 
 Retry pattern if required cannot be implemented at this level, since it would further block the functioning of other features for the time being.
### Excessive socket connections 
 Considering a system with a large number of devices, the number of socket connections would be large. With a limited number of socket connections GUI can open(_due to fixed number of ports_), it would inevitably result in a queue system for socket connections.
	Hence I settled to try and implement it at the level of ServerApp. The pros are discussed below.

## Actual approach - Using ServerApp

The approach is based on the idea of setting up a communication between the SecureTea-Core and ServerApp, which would further pass on the information to GUI. The way it was implemented is that an API was set up to send notification requests to GUI. On receiving anything in log, SecureTea-Core would call the API, which in place would send notification to GUI via socket. These notifications would be shown as toasts in GUI. Although we still require sockets for it to be Realtime. An important aspect to note is that there will be a delay introduced because of API call, but with a fair assumption of everything hosted on the same network, it can be considered negligible. Let us look into how it __solves__ previous __shortcomings__ :
### Excessive socket connections 
  Since the ServerApp might be hosted at a cluster level, it decreases the number of socket connections GUI needs to handle. It might still need an implementation of queue system for socket connection on a very large amount of clusters, but the bound on #devices per GUI instance increases by a large factor.
### Scalability
Also since the number of ServerApp resources/copies can be easily scaled _horizontally_ and configured, blocking security features is not an issue anymore. A separate monitoring service could be set up for the ServerApp (like a heartbeat), which would be easy to implement to make sure the ServerApp is healthy.

### Failure & Retry Pattern
SecureTea-Core being blocked on failure will no longer be an issue and a retry pattern could be easily implemented if required. Also a proxy of some kind is automatically setup here between SecureTea-Core and ServerApp, making the architecture a bit more secure.

## An overview of Code :
You can find some important excerpts from the code here. Refer to this [commit](https://github.com/OWASP/SecureTea-Project/commit/941d9f65e30b7f3124ef8172fccf043b27417fa3) to get an idea in detail. Here is an overview of the important components :

### ServerApp
_app.py_
```
from flask_cors import CORS
from flask_sqlalchemy import SQLAlchemy
from functools import wraps
from flask_socketio import SocketIO, emit

app = Flask(__name__)
CORS(app)
app.config.from_object('config')
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
cors = CORS(app,resources={r"*":{"origins":"*"}})
async_mode = None
socketio = SocketIO(app, async_mode=async_mode, cors_allowed_origins="*")

@app.route('/notifs',methods=['GET'])
def notifs():
    if request.remote_addr==DEVICE_ADDR:
        socketio.emit('newmessage', {'message': request.args.get("msg")})
    return 'Notif sent'
```
_run.py_
```
from app import app
app.run()
from app import socketio, app
socketio.run(app)
```
### SecureTea-Core
```
import requests as req
import time

def printinfo(self, message):
    "Print info.
    Args:
        message (str): Message to log as info
    "
    print(self.LEGEND + self.OKGREEN + message + self.ENDC)
    param={"msg" : "S"+message}
    resp = req.get(SERVER_ADDR+"/notifs",params=param)

def printerror(self, message):
    "Print error.
    Args:
        message (str): Message to log as error
    "
    print(self.LEGEND + self.ERROR + message + self.ENDC)
    param={"msg" : "E"+message}
    resp = req.get(SERVER_ADDR+"/notifs",params=param)
...
```
### GUI App
```
import * as toastr from 'toastr';
import * as io from 'socket.io-client';

socket: any;
toastr.options ={
      "progressBar": true
    }
    this.socket = io(this.apiRoot);
    this.socket.on('newmessage', (msg) => {
      if(msg.message.charAt(0) === "W")
      {
        toastr.warning(msg.message.substring(1));
      }
      else if(msg.message.charAt(0) === "S")
      {
        toastr.success(msg.message.substring(1));
      }
      else
      {
        toastr.error(msg.message.substring(1));
      }
    });
```

## Give it a thought
+ Does introducing socket make the Angular app one with Backend?
+ What are Node Apps : Frontend, Backend or Something else? [Spoiler Alert!](https://www.section.io/engineering-education/nodejs-frontend-backend/)
+ Have a look at toastr import and implementation. It is a gem(_hyperbole_) to find with documentation like theirs!
+ Are sockets optimal or can we do better? (Message queues)