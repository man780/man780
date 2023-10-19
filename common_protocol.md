# HTTP Comm Protocol
v 1.3

## to Device
- taskRequest
- gpsReport (once time)

## Tasks from server
1. add/update User
2. deleteUser
3. sync time
4. The task (idle)
5. setScreenSave (up to 5 images)
6. upgrade
7. getLogFile
8. getDevStatus
9. delUserList
10. setResumeTransfer (Set the broken network transmission)
11. setPlatformServer

## taskResult
The device completes the task and returns the results to the server
## faceRecognition
After the device recognizes the face, send a recognition record to the server
