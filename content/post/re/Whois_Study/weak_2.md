---
title: "[RE] RE Study Weak 2"
date: 2021-11-08T22:00:29+09:00
categories:
- RE
- Whois
tags:
- RE
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

Whois RE Study 2주차 과제에 대해서 포스팅하려 한다.  
과제는 총 2개. Root-Me와 EasyReverseMe이다.  

# Root-Me

정확히는 PE x86 - 0 Protection이다.  
먼저 문제를 보면 아무런 힌트없이 파일 하나를 준다.  

![Problem1](https://github.com/RoomRooms/blog/blob/master/img/Reversing/Root-Me/PE%20x86%20-%200%20Protection/Problem1.PNG?raw=true)

문제에서 제공되는 파일을 x32dbg로 열어보자!  

![Problem2](https://github.com/RoomRooms/blog/blob/master/img/Reversing/Root-Me/PE%20x86%20-%200%20Protection/Problem2.PNG?raw=true)

흠.. 사실 처음 열어봐서는 하나도 모르겠다.  
일단 프로그램 작성자가 작성한 부분을 찾아야겠다.  
이를 위해서 몇 가지 방법이 있는데 필자는 import한 함수를 통해 찾아볼 것이다.  

![import](https://github.com/RoomRooms/blog/blob/master/img/Reversing/Root-Me/PE%20x86%20-%200%20Protection/import.PNG?raw=true)

위 버튼을 누르면 사용자가 import한 함수들을 알 수 있다.  
이를 통해 찾아보면 puts 함수가 있는 것을 확인할 수 있는데,  
이를 보고 사용자가 작성한 부분이라 추정하여 이를 추적해보았다.  

![Find](https://github.com/RoomRooms/blog/blob/master/img/Reversing/Root-Me/PE%20x86%20-%200%20Protection/Find.PNG?raw=true)

함수 하나를 찾았다. 내용을 보니 문자를 입력받고  
이를 비밀번호와 대조하는 역할의 함수로 추정된다.  
Root-Me를 접속해보면 알겠지만, 이 문제에서는 비밀번호를 찾으라고 했다.  
프로그램에서 대조하는 문자들을 정리해서 Root-Me에 입력해보니 정답임을 확인할 수 있었다.  

![puts](https://github.com/RoomRooms/blog/blob/master/img/Reversing/Root-Me/PE%20x86%20-%200%20Protection/puts.PNG?raw=true)

-----

# EasyReverseMe

이 문제는 우리 Whois내에서 만들어진 교육용 문제이다.  
먼저 프로그램을 x64dbg를 통해서 열어보면 다음과 같다.  

![Problem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/Problem.PNG)

흠.. 하나도 모르겠다. 일단 실행을 시켜보자.  

![Fproblem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/FProblem.PNG)

첫 번째 문제다. 무슨 숫자일까?  
이는 puts를 통해 문자열을 출력한 부분 아래에서 확인할 수 있다.  
cmp 문을 통해서 입력받은 문자열과 0x49를 비교하는 부분을  
처음에는 이를 그대로 49라 입력하면 틀린다.  
컴터는 10진수로 입력받기에 0x49를 10진수로 바꿔서 73을 입력해보면 정답이 나온다.  

![FSuccess](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/FSuccess.PNG)

다시 쭉~ 실행시키다보면 두 번째 문제와 마주하게 된다.  

![SProblem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/SProblem.PNG)

와우, 내가 어떻게 알까 싶지만, 코드를 분석해보자.  
puts를 통해 문자열을 출력한 이후에 fgets를 통해서 입력을 받는다.  
입력받은 값을 qword ptr ds:\[40406D\]랑 비교하게 되는데,  
이를 통해서 정답이 "I'm so hungry"라는 것을 알 수 있었다.  

![SSuccess](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/SSuccess.PNG)

자 마지막이다. 쭉 실행을 하다보면 랜덤한 값을 맞추라는 문제가 나온다.  

![FinalProblem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/Finalproblem.PNG)

느엇.. 내가 어케 알지?.. 싶지만 코드를 보면 문자열을 출력하기 전에  
rand함수를 통해서 랜덤값을 결정하는 것을 확인할 수 있다.  
이렇게 결정된 랜덤값이 RAX에 저장되게 되는데 그것은 다음과 같다.  

![rand](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/rand.PNG)

0x5630은 16진수다. 이를 10진수로 변환하면? 22064이다.  
자 이제 랜덤값을 프로그램에 입력하면?

![FinalSuccess](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/FinalSuccess.PNG)

문제를 풀 수 있다. 이제 마지막 Flag 값을 받으면서 프로그램이 종료된다.  

![Flags](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Reversing/Whois_Study/EasyReverseMe/Flags.PNG)
