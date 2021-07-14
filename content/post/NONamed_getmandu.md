---
title: "[Digital Forensic] NONamed_길에서 주어온 만두"
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

![Problem](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/Problem.PNG?raw=true "문제")

NONamed_길에서 주어온 만두 문제는 위와 같다.  
제공된 파일을 먼저 다운로드했다.  

![zip](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/zip.PNG?raw=true "zip")

이번 과제에서 처음으로 정상적인 zip 파일이다!!  
big.png와 readme.txt로 구성되어 있다.  

![big](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/big.png?raw=true "big.png")

readme.txt에는 아래와 같이 문제의 힌트가 적혀 있다.  

![readme.txt](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/readme1.PNG?raw=true "readme.txt")

passwd가 필요한 것으로 봐서 openstego나 steghide같다.  
passwd를 알아내기 위해 먼저 Ubuntu에서 Strings 명령어를 통해  
파일 내의 문자열을 출력했다.

![str1](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/string1.PNG?raw=true "str1")

![str2](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/string2.PNG?raw=true "str2")

사실 Strings 명령어로 passwd를 알아낼 수 있으리라 생각하지 않았어서  
많이 당황했다.  

먼저 아까 예상한 Steghide를 통해 복호화를 시도했다.  

![steghide](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/steghide1.PNG?raw=true "steghide")

실패!  
다음은 Openstego를 통해 복호화를 시도했다.  

![openstego](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/openstego1.PNG?raw=true "openstego")

성공했다!  
medium.png를 추출해냈다.  

![medium](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/medium.png?raw=true "medium")

----
여기서부터는 다른 사이트를 참조했습니다.  
\[참조\] : https://yysecurity.tistory.com/208  
---

사실 medium.png에서 pass가 달라지는 줄 알고 RGBA 값이랑 Exif 등등.. 다양한 방식으로 분석했다.  
문제는 가장 근본적인 생각을 못했다는 점이다.  
pass가 동일하다...  

![openstego](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/openstego2.PNG?raw=true "openstego")

![small](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/small.png?raw=true "small")

이렇게 small.png를 추출해내고  

![openstego](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/openstego3.PNG?raw=true "medium")

똑같이 flag.txt를 추출해낼 수 있다...  

![flag](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/NONamed_%EA%B8%B8%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%96%B4%EC%98%A8%20%EB%A7%8C%EB%91%90/flag.png?raw=true "medium")


\[출처\]  
- NONamed : http://ctf.no-named.kr:1234/
