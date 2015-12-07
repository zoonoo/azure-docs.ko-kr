<properties
	pageTitle="Linux VM에서 Apache Tomcat 설정 | Microsoft Azure"
	description="Linux를 실행하는 Azure VM(가상 컴퓨터)을 사용하여 Apache Tomcat7을 설정하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="ningk"/>

#Microsoft Azure를 사용하여 Linux 가상 컴퓨터에 Tomcat7을 설치하는 방법

Apache Tomcat(또는 간단히 Tomcat, 이전에는 Jakarta Tomcat이라고도 함)은 ASF(Apache Software Foundation)에서 개발한 오픈 소스 웹 서버 및 서블릿 컨테이너입니다. Tomcat은 Sun Microsystems의 Java Servlet 및 JSP(JavaServer Pages) 사양을 구현하며, Java 코드를 실행할 순수 Java HTTP 웹 서버 환경을 제공합니다. 가장 단순한 구성의 경우 Tomcat은 단일 운영 체제 프로세스로 실행됩니다. 이 프로세스에서는 JVM(Java Virtual Machine)을 실행합니다. 브라우저에서 Tomcat으로 전송되는 모든 HTTP 요청은 Tomcat 프로세스에서 별도의 스레드로 처리됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 가이드에서는 Linux 이미지에 tomcat7을 설치하여 Microsoft Azure에서 배포합니다.

다음 내용을 배웁니다.

-	Azure에서 가상 컴퓨터를 만드는 방법
-	tomcat7에 대해 가상 컴퓨터를 준비하는 방법
-	tomcat7을 설치하는 방법

독자에게 이미 Azure 구독이 있다고 가정합니다. 그렇지 않으면 [http://azure.microsoft.com](http://azure.microsoft.com)에서 무료 평가판을 등록할 수 있습니다. MSDN 구독이 있는 경우에는 [Microsoft Azure 특별 가격: MSDN, MPN 및 Bizspark 이점](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)을 참조하세요. Azure에 대한 자세한 내용은 [Azure 정의](http://azure.microsoft.com/overview/what-is-azure/)를 참조하세요.

이 항목에서는 사용자에게 tomcat 및 Linux에 대한 기본 실무 지식이 있다고 가정합니다.

##1단계: 이미지 만들기
이 단계에서는 Linux 이미지를 사용하여 Azure에 가상 컴퓨터를 만듭니다.

###1단계: SSH 인증 키 생성
SSH는 시스템 관리자에게 중요한 도구입니다. 그러나 사람이 식별할 수 있는 암호를 기반으로 액세스 보안을 구성하는 것은 바람직하지 않습니다. 악의적인 사용자는 사용자 이름 및 취약한 암호를 기반으로 하는 시스템에 침입할 수 있습니다.

하지만 원격 액세스를 열린 상태로 두고 암호에 대해 걱정하지 않을 수 있는 방법이 있습니다. 이 방법은 비대칭 암호화를 사용한 인증으로 구성됩니다. 사용자의 개인 키가 인증을 부여합니다. 사용자 계정을 잠가서 암호 인증을 완전히 허용하지 않을 수도 있습니다.

이 방법의 또 다른 이점은 서버마다 다른 암호를 사용하여 로그온할 필요가 없다는 점입니다. 모든 서버에서 개인 키를 사용하여 인증할 수 있으므로 여러 암호를 기억할 필요가 없습니다.

또한 이 방법을 사용하면 필요한 암호를 제공하여 로그인할 수도 있습니다.

SSH 인증 키를 생성하려면 다음 단계를 수행합니다.

1.	다음 위치에서 puttygen 다운로드 및 설치: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	PUTTYGEN.EXE를 실행합니다.
3.	**생성**을 클릭하여 키를 생성합니다. 프로세스에서 마우스를 창의 빈 영역 위로 이동하여 임의성을 늘릴 수 있습니다. ![][1]
4.	생성 프로세스 후 Puttygen.exe에서 생성된 키를 표시합니다. 예: ![][2]
5.	**키**에서 공개 키를 선택하여 복사하고 publicKey.pem 파일에 저장합니다. 저장된 공개 키의 파일 형식은 원하는 공개 키와 다르므로 **공개 키 저장**을 클릭하지 마세요.
6.	**개인 키 저장**을 클릭하고 privateKey.ppk 파일에 저장합니다.

###2단계: Azure 미리 보기 포털에서 이미지 만들기
[Azure Preview 포털](https://portal.azure.com/)의 작업 표시줄에서 **새로 만들기**를 클릭한 후 요구에 따라 Linux 이미지를 선택하여 이미지를 만듭니다. 다음 예제에서는 Ubuntu 14.04 이미지를 사용합니다. ![][3]

**호스트 이름**에서 사용자와 인터넷 클라이언트가 이 가상 컴퓨터에 액세스하는 데 사용할 URL의 이름을 지정합니다. DNS 이름의 마지막 부분(예: tomcatdemo)을 정의하면 Azure에서 URL을 tomcatdemo.cloudapp.net으로 생성합니다.

**SSH 인증 키**에서 puttygen에 의해 생성된 공개 키가 포함된 **publicKey.pem** 파일의 키 값을 복사합니다. ![][4]

필요에 따라 다른 설정을 구성하고 만들기를 클릭합니다.

##2단계: Tomcat7에 대해 가상 컴퓨터 준비
이 단계에서는 tomcat 트래픽에 대해 끝점을 구성한 다음 새 가상 컴퓨터에 연결합니다.
###1단계: 웹 액세스를 허용하도록 HTTP 포트 열기
Azure의 끝점은 프로토콜(TCP 또는 UDP)과 공용 및 개인 포트로 구성됩니다. 개인 포트는 가상 컴퓨터에서 서비스가 수신 대기하는 포트입니다. 공용 포트는 Azure 클라우드 서비스가 외부에서 인터넷 기반 수신 트래픽을 수신 대기하는 포트입니다.

tomcat이 수신 대기하는 기본 포트 번호는 TCP 포트 8080입니다. Azure 끝점에서 이 포트를 열면 사용자 및 다른 인터넷 클라이언트가 tomcat 페이지에 액세스할 수 있습니다.

1.	Azure Preview 포털에서 **찾아보기** -> **가상 컴퓨터**를 클릭한 다음 직접 만든 가상 컴퓨터를 클릭합니다. ![][5]
2.	가상 컴퓨터에 끝점을 추가하려면 **끝점** 확인란을 클릭합니다. ![][6]
3.	**추가**를 클릭합니다.  
	1.	**끝점**의 경우, 끝점에 끝점 이름을 입력하고 **공용 포트**에 80을 입력합니다.  

		80으로 설정하는 경우 tomcat에 액세스할 수 있게 해주는 URL에 포트 번호를 포함할 필요가 없습니다. 예: http://tomcatdemo.cloudapp.net.

		다른 값(예: 81)으로 설정한 경우에는 tomcat에 액세스하기 위한 URL에 포트 번호를 추가해야 합니다. 예: http://tomcatdemo.cloudapp.net:81/.
	2.	개인 포트에 8080을 입력합니다. 기본적으로 tomcat은 TCP 포트 8080에서 수신 대기합니다. tomcat의 기본 수신 대기 포트를 변경한 경우 개인 포트를 tomcat 수신 대기 포트와 같도록 업데이트해야 합니다. ![][7]

4.	**확인**을 클릭하여 가상 컴퓨터에 끝점을 추가합니다.



###2단계: 만든 이미지에 연결
가상 컴퓨터에 연결하기 위한 SSH 도구를 선택할 수 있습니다. 이 예제에서는 Putty를 사용합니다.

먼저 Azure Preview 포털에서 가상 컴퓨터의 DNS 이름을 가져옵니다. **찾아보기** -> **가상 컴퓨터** -> 가상 컴퓨터의 이름 -> **속성**을 클릭한 다음 **속성** 타일의 **도메인 이름** 필드를 확인합니다.

**SSH** 필드에서 SSH 연결의 포트 번호를 가져옵니다. 다음은 예제입니다. ![][8]

[여기](http://www.putty.org/)에서 Putty를 다운로드합니다

다운로드한 후 실행 파일 PUTTY.EXE를 클릭합니다. 가상 컴퓨터의 속성에서 가져온 호스트 이름과 포트 번호를 사용하여 기본 옵션을 구성합니다. 다음은 예제입니다. ![][9]

왼쪽 창에서 **연결** -> **SSH** -> **인증**을 클릭한 다음 **찾아보기**를 클릭하여 단계 1에서 puttygen에 의해 생성된 개인 키가 포함된 **privateKey.ppk** 파일의 위치를 지정합니다. 다음은 예제입니다. ![][10]

**열기**를 클릭합니다. 메시지 상자에 경고 메시지가 표시될 수도 있습니다. DNS 이름과 포트 번호를 올바르게 구성한 경우 **예**를 클릭합니다. ![][11]


다음이 표시되어야 합니다. ![][12]

1단계: 이미지 만들기에서 가상 컴퓨터를 만들 때 지정한 사용자 이름을 입력합니다. 다음과 유사한 출력이 표시됩니다. ![][13]





##3단계: 소프트웨어 설치
이 단계에서는 Java Runtime Environment, tomcat 및 기타 tomcat 구성 요소를 설치합니다.

###Java Runtime Environment
Tomcat은 Java로 작성되었습니다. 두 가지 종류의 JDK(Java Development Kit)(OpenJDK와 Oracle JDK)가 있으므로 원하는 종류를 선택할 수 있습니다.

>AZURE.NOTE: 두 JDK는 Java API의 클래스에 대한 거의 동일한 코드를 제공하지만 가상 컴퓨터에 대한 코드는 실제로 다릅니다. 라이브러리의 경우 OpenJDK는 개방형 라이브러리를 사용하는 반면, Oracle은 폐쇄형 라이브러리를 사용합니다. 그러나 Oracle JDK는 클래스가 더 많고 일부 수정된 버그가 있으며, Oracle JDK는 OpenJDK보다 더 안정적입니다.

다음 명령은 서로 다른 JDK를 다운로드합니다.

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	Oracle 웹 사이트에서 JDK를 다운로드하려면 다음을 실행합니다.  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	JDK 파일을 포함할 디렉터리를 만들려면 다음을 실행합니다.

		sudo mkdir /usr/lib/jvm  

-	JDK 파일을 /usr/lib/jvm/ 디렉터리에 추출하려면 다음을 실행합니다.

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	Oracle JDK를 기본 JVM으로 설정하려면 다음을 실행합니다.

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####테스트
다음과 같은 명령을 사용하여 Java Runtime Environment가 올바르게 설치되었는지 테스트할 수 있습니다.

	java -version  

open-jdk를 설치한 경우 다음과 같은 메시지가 표시되어야 합니다. ![][14]

oracle-jdk를 설치한 경우 다음과 같은 메시지가 표시되어야 합니다. ![][15]

###Tomcat7
다음 명령을 사용하여 tomcat7을 설치합니다.

	sudo apt-get install tomcat7  

tomcat7을 사용하지 않는 경우 이 명령의 적절한 변형을 사용합니다.

####테스트

tomcat7이 성공적으로 설치되었는지 확인하려면, tomcat 서버의 DNS 이름을 찾습니다(http://tomcatexample.cloudapp.net/은 이 문서의 예제 URL임). 다음과 같은 페이지가 표시되면 tomcat7이 올바르게 설치된 것입니다. ![][16]


###다른 Tomcat 구성 요소 설치
설치할 수 있는 다른 선택적 tomcat 구성 요소도 있습니다.

**sudo apt-cache search tomcat7** 명령을 사용하여 사용 가능한 모든 구성 요소를 확인할 수 있습니다. 다음 명령은 몇 가지 유용한 부분을 설치하는 예제입니다.

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##4단계: Tomcat 구성
이 단계에서는 tomcat을 관리합니다.
###tomcat7 시작 및 중지
tomcat7 서버를 설치하면 자동으로 시작됩니다. 다음 명령을 사용하여 수동으로 시작할 수도 있습니다.

	sudo /etc/init.d/tomcat7 start

tomcat7을 중지하려면 다음을 실행합니다.

	sudo /etc/init.d/tomcat7 stop

tomcat7의 상태를 보려면:

	sudo /etc/init.d/tomcat7 status

tomcat 서비스를 다시 시작하려면：

	sudo /etc/init.d/tomcat7 restart

###Tomcat 관리
다음 명령을 사용하여 Tomcat 사용자 구성 파일을 편집해 관리 자격 증명을 설정할 수 있습니다.

	sudo vi  /etc/tomcat7/tomcat-users.xml   

다음은 예제입니다. ![][17]

>AZURE.NOTE: 관리 사용자 이름에 대한 강력한 암호를 만듭니다.

이 파일을 수정한 후 다음 명령을 사용하여 tomcat7 서비스를 다시 시작해야 변경 내용이 적용됩니다.

	sudo /etc/init.d/tomcat7 restart  

브라우저를 열고 URL **http://<your tomcat server DNS name>/manager/html**을 입력합니다. 이 문서의 예제에 나와 있는 URL은 http://tomcatexample.cloudapp.net/manager/html입니다.

연결되면 다음과 유사한 페이지가 표시됩니다. ![][18]

##일반적인 문제

###인터넷에서 Tomcat 및 Moodle이 있는 가상 컴퓨터에 액세스할 수 없음

-	**증상** Tomcat이 실행되고 있지만 브라우저에서 Tomcat 기본 페이지를 볼 수 없습니다.
-	**가능한 근본 원인**   
	1.	Tomcat 수신 대기 포트가 Tomcat 트래픽에 대한 가상 컴퓨터 끝점의 개인 포트와 다릅니다.  

		공용 포트 및 개인 포트 끝점 설정을 검사하고 개인 포트가 Tomcat 수신 대기 포트와 같은지 확인합니다. 가상 컴퓨터에 대한 끝점 구성 지침은 1단계: 이미지 만들기를 참조하세요.

		Tomcat 수신 대기 포트를 확인하려면 /etc/httpd/conf/httpd.conf(Red Hat 릴리스) 또는 /etc/tomcat7/server.xml(Debian 릴리스)을 엽니다. 기본적으로 Tomcat 수신 대기 포트는 8080입니다. 다음은 예제입니다.

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Debian 또는 Ubuntu와 같은 가상 컴퓨터를 사용하는 경우 Tomcat 수신 대기 기본 포트(예: 8081)를 변경하려면 해당 OS의 포트도 열어야 합니다. 먼저 프로필을 엽니다.

			sudo vi /etc/default/tomcat7  

		그런 다음 마지막 줄에 주석 처리를 제거하고 "아니요"를 "예"로 변경합니다.

			AUTHBIND=yes

	2.	방화벽에서 Tomcat 수신 대기 포트를 사용하지 않도록 설정했습니다.

		로컬 호스트에서 Tomcat 기본 페이지만 볼 수 있는 경우 Tomcat이 수신 대기하는 포트가 방화벽에서 차단되었을 가능성이 큽니다. w3m 도구를 사용하여 웹 페이지를 찾을 수 있습니다. 다음 명령은 w3m을 설치하고 Tomcat 기본 페이지로 이동합니다.

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**해결 방법**
	1. Tomcat 수신 대기 포트가 가상 컴퓨터의 트래픽에 대한 끝점의 개인 포트와 다른 경우 개인 포트를 Tomcat 수신 대기 포트와 동일하게 변경해야 합니다.   

	2.	방화벽/iptables로 인해 문제가 발생한 경우 /etc/sysconfig/iptables에 다음 줄을 추가합니다.

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		두 번째 줄은 https 트래픽에만 필요합니다.

		이 줄은 전체적으로 액세스를 제한하는 다음과 같은 줄 위에 있어야 합니다.

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		iptables를 다시 로드하려면 다음 명령을 실행합니다.

			service iptables restart  

		CentOS 6.3에서 테스트되었습니다.

###/var/lib/tomcat7/webapps/에 프로젝트 파일을 업로드할 때 권한이 거부됨  

-	**증상** SFTP 클라이언트(예: FileZilla)를 사용하여 가상 컴퓨터에 연결한 다음 /var/lib/tomcat7/webapps/로 이동하여 사이트를 게시하는 경우 다음과 유사한 오류 메시지가 표시됩니다.  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**가능한 근본 원인** /var/lib/tomcat7/webapps 폴더에 액세스할 수 있는 권한이 없습니다.
-	**해결 방법** 루트 계정에서 권한을 받아야 합니다. 해당 폴더의 소유권을 루트에서 컴퓨터를 프로비전할 때 사용한 사용자 이름으로 변경할 수 있습니다. 다음은 azureuser 계정 이름을 사용한 예제입니다.  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	-R 옵션을 사용하여 디렉터리 내의 모든 파일에 대해서도 권한을 적용합니다.

	이 명령은 디렉터리에서도 작동합니다. -R 옵션은 디렉터리 내의 모든 파일과 디렉터리에 대해 권한을 변경합니다. 다음은 예제입니다.

		sudo chown -R username:group directory  

	이 명령은 디렉터리 내의 모든 파일과 디렉터리 및 디렉터리 자체의 소유권(사용자 및 그룹 둘 다)을 변경합니다.

	다음 명령은 폴더 디렉터리의 사용 권한만 변경하고 디렉터리 내의 파일과 폴더는 그대로 유지합니다.

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png

<!---HONumber=AcomDC_1125_2015-->