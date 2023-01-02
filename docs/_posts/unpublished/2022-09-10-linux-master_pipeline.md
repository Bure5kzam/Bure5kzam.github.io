---
layout: single
title: 리눅스 데이터 스트림
category : linuxMaster
---

# Overview
데이터 스트림은 장치나 프로그램간에 데이터를 전달할 때 보관하는 버퍼를 의미합니다.

명령어를 실행했을 때도 데이터 스트림이 이용되는데, 예를들어 `echo Hi` 명령어를 실행하면

1. 표준 입력 스트림 stdin으로 'Hi'가 전달
2. echo에서 stdin에 있는 'Hi'를 읽음
3. 표준 출력 스트림 (stdout)으로 'Hi' 데이터 전달

위 과정을 통해 화면에 HI가 보이게 됩니다.

표준 입출력에 사용되는 데이터 스트림 버퍼는 `stdin`, `stdout`, `stderr` 가 있습니다.

[터미널에서 프로그램이 실행되면 세 데이터 스트림 버퍼 파일이 생성됩니다.](https://www.geeksforgeeks.org/input-output-redirection-in-linux/)
stdin : 쉘에 입력된 명령어가 보관되는 버퍼. 명령어에서 참고하게됨.
stdout : 명령어 실행 결과가 저장되는 버퍼
stderr : 에러 구문이 저장되는 버퍼

# Redirection
리디렉션은 출력이나 입력을 stdin이나 stdout이 아닌 다른 데이터 스트림으로 전달할 때 사용합니다.

리디렉션은 세가지 유형이 있습니다.

## Overwrite
데이터를 파일에 덮어씁니다.

 - `>` 좌측 명령어의 standard output을 우측 파일로 전달
 - `<` 좌측 명령어의 standard input으로 우측 파일의 내용을 전달

```console
[root@localhost ~]# ls gcc
code.c  code.o

[root@localhost ~]# ls gcc > result.txt 
                    # stdout으로 나오는 ls 명령어 결과를 result.txt의 stdin으로 리디렉션 한다.

[root@localhost ~]# cat result.txt 
code.c
code.o

[root@localhost ~]# cat < result.txt
code.c
code.o
```

명령어 실행에 실패할 경우 기본적으로 스크린에 에러 메세지를 출력하는데, 이는 stdout이 아니라 stderr 스트림을 통해 전달됩니다.

실제로 쉘에서 에러메세지를 출력해보면 `>` 리디렉션 여부와 관련없이 출력됩니다.

```console
[root@localhost ~]# cat aa.txt > result.txt
                  # 존재하지 않는 파일 조회
                  
cat: aa.txt: 그런 파일이나 디렉터리가 없습니다
                  # 에러가 파일로 전달되지 않고 스크린에 나타남
```

stderr도 stdout처럼 리디렉션할 수 있습니다.
숫자 + 파이프라인 기호`>`를 조합하면 되는데 1은 stdout, 2는 stderr입니다.

아래는 stdout과 stderr를 구분서 처리하는 예시입니다.
존재하지 않는 디렉토리인 noDir과 존재하는 gcc를 ls명령어로 조회합니다.

```console
[root@localhost ~]# ls noDir
                  1-1. noDir는 존재하지 않음
ls: cannot access noDir: 그런 파일이나 디렉터리가 없습니다

[root@localhost ~]# ls gcc
                  1-2. gcc 폴더는 존재
code.c  code.o
```

두 디렉토리를 동시에 조회하면 noDir은 에러가 나오고 gcc는 결과가 나옵니다.

``` console
[root@localhost ~]# ls noDir gcc
                  1. 
ls: cannot access noDir: 그런 파일이나 디렉터리가 없습니다
gcc:
code.c  code.o

```

`>`은 stdout만 하는 명령어입니다. stderr는 리디렉션 되지않고 화면에 출력됩니다.

```console
[root@localhost ~]# ls noDir gcc > result.txt
                  3-1. stdout을 리디렉션하면 화면에 에러만 나타남
ls: cannot access noDir: 그런 파일이나 디렉터리가 없습니다

[root@localhost ~]# cat result.txt 
                  3-2. 파일에는 결과만 저장됨.
gcc:
code.c
code.o
```

`2>`로 stderr를 리디렉션할 수 있습니다.

```console
[root@localhost ~]# ls noDir gcc 2> result.txt
                  4-1. stderr를 리디렉션하면 화면에 결과만 나타남
gcc:
code.c  code.o

[root@localhost ~]# cat result.txt 
                  4-2. 파일에는 에러만 저장됨
ls: cannot access noDir: 그런 파일이나 디렉터리가 없습니다

```

`>`과 `1>`은 동작이 같습니다.

```console
[root@localhost ~]# ls noDir gcc 1> result.txt
ls: cannot access noDir: 그런 파일이나 디렉터리가 없습니다
You have new mail in /var/spool/mail/root
[root@localhost ~]# cat result.txt 
gcc:
code.c
code.o
```

## Append

데이터를 파일의 끝부분에 추가할 때 사용합니다.

 - `>>`, `<<`

아래는 `>>` 기호로 리디렉션을 수행한 예시입니다.

```console
[root@localhost ~]# cat aa.txt
alian
bike
chio
dead
escape
fire

[root@localhost ~]# cat >> aa.txt 
                  # stdout으로 출력되어야 할 내용이 파일의 stdin으로 리디렉션
new alian
new bike
new chio

[root@localhost ~]# cat aa.txt
alian
bike
chio
dead
escape
fire
new alian
new bike
new chio
```

동작순서는
1. cat 명령어 실행
2. 사용자 입력 대기
3. 사용자 입력
4. cat 출력결과를 aa.txt의 stdin으로 리디렉션
임을 확인할 수 있습니다.

## Merge
스트림 데이터를 병합합니다. 예를들어 stdout과 stderr의 결과물을 합칠 수 있습니다.
  - `p>&q` : p 데이터 스트림의 결과를 q와 병합합니다.
  - `p<&q` : p 데이터 스트림의 입력을 q와 병합합니다.

# Pipeline

파이프라인은 리디렉션의 한 형태로, 두 개 이상의 명령어나 파일을 결합하는데 사용됩니다.

파이프라인을 사용하면 출력 결과를 다른 명령어의 입력값으로 전달하거나, 파일로 생성 할 수 있습니다.

데이터는 단방향으로 왼쪽에서 오른쪽으로 전달됩니다.

```console
[root@localhost ~]# ls -al
                  # Only one command
합계 84
dr-xr-x---.  7 root root  4096  9월  7 20:26 .
dr-xr-xr-x. 17 root root   224  4월  2 14:54 ..
-rw-------.  1 root root 14017  9월  7 03:07 .bash_history
-rw-r--r--.  1 root root    18 12월 29  2013 .bash_logout
-rw-r--r--.  1 root root   176 12월 29  2013 .bash_profile
-rw-r--r--.  1 root root   176 12월 29  2013 .bashrc
-rw-r--r--.  1 root root   100 12월 29  2013 .cshrc
-rw-------.  1 root root   795  9월  6 23:12 .lesshst
drwxr-----.  3 root root    19  4월 30 12:55 .pki
-rw-r--r--.  1 root root   129 12월 29  2013 .tcshrc
drwxr-xr-x.  5 root root  4096  9월  7 18:43 .vscode-server

[root@localhost ~]# ls -al | head -3
                  # Use with pipeline
합계 84
dr-xr-x---.  7 root root  4096  9월  7 20:26 .
dr-xr-xr-x. 17 root root   224  4월  2 14:54 ..
```

# Reference
[Shell script, stdin, stdout, geeksforgeeks](https://www.geeksforgeeks.org/shell-scripting-standard-input-output-and-error/?ref=rp)


[Input Output Redirection in Linux, geeksforgeeks](https://www.geeksforgeeks.org/input-output-redirection-in-linux/)


[Pipeline in Unix and Linux](https://www.geeksforgeeks.org/piping-in-unix-or-linux/)