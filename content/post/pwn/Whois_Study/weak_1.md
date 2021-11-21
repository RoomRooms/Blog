---
title: "[Pwn] Pwn Study Weak 1"
date: 2021-11-21T02:51:09+09:00
categories:
- Pwn
- Whois
tags:
- Pwn
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

오늘은 Whois에서 진행 중인 Pwnable 교육 과제의 Write-UP을 포스팅하려고 한다!  

# Student ID

![Problem]()

첫 문제다! Student ID!!  
일단 다운받아서 Ubuntu로 옮겨보자.  

![file]()

일단 해당 파일은 ELF-64bit 파일이다. 리눅스 실행파일이라는 뜻이다!  
일단 분석을 위해 gdb를 실행하고 main 함수를 확인해보자!  

![pd main]()

흠... 읽어보면 출력 -> 입력 -> 출력 x 2의 흐름이다.  
필자는 main에서 가장 중요하다고 본 부분이 다음과 같다.  

![system]()

사용자에게 scanf함수를 통해서 입력을 받은 후 rbp-0x4 주소에 있는 데이터와 0x499602d2와 비교,  
같지 않다면 점프하지만, 같다면 system 함수를 실행한다.  
그럼 system 함수가 실행하는 명령문을 확인해보자!  

![string]()

여기가 중요부분이라는 것이 명확해졌다.  
그럼 rbp-0x4의 데이터를 0x499602d2로 바꿔야 한다!  
가장 처음으로 Scanf로 입력받는 주소를 확인해보자.  
rbp-0x20이다. 즉, 문자열을 0x1c만큼 받은 다음, 0x499602d2를 입력하면  
BOF에 의해서 rbp-0x4의 데이터가 바뀔 것이다!!  
그럼 먼저 Test를 진행해보자!  

![test]()

성공이다! 그럼 바로 직접 서버에 입력하여 Flag를 얻어보자!  

![Flag]()

# Magic Spell

두 번째 문제다!  

![Problem]()

내가 아는 주문은 윙가르디움 레비오우사 뿐인데...  
일단 file 명령어를 통해서 다운받은 문제파일을 확인해보자  

![file]()

Student 파일과 마찬가지로 ELF-64bit 파일이다.  
gdb를 통해 main을 확인해보자!!  

![pd main]()

너무 심플하다. spell1 함수를 실행할 뿐,  
그렇다면 spell1 함수를 둘러보자!  

![pd spell1]()

main에 비해서 내용이 실하다. 그럼 천천히 읽어보자!  
읽다보면 한 가지 특이한 부분이 존재한다. 그것이 아래다.  

![cmp spell1]()

rbp-0x1의 주소값과 0x4d를 비교하여 같다면 puts를 한 번 실행하고 spell2 함수로 넘어간다.  
틀릴 경우는 spell1 함수 종료.  
그렇다면 cmp 바로 위 scanf를 통해서 이 문제를 통과해보자.  

scanf가 입력받는 주소는 rbp-0x20이다. 비교문을 수행하는 rbp-0x1과 0x1e의 차이가 존재한다.  
그러니 A를 0x1e번 입력한 후, 0x4d를 입력하면  
rbp-0x1에 0x4d가 삽입되면서 spell2로 넘어가게 될 것이다.  
확인해보자!!

![Fin spell1]()

성공이다! 이제 Spell2 함수의 내부를 들여다보자.  

![pd spell2]()

문자를 출력 -> 입력 -> 검사의 흐름을 갖는 함수같다.  
내용을 보다보면 여기가 포인트구나 하는 부분이 있다. 아래와 같다.  

![cmp spell2]()

scanf를 통해 rip+0x2e82의 위치에 데이터를 입력받는다.  
문제는 비교문을 수행하는 주소가 rip+0x2e6e라는 것이다.  
둘 사이에는 0x10의 차이가 존재한다. 주석에도 있듯이 bof를 통해 해결해보자.  
A를 0x10번 출력하고, 이후에 검사하는 문자열 즉, 0x205a를 입력하면 해결될 것이다.  
자 이제, 0x205a의 문자열이 무엇인지 확인해보자!  

![string]()

pwnable!이라고 한다. 즉, A를 0x1e번 입력하고 M을 1번입력하여 spell1을 통과하고,  
A를 0x10번 입력 후, pwnable!을 입력하는 것으로써 spell2가 해결되면서 Flag를 알 수 있을 것이다!  

![flag]()

# System

마지막 문제다.  

![Problem]()

일단 바로 다운 후, Ubuntu에서 File 명령어를 통해 무슨 파일인지 확인해보자.  

![file]()

ELF-64bit 파일이니, 바로 gdb를 통해서 열어보자.  

![pd main]()

단순한 프로그램이다. 입력을 받은 후, rip+0x2eb4의 주소에 위치한 command를  
system함수를 통해서 실행하는 프로그램이다.  
그럼 command를 확인해보자.  

![string]()

ls -al이라,, 현 위치의 있는 모든 파일, 폴더들의 권한등을 모두 출력하는 명령어다.  
즉, 이 부분을 cat ./flag로 덮어쓰면 해결될 것이다.  
여기를 덮어쓰기 위해서는 gets를 통해 입력받는 rip+0x2e95에서부터  
비교문을 수행하는 rip+0x2eb4까지는 0x30의 차이가 존재한다.  
즉, A를 0x30번 입력한 후, 원하는 명령어를 입력하면 해결될 것이다. 바로 테스트다!  

![test]()

명령어를 file ./shell로 바꿔봤다. 정상적으로 작동하는 것을 확인할 수 있다!!  
이제 바로 Flag를 얻으러 가보자!!  

![Flag]()

이것으로 Whois Pwnable 과제 1주차 완료다!  
