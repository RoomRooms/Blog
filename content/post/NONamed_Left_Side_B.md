---
title: "[Digital Forensic] NONamed_Left Side B"
date: 2021-07-15T04:53:38+09:00
categories:
- Digital Forensic
- NONamed
tags:
- DF
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

![Problem](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/Problem.PNG?raw=true "문제")

NONamed_길에서 주어온 만두 문제는 위와 같다.  
제공된 파일을 먼저 다운로드했다.  

![bmp](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/task.bmp?raw=true "bmp")

task.bmp 파일을 다운받아 실행했다.  
간만에 제대로 실행되는 이미지다!  
값을 보면 Fake Flag값 밖에 없다. ( 물론 입력해봤다! )  

문제에서 준 힌트는 문제 이름.  
Left Side B를 잘 조합하다보면 LSB가 된다.  
즉, task.bmp의 LSB에 Flag 값이 숨어있다고 생각하면 될 것이다!  

![hxd1](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/hxd1.PNG?raw=true "hxd1")

task.bmp는 위와 같은 포맷을 갖고 있다.  
이제 내부 Hex 값 중에서 이상한 특히, LSB가 변조됨에 따라 미세하게 차이가 나는 부분을 찾아보자.  

![hxd2](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/hxd2.PNG?raw=true "hxd2")

이 부분을 복호화하기 위해서 먼저 str.txt 형식으로 가공했다.  

![str.PNG](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/str.PNG?raw=true "str.PNG")

Hex 값을 보다 보면 FF or FE로 LSB가 1씩 차이난다.  
이런 LSB 값만을 추출하기 위해서  
파이썬으로 코드를 작성했다.  

![py](https://https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/py.PNG?raw=true "py")

py 파일은 str.txt를 한 줄씩 읽어서 각 줄에 해당되는 각 값의 LSB 값만을 추출한다.  
그렇게 추출된 값을 8bit씩 조합한다. 조합한 값은 10진수로 변환되며,   
10진수를 chr 함수를 통해 아스키 문자로 번역한다.  

파이썬 코드를 실행시켜보면 Flag 값을 얻을 수 있다.

![flag](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_Left%20Side%20B/flag.PNG?raw=true "flag")

\[출처\]  
- NONamed : http://ctf.no-named.kr:1234/
- Hackerz on the Ship : https://bpsecblog.wordpress.com/2016/08/21/amalmot_4/
