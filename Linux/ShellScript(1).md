# ShellScript(1)

[이고잉님의 Linux 강좌](https://www.youtube.com/watch?v=cXnVygkAg4I&list=PLuHgQVnccGMBT57a9dvEtd6OuWpugF9SH&index=21) 학습한 내용을 정리함


어떤 파일들을 특정 폴더를 생성해서 복사해야하는 일이 주기적으로 있다고 가정했을 때 Shell script를 활용한다.

![일반명령어](/Linux/img/bash.png)

위의 과정을 반복적으로 할 필요없이 shell sciprt인 bash를 사용하여 반복적인 과정을 해결 할 수 있다.

`vim` 명령어로 backup 파일을 생성 및 연다.

~~~bash
#!/bin/bash
if ! [ -d bak ]; then
    mkdir bak
fi
cp *.log bak
~~~
`#!/bin/bash` : 스크립트 파일을 bash로 실행시킨다는 것을 의미
`-d bak` : bak 디렉토리를 의미(디렉토리를 명시할 때는 -d 옵션을 넣어야함)
`then` : 조건식이 만족될때 수행되는 명령어 
`fi` : 조건문 종료

위의 쉘스크립트는 bak 디렉토리가 존재하지 않으면 mkdir bak 명령어를 수행하며, 현재 .log 로 끝나는 확장자 파일들을 bak 디렉토리로 복사하는 일을 수행한다.

![백업파일 생성](/Linux/img/backup파일생성.png)
vim 편집기로 backup 파일이 생성된 것을 확인 할 수 있다. 

`./backup`명령어로 backup파일을 실행시키면 "Permission denied" 권한 거부가 발생한다. 

![접근거부](/Linux/img/접근거부이미지.png)
backup 파일을 실행 할 수 있는 권한을 부여하려면,
`chmod +x backup` 명령어를 수행한다.

![권한성공](/Linux/img/권한생성.png)

권한 과정을 살펴보면, `-rw-rw-r--`에서 `rwxrwxr-x`로 바뀐걸 알 수 있다. 

`./backup`을 실행하면, bak 디렉토리 파일이 없을 경우 파일을 만들어 현재 디렉토리의 .log 파일을 복사가 성공된다. 
