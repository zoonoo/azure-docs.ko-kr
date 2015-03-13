<properties 
	pageTitle="SSH를 사용하여 Azure의 Linux 가상 컴퓨터에 연결" 
	description="SSH 키를 생성하여 Azure에서 Linux 가상 컴퓨터에 사용하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="szarkos"/>

#Azure에서 Linux와 함께 SSH를 사용하는 방법

현재 버전의 Azure 관리 포털은 X509 인증서에 캡슐화된 SSH 공개 키만 허용합니다. SSH 키를 생성하고 Azure에 사용하려면 아래 단계를 따르세요.

## Linux에서 Microsoft Azure 호환 키 생성 ##

1. 필요한 경우  `openssl` 유틸리티를 설치합니다.

	**CentOS/Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES 및 openSUSE**

		# sudo zypper install openssl


2.  `openssl`을 사용하여 2048비트 RSA 키 쌍으로 X509 인증서를 생성합니다.  `openssl`에서 확인하는 몇 가지 질문에 대답합니다(또는 비워 둘 수도 있음). 이러한 필드의 콘텐츠는 플랫폼에서 사용되지 않습니다.

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	개인 키의 사용 권한을 변경하여 보안을 설정합니다.

		# chmod 600 myPrivateKey.key

4.	Linux 가상 컴퓨터를 만드는 동안  `myCert.pem`을 업로드합니다. 프로비전 프로세스에서 가상 컴퓨터의 지정된 사용자에 대한  `authorized_keys` 파일에 이 인증서의 공개 키를 자동으로 설치합니다.

5.	API를 직접 사용하고 관리 포털을 사용하지 않으려는 경우 다음 명령을 사용하여  `myCert.pem`을  `myCert.cer`(DER 인코딩된 X509 인증서)로 변환합니다.

		# openssl  x509 -outform der -in myCert.pem -out myCert.cer


## 기존 OpenSSH 호환 키에서 키 생성
이전 예제에서는 Azure와 함께 사용할 새 키를 만드는 방법을 설명합니다. 사용자에게 기존의 OpenSSH 호환 공용 키 및 개인 키 쌍이 이미 있고 동일한 키를 Microsoft Azure에 사용하려는 경우도 있을 수 있습니다.

OpenSSH 개인 키는  `openssl` 유틸리티를 통해 직접 읽을 수 있습니다. 다음 명령은 기존 SSH 개인 키(아래 예제에서는 id_rsa)를 사용하고 Azure에 필요한  `.pem` 공개 키를 만듭니다.

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** 파일은 Azure에 Linux 가상 컴퓨터를 프로비전하는 데 사용할 수 있는 공개 키입니다. 프로비전하는 동안  `.pem` 파일이  `openssh` 호환 공개 키로 변환되고 `~/.ssh/authorized_keys`.에 저장됩니다.


## Linux에서 Microsoft Azure 가상 컴퓨터에 연결

1. 일부 경우에 Linux 가상 컴퓨터의 SSH 끝점이 기본 포트인 22 이외의 포트에 대해 구성될 수 있습니다. 관리 포털("SSH 세부 정보")의 VM에 대한 대시보드에서 올바른 포트 번호를 찾을 수 있습니다.

2.	 `ssh`를 사용하여 Linux 가상 컴퓨터에 연결합니다. 처음 로그인할 때 호스트의 공개 키 지문을 허용하라는 메시지가 표시됩니다.

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	(옵션)  `myPrivateKey.key`를 `~/.ssh/id_rsa`로 복사할 수 있습니다. 그러면 `-i` 옵션을 사용하지 않아도 OpenSSH 클라이언트가 이 파일을 자동으로 선택할 수 있습니다.

## Windows에서 OpenSSL 가져오기 ##
### msysgit 사용 ###

1.	다음 위치에서 msysgit를 다운로드하여 설치합니다. [http://msysgit.github.com/](http://msysgit.github.com/)
2.	설치된 디렉터리(예: c:\msysgit\msys.exe)에서  `msys`를 실행합니다.
3.	 `cd bin`을 입력하여  `bin` 디렉터리로 변경합니다.

###Windows용 GitHub 사용###

1.	다음 위치에서 Windows용 GitHub를 다운로드하여 설치합니다. [http://windows.github.com/](http://windows.github.com/)
2.	시작 메뉴 > 모든 프로그램 > GitHub, Inc에서 Git 셸을 실행합니다.

###cygwin 사용###

1.	다음 위치에서 Cygwin을 다운로드하여 설치합니다. [http://cygwin.com/](http://cygwin.com/)
2.	OpenSSL 패키지와 모든 종속성이 설치되었는지 확인합니다.
3.	 `cygwin`을 실행합니다.

## Windows에 개인 키 만들기 ##

1.	위의 지침 집합 중 하나를 따라  `openssl.exe`를 실행합니다.
2.	다음 명령을 입력합니다.

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	화면이 다음과 같이 표시됩니다.

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	표시되는 질문에 대답합니다.
5.	두 개의 파일, 즉  `myPrivateKey.key`와  `myCert.pem`을 만들었습니다.
6.	API를 직접 사용하고 관리 포털을 사용하지 않으려는 경우 다음 명령을 사용하여  `myCert.pem`을  `myCert.cer`(DER 인코딩된 X509 인증서)로 변환합니다.

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Putty용 PPK 만들기 ##

1. 다음 위치에서 Puttygen을 다운로드하여 설치합니다. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen은 앞에서 만든 개인 키(`myPrivateKey.key`)를 읽지 못할 수도 있습니다. 이 경우 다음 명령을 실행하여 해당 키를 Puttygen이 이해할 수 있는 RSA 개인 키로 변환합니다.

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	위의 명령을 실행하면 새 개인 키 myPrivateKey_rsa가 생성됩니다.

3.  `puttygen.exe`를 실행합니다.

4. 메뉴: 파일 > 개인 키 로드를 클릭합니다.

5. 위에서 이름을  `myPrivateKey_rsa`로 지정한 개인 키를 찾습니다. **모든 파일(\*.\*)**을 표시하도록 파일 필터를 변경해야 합니다.

6. **열기**를 클릭합니다. 다음과 같은 프롬프트가 표시됩니다.

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. **확인**을 클릭합니다.

8. 아래 스크린샷에서 강조 표시된 **개인 키 저장**을 클릭합니다.

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. 파일을 PPK로 저장합니다.


## Putty를 사용하여 Linux 컴퓨터에 연결 ##

1.	다음 위치에서 putty를 다운로드하여 설치합니다. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	putty.exe를 실행합니다.
3.	관리 포털의 IP를 사용하여 호스트 이름을 입력합니다.

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	**열기**를 선택하기 전에 연결 > SSH > 인증 탭을 클릭하여 키를 선택합니다. 내용을 입력할 필드는 아래 스크린샷을 참조하세요.

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	**열기**를 클릭하여 가상 컴퓨터에 연결합니다.


<!--HONumber=42-->
