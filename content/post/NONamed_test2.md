---
title: "[Digital Forensic] NONamed_입사 테스트 [2]"
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

![Problem](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "입사 테스트 [2] 문제")

NONamed_입사 테스트 \[2\] 문제는 위와 같다.  
제공된 파일을 먼저 다운로드했다. ( task.jpg )  

![제공된 이미지](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "task.jpg")

당연하게도 손상된 파일이다.  
파일의 시그니처 값을 확인하기 위해 HxD로 파일을 열어주었다.  

![HeX 값](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "task.jpg의 Hex값")

정상적인 JPG의 파일 시그니처는 "FF D8 FF E0 xx xx 4A 46"인 것에 비해  
task.jpg의 파일 시그니처는 "89 4A 50 47 0D 0A 1A 0A"인 것을 알 수 있다.  
이를 통해 jpg 파일이 아니라는 것을 확인할 수 있다.  

task.jpg 파일 내부 구조를 분석하다보면 IHDR이 존재한다는 것을 알 수 있다.  
IHDR은 png에 존재하는 필수 chunk 중에 하나인데, 이를 통해 task.jpg가 png 파일이라는 것을 알 수 있다.  
task.jpg의 파일 시그니처를 png의 파일 시그니처인 "89 50 4E 47 0D 0A 1A 0A"로 바꿔줬다.  

![fix sign](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "파일 시그니처 수정")

그러면 새까만 이미지의 task.png 파일을 얻을 수 있다.  

![black img](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "task.png")

task.png가 새까만 것으로 봐서 손상된 파일임을 짐작할 수 있다.  
손상된 부분을 확인하기 위해서 Ubuntu의 pngcheck를 사용했다.  

![pngcheck](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "파일 손상 확인")

오류를 보면 tRNS 청크가 IDAT보다 선행해야 한다고 나온다. 즉, tRNS 청크가 IDAT 청크보다 뒤에 존재한다는 것이다.  
이를 수정하기 위해서 TweakPNG를 사용했다.  

![TweakPNG](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "청크 확인")

![TweakPNG](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "청크 순서 변경")

task.png의 내부 chuck를 보고 한참을 고민하고, pngcheck로 삽질을 하다가 APNG 구조를 참고하라는 힌트를 얻었다.  

![apng](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "apng 구조")

apng 구조는 위와 같은데 아까 확인한 task.png의 청크와 유사하다는 것을 알 수 있다.  
task.png의 청크 구조를 apng에 맞게 수정해주었다.  

![TweakPNG](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "청크 순서 변경")

![task.png](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "파일 복구")

청크를 apng에 맞게 수정한 결과 위와 같이 task.png를 복구할 수 있었다.  

하지만 아직 Flag값을 알지 못했다. 그래서 apngdis를 사용해서 task.png 파일의 프레임을 분리하였다.  
그 결과 Flag값을 얻을 수 있었다.  

![Flag](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/NONamed/magicIMAGE/problem.PNG?raw=true "Flag")


\[출처\]  
- NONamed : http://ctf.no-named.kr:1234/
- Wikipedia : https://ko.wikipedia.org/wiki/APNG
