---
title: "[Digital Forensic] NONamed_유출된 자료 거래 사건 4"
date: 2021-07-22T11:21:00+09:00
categories:
- Digital Forensic
- NONamed
tags:
- df
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

![Problem]()

이번 포스팅에서 해결해볼 문제다!!  
정보가 없다. 있다면 \"문서\"와 \"암호화\"라는 부분 정도?  
이를 바탕으로 먼저 VeraCrypt가 생각나서 NTFS Log Tracker로 찾아보았다.

![Log]()

찾기는 했지만 뭐가 뭔지 파악이 힘들다.  
무작정 이 시간대에서부터 앞 뒤로 뒤지다보니 이상한 파일들이 보인다.  
\"plan.txt\"과 \"vc.txt\"이다.  
plan.txt는 별다른 이상한 점을 찾지 못했다.  

![log2]()

하지만 vc.txt는 해당 파일이 쓰레기통으로 버려졌다는 것을 확인할 수 있었다.  

![log3]()

그래서 해당 파일을 찾아서 열어보았다.  

![passwd]()

Veracrypt에서 암호화할 때 사용한 정보가 적혀있는 것으로 추정되는 파일을 찾을 수 있었다!!  

문제는 이에 해당하는 파일을 몇 시간 째 못 찾고 있다..  
그래서 여기서부터는 다른 이의 포스팅을 참고했습니다...  
(추측은 했는데, 그걸 실행해보는 것보다 무의식적으로 답을 봐버렸습니다.. 한심하고 짜증나네요..)  

[참고](https://d0ngr0thy.tistory.com/62) 이 글을 읽고 제가 예상했던 파티션 3이 정답이였습니다.  
이 파일을 VeraCrypt로 복호화를 시키면 볼륨이 나옵니다.

![vera]()

이 볼륨 내에는 Plan.txt가 존재하며 파일 내부에 Flag가 존재합니다.  

![Flag]()

다음부터는 힘들어도 혼자 푸는 게 마음이 편하고 행복한 것 같습니다..  
정답을 보는 건 항상 후회되네요..  
