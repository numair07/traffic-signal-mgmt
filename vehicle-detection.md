```python
import cv2
```


```python
def calculateVehicles(path):
    
    vidCap = cv2.VideoCapture(path)

    BS_KNN = cv2.createBackgroundSubtractorKNN()
    BS_MOG2 = cv2.createBackgroundSubtractorMOG2()
    vehicle = 0
    validVehicles = []
    while vidCap.isOpened():
        ret, frame = vidCap.read()

        if not ret:
            break

        fgMask = BS_MOG2.apply(frame)

        cv2.line(frame, (220,450), (1000,450), (0,0,255), 2) # RED Line
        cv2.line(frame, (220,440), (1000,440), (0,255,0), 1) # GREEN Offset ABOVE
        cv2.line(frame, (220,460), (1000,460), (0,255,0), 1) # GREEN Offset BELOW

        conts, _ = cv2.findContours(fgMask, cv2.RETR_LIST, cv2.CHAIN_APPROX_SIMPLE)

        for c in conts:
            x, y, w, h = cv2.boundingRect(c)

            visibleVehicle = (w > 60) and (h > 60)
            if not visibleVehicle:
                continue

            if x > 250 and x < 450 and y > 180:
                cv2.rectangle(frame, (x,y), (x+w, y+h), (0,255,0), 2)
                xMid = int((x + (x+w))/2)
                yMid = int((y + (y+h))/2)
                cv2.circle(frame, (xMid,yMid),5,(0,0,255),5)

                validVehicles.append((xMid,yMid))

                if yMid > 442 and yMid < 451:
                    vehicle += 1

        #print(vehicle);
        cv2.putText(frame, 'Total Vehicles : {}'.format(vehicle), (450, 50), cv2.FONT_HERSHEY_PLAIN, 2, (255,255,255), 2)
        cv2.imshow("Vehicle Count", frame)

        # wait for any key to be pressed
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

        # release video capture
    cv2.destroyAllWindows()
    vidCap.release()
    return vehicle

```


```python
vehicleCount = []

trafficSignal1 = calculateVehicles('./Downloads/traffic1.mp4')
trafficSignal2 = calculateVehicles('./Downloads/traffic2.mp4')
trafficSignal3 = calculateVehicles('./Downloads/traffic3.mp4')
trafficSignal4 = calculateVehicles('./Downloads/traffic4.mp4')

vehicleCount.append(trafficSignal1)
print('Traffic Signal 1 : ' + str(trafficSignal1))
vehicleCount.append(trafficSignal2)
print('Traffic Signal 2 : ' + str(trafficSignal2))
vehicleCount.append(trafficSignal3)
print('Traffic Signal 3 : ' + str(trafficSignal3))
vehicleCount.append(trafficSignal4)
print('Traffic Signal 4 : ' + str(trafficSignal4))

minTrafficSignalTime = 20
averageSignalTime = 60
totalTime = averageSignalTime * 4
relevantTime = totalTime-(minTrafficSignalTime*4)

totalVehicles=0

for x in vehicleCount:
    totalVehicles += x

for i in range(len(vehicleCount)):
        print("Time that traffic signal " + str(i) + " will remain open for - " + str(int(20 + vehicleCount[i]/totalVehicles*relevantTime)) + " seconds")

```

    Traffic Signal 1 : 6
    Traffic Signal 2 : 2
    Traffic Signal 3 : 3
    Traffic Signal 4 : 4
    Time that traffic signal 0 will remain open for - 84 seconds
    Time that traffic signal 1 will remain open for - 41 seconds
    Time that traffic signal 2 will remain open for - 52 seconds
    Time that traffic signal 3 will remain open for - 62 seconds
    


```python

```
