---
title: "[Digital Forensic] Suninatas 21번"
date: 2021-07-15T03:53:58+09:00
categories:
- Digital Forensic
- Suninatas
tags:
- DF
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

![문제](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/Problem.PNG?raw=true "21번 문제")

Suninatas 21번 문제는 위와 같다.  
처음 문제를 확인하자마자 Steganography를 떠올렸다.  
이를 확인하기 위해서 Ubuntu에서 Binwalk를 사용하여 내부에 숨겨진 파일이 존재하는지 확인했다.    

![binwalk](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/binwalk.PNG?raw=true "binwalk 실행결과")

상당히 많은 파일이 존재한다는 것을 알 수 있다.  
이를 전부 사람이 추출하는 것은 비효율적이다.  
Foremost 도구를 사용하여 monitor.jpg 안에 존재하는 모든 파일을 추출해냈다.  

![foremost](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/foremost.PNG?raw=true "foremost 실행결과")

![output](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/output.PNG?raw=true "foremost output")  
위와 같이 monitor.jpg 내에 숨어 있는 다량의 이미지 파일들을 추출하여 Flag 값을 알 수 있다.

\[출처\]  
- Suninatas : http://suninatas.com/
