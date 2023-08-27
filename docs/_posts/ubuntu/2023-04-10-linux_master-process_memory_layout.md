---
title: 우분투 프로세스 메모리 레이아웃
category : ubuntu
author: Bure5kzam
tags: [linux, process]
summary: 프로세스의 메모리 레이아웃 섹션을 설명합니다.
---

프로세스의 메모리 레이아웃 섹션을 예시와 함께 설명합니다.

---
![메모리 레이아웃](assets/img/linux/프로세스%20메모리%20레이아웃.png)


## Stack의 예시

Stack 공간은 일시적으로 사용하고 사라지는 데이터들을 저장하는 공간입니다.

필요없는 데이터는 즉시 삭제하는 공간이며, 선입선출, stack 구조로 되어있습니다.

소스코드 예시와 함께 설명하자면, Say 함수가 실행된 후 삭제되고 난 후 다음과 같은 데이터들은 삭제됩니다.

 - Function의 argument로 주어진 값
   - Say 함수 인자로 제공된 "amy"
 - Funciton 호출 후 복귀할 주소
   - Say("amy") 함수 실행 후 복귀해야 할 main 내 복귀 주소
 - Function 내 사용된 로컬 변수
   - Say 함수 안에서 정의된 "Hello"

```c
#include <stdio.h>
#include <string.h>
int main() {
    Say("amy");
}

void Say(string msg) {
    string proceed = "Hello, ";
    printf("%s %s\n", proceed, msg);
}

```

이처럼 한번 실행되고 나면 필요없는 데이터들은 stack 에 저장되었다가 실행 후 바로바로 삭제됩니다.

## Heap의 예시

Heap은 런타임 중에 동적으로 할당받은 메모리 공간입니다.

정적 할당된 일반 변수들은 필요한 메모리 크기가 정해져있지만, 빌드된 후에서야 크기를 확인할 수 있는 데이터들도 있습니다.

아래 예시처럼 I/O로 입력받는 데이터는 필요한 저장공간 크기가 확실치 않기 때문에 Heap 공간에서 따로 관리합니다.


```c
#include <stdio.h>
#include <string.h>
int main() {
    int num;
    scanf ("%d ", &num);

    arr = (int**) malloc ( sizeof(int*) * num );
for(int i=0; i<num; i++){
    arr[i] = (int*) malloc ( sizeof(int) * num );
}
    for(int i = 0 ; i < num; i++) {
        for(int j = 0 ; j < num; j++) {
            scanf("%d ", arr[i][j]);
        }
    }
}

void Say(string msg) {
    string proceed = "Hello, ";
    printf("%s %s\n", proceed, msg);
}

```

편의상 메모리 공간을 설명할 때 Heap 공간의 크기는 제약이 없다고도 하며, 상단 그림에서 Heap과 Stack 사이를 화살표로 표시하는 이유도 둘의 데이터가 쌓이면 결국 마주칠 수 있다는 뜻입니다.

