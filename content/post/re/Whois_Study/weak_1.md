---
title: "[RE] RE Study Weak 1"
date: 2021-10-13T00:32:52+09:00
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

Whois에서 RE(Reverse Engineering)에 대한 스터디를 진행했다.  
오늘은 RE 스터디에서 받은 핸드레이 과제를 포스팅하려 한다.  

# 첫 번째

```assembly
push   ebp
mov    ebp,esp
sub    esp,0x10
mov    DWORD PTR [ebp-0x4],0xa
mov    eax,DWORD PTR [ebp-0x4]
mov    DWORD PTR [ebp-0x8],eax
mov    eax,DWORD PTR [ebp-0x8]
imul   eax,eax,0x17
mov    DWORD PTR [ebp-0x8],eax
mov    eax,DWORD PTR [ebp-0x8]
sub    eax,DWORD PTR [ebp-0x4]
mov    DWORD PTR [ebp-0xc],eax
mov    eax,DWORD PTR [ebp-0xc]
cdq
idiv   DWORD PTR [ebp-0x4]
mov    DWORD PTR [ebp-0x4],eax
mov    eax,0x0
leave
ret
```
-----
한 줄씩 번역해보자.  

```assembly
push   ebp
mov    ebp,esp
```

이 부분은 어셈블리어의 함수 프롤로그입니다.  
함수를 실행하기 전 ebp, 즉 해당 함수를 실행하는 모체의 ebp를 백업하는 것입니다.  
이를 Return Address라고 칭합니다.  
백업을 하고, ebp를 esp의 위치로 데려옵니다.  

--------

```assembly
sub    esp,0x10
mov    DWORD PTR [ebp-0x4],0xa
mov    eax,DWORD PTR [ebp-0x4]
mov    DWORD PTR [ebp-0x8],eax
mov    eax,DWORD PTR [ebp-0x8]
```

위 부분은 esp에 0x10을 뺀 값을 다시 esp에 넣습니다.  
즉, 0x10만큼의 공간을 사용하려고 할당했다는 것입니다.  
ebp-0x4의 주소에 0xa의 값을 넣은 것이며,  
eax에 ebp-0x4의 주소가 갖고 있는 데이터, 즉 0xa를 삽입합니다.  
ebp-0x8의 주소에 eax이 갖는 데이터, 즉 0xa를 삽입합니다.  
마지막으로 eax에 ebp-0x8의 주소에 있는 데이터, 즉, 0xa를 삽입합니다.  

이를 C언어로 번역해보면?  

```C
int a, b, c, d;
a = 0xa;
b = a;
```

로 번역할 수 있을 것이다.  

-------

```assembly
imul   eax,eax,0x17
mov    DWORD PTR [ebp-0x8],eax
mov    eax,DWORD PTR [ebp-0x8]
sub    eax,DWORD PTR [ebp-0x4]
mov    DWORD PTR [ebp-0xc],eax
mov    eax,DWORD PTR [ebp-0xc]
```
imul은 정수의 부호가 있는 곱셈을 계산할 때 사용됩니다.  
인자가 2개일 때와 3개일 때의 경우가 존재하며,  
2개일 때는, 첫 인자에 둘을 곱한 결과를 삽입합니다.  
3개일 때는, 첫 인자에 두 번째와 세 번째 인자를 곱한 결과를 삽입합니다.  

계산한 결과를 ebp-0x8에 저장한다. 즉, b에 삽입한 거다.  
이후, b에 a를 뺄셈한 후, 이를 ebp-0xc, 즉 c에 저장한다.  
이를 C로 변역해보면?


```C
b = b * 0x17;
c = b - a;
```

---

```assembly
cdq
idiv   DWORD PTR [ebp-0x4]
mov    DWORD PTR [ebp-0x4],eax
mov    eax,0x0
```

cdq는 4byte인 eax를 8byte인 edx:eax로 확장한다는 것이다.  
이렇게 하는 이유는, 어셈블리어에서 나눗셈의 조건 때문이다.  
만일 제수가 8bit라면, 피제수는 16bit여야 한다.  
그렇기에 위처럼 16bit/16bit 연산을 진행하기 전에, 제수를 32bit으로 확장해주는 것이다.  
이렇게 확장되면 eax에 몫을 edx에 나머지값을 삽입하게 된다.  
idiv 연산은 edx:eax를 ebp-0x4의 주소가 갖는 데이터로 나누는 것이다.  
위 코드에서는 나누고 난 몫을 a에 넣고, eax를 0x0으로 초기화했다.  
이를 C언어로 번역하면?  

```C
a = c / a;
```

---

첫 번째 과제의 마지막이다.  

```assembly
leave
ret
```

이 부분은 함수의 에필로그 부분이다.  
즉, 현재 실행중인 함수를 종료하고 현함수를 실행한 모체 함수로 돌아가는 것이다.  
leave와 ret은 각각 작은 함수라고 생각하면 편합니다.  
leave는
```assembly
mov esp, ebp
pop ebp
```
이며, ret은 
```assembly
pop eip
jmp eip
```
를 의미합니다.  

leave는 esp를 ebp의 위치로 데려옵니다.  
그리고 pop ebp를 실행해서 현재 esp가 가리키는 값을 ebp로 설정하여  
모체 함수의 ebp가 복구되도록 합니다.  

ret은 pop eip를 이용하여 esp가 가리키는 값을 eip로 설정합니다.  
이후 jmp eip 명령어를 통해 현재 eip에 들어있는 값으로 eip를 이동시킵니다.  
위 과정을 모두 진행함으로써 함수는 정상적으로 종료되어 기존 모체함수로 돌아가게 됩니다.  

번역 결과를 정리해보면

```C
int main(){
	int a, b, c, d;
	a = 0xa;
	b = a;
	
	b = b * 0x17;
	c = b - a;
	a = c / a;
	
	return 0;
}
```

입니다.

# 두 번째

```assembly
push   ebp;
mov    ebp,esp;
sub    esp,0x3fc;
mov    DWORD PTR [ebp-0x400],0x0;
lea    edx,[ebp-0x3fc];
mov    eax,0x0;
mov    ecx,0xf9;
mov    edi,edx;
rep stos DWORD PTR es:[edi],eax;
lea    eax,[ebp-0x400];
mov    DWORD PTR [eax],0x6c6c6548;
mov    DWORD PTR [eax+0x4],0x6f57206f;
mov    DWORD PTR [eax+0x8],0x21646c72;
mov    DWORD PTR [eax+0xc],0x4f48570a;
mov    DWORD PTR [eax+0x10],0x4a205349;
mov    DWORD PTR [eax+0x14],0x474e414a;
mov    WORD PTR [eax+0x18],0x217e;
mov    BYTE PTR [eax+0x1a],0x0;
sub    esp,0xc;
lea    eax,[ebp-0x400];
push   eax;
call   0x8049030;
add    esp,0x10;
mov    eax,0x0;
leave;
ret;
```

한 줄씩 번역해봅시다. ( 중복은 건너뛸 예정입니다. )  

```assembly
sub    esp,0x3fc;
mov    DWORD PTR [ebp-0x400],0x0;
lea    edx,[ebp-0x3fc];
mov    eax,0x0;
mov    ecx,0xf9;
mov    edi,edx;
rep stos DWORD PTR es:[edi],eax;
```

1,020 byte 크기의 공간을 할당합니다.  
ebp-0x400의 주소에 0x0의 값을 삽입합니다.  
edx에 ebp-0x3fc의 주소를 삽입합니다.  
eax에 0x0을, ecx에 0xf9를, edi에 edx의 값을 각각 삽입합니다.  
이는 반복문 실행을 위한 초기 조건입니다.  
edi는 eax 레지스터 값이 데이터로서 저장될 주소를 의미하며,  
eax는 삽입할 데이터이다. ecx는 명령을 반복할 횟수를 나타냅니다.  

rep는 반복하라는 명령이며, 반복할 명령어는 stos다.  
stos는 eax의 데이터를 edi가 가리키는 주소에 복사하는 것이다.  
반복할 때마다 ecx의 값이 1씩 감소하며,  
edi가 가리키는 곳에 값을 삽입할 때마다 edi가 자동으로 증가합니다.  
이를 C언어로 번역하면

```C
char str[0x3fc];
for(int i=0x0;i<0xf9;i++)
	str[i] = 0x0;
```

---

```assembly
lea    eax,[ebp-0x400];
mov    DWORD PTR [eax],0x6c6c6548;
mov    DWORD PTR [eax+0x4],0x6f57206f;
mov    DWORD PTR [eax+0x8],0x21646c72;
mov    DWORD PTR [eax+0xc],0x4f48570a;
mov    DWORD PTR [eax+0x10],0x4a205349;
mov    DWORD PTR [eax+0x14],0x474e414a;
mov    WORD PTR [eax+0x18],0x217e;
mov    BYTE PTR [eax+0x1a],0x0;
```

eax에 ebp-0x400의 주소를 삽입합니다.  
eax가 가리키는 주소, 즉 ebp-0x400에 0x6c6c6548을 삽입합니다. ( 4byte )  
이를 eax+0x14까지 진행하고, 이후부터는 2byte 한 번, 1byte 한 번을 삽입합니다.  
이를 C언어로 번역하면?

```C
str[0] = 0x48;
str[1] = 0x65;
str[2] = 0x6c;
str[3] = 0x6c;

str[4] = 0x6f;
str[5] = 0x20;
str[6] = 0x57;
str[7] = 0x6f;

str[8] = 0x72;
str[9] = 0x6c;
str[10] = 0x64;
str[11] = 0x21;

str[12] = 0x0a;
str[13] = 0x57;
str[14] = 0x48;
str[15] = 0x4f;

str[16] = 0x49;
str[17] = 0x53;
str[18] = 0x20;
str[19] = 0x4a;

str[20] = 0x4a;
str[21] = 0x41;
str[22] = 0x4e;
str[23] = 0x47;

str[24] = 0x7e;
str[25] = 0x21;

str[26] = 0x0;
```

즉, str에 "Hello World!\nWHOIS JJANG~!\0"을 삽입한 것입니다.  

---

```assembly
sub    esp,0xc;
lea    eax,[ebp-0x400];
push   eax;
call   0x8049030;
add    esp,0x10;
mov    eax,0x0;
```

esp에서 0xc만큼의 공간을 할당합니다.  
eax에 ebp-400의 주소를 삽입합니다. 즉, str의 시작주소.  
eax를 push하여 스택에 삽입합니다.  
call 0x8049030은 puts 함수이기에, str의 데이터를 puts로 출력한다는 것을 알 수 있습니다.  

```C
puts(str);
```

모든 번역본을 정리하면 다음과 같습니다.  

```C
#include <stdio.h>

int main(){
	char str[0x3fc];
	for(int i=0x0; i<0xf9; i++)
		str[i] = 0x0;
	
	str = "Hello World!\nWHOIS JJANG~!\0";
	
	puts(str);
	
	return 0;
}
```

이번 과제 끝!
