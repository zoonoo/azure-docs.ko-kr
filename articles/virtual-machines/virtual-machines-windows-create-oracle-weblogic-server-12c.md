<properties
	pageTitle="Oracle WebLogic Server 12c VM 만들기 | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 Microsoft Azure에서 Windows Server 2012를 실행하는 Oracle WebLogic Server 12c 가상 컴퓨터를 만듭니다."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터 만들기

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

다음 예제에서는 이전에 만들었으며 Azure의 Windows Server 2012에서 실행되는 Oracle WebLogic Server 12c를 설치한 VM에서 실행되는 WebLogic Server 12c를 만드는 방법을 보여 줍니다.

##Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터를 구성하려면

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2.	**가상 컴퓨터**를 클릭합니다.

3.	로그인할 가상 컴퓨터의 이름을 클릭합니다.

4.	**Connect**를 클릭합니다.

5.	가상 컴퓨터에 연결하려면 필요에 따라 메시지에 응답합니다. 관리자 이름 및 암호를 묻는 메시지가 표시되면 가상 컴퓨터를 만들 때 제공한 값을 사용하세요.

6.	**WebLogic 플랫폼 빠른 시작** 대화 상자에서 **WebLogic Server 시작**을 클릭합니다. **WebLogic 플랫폼 빠른 시작** 대화 상자가 아직 열려 있지 않으면 **Windows 시작**을 클릭하고 **WebLogic Server 도메인용 관리 서버 시작**을 입력한 다음 **WebLogic Server 도메인용 관리 서버 시작** 아이콘을 클릭하여 엽니다.

7.	**시작** 대화 상자에서 **새 WebLogic 도메인 만들기**를 선택하고 **다음**을 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image10.png)

8.	**도메인 소스 선택** 대화 상자에서 기본값을 적용하고 **다음**을 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image11.png)

9.	**도메인 이름 및 위치 지정** 대화 상자에서 기본값을 적용하고 **다음**을 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image12.png)

10.	**관리자의 사용자 이름 및 암호 구성** 대화 상자에서 다음을 수행합니다.

	1.	[선택 사항] 사용자 이름을 **weblogic**에서 선택한 값으로 변경합니다.

	2.	WebLogic 서버 관리자의 암호를 지정하고 확인합니다.

	3.	**다음**을 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image13.png)

11.	**서버 시작 모드 및 JDK 구성** 대화 상자에서 **프로덕션 모드**를 선택하고 사용할 수 있는 JDK(또는 원하는 경우 JDK 브라우저)를 선택한 후 **다음**을 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image14.png)

12.	**선택적 구성 선택** 대화 상자에서 옵션을 선택하지 말고 **다음**을 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image15.png)

13.	**구성 요약** 대화 상자에서 **만들기**를 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image16.png)

14.	**도메인 만들기** 대화 상자에서 **관리 서버 시작**을 선택하고 **완료**를 클릭합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image17.png)

15.	**startWebLogic.cmd**의 명령 프롬프트가 시작됩니다. 메시지가 표시되면 WebLogic 사용자 이름과 암호를 제공합니다.

##Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터에 응용 프로그램 설치하려면
1.	가상 컴퓨터에 로그인된 상태에서 http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war에 있는 shoppingcart.war 예제를 로컬에 복사합니다. 예를 들어 **c:\\mywar**라는 폴더를 만들고 http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war에 있는 WAR를 **c:\\mywar**에 저장합니다.

2.	**WebLogic Server 관리 콘솔**, http://localhost:7001/console을 엽니다. 메시지가 표시되면 WebLogic 사용자 이름과 암호를 제공합니다.

3.	**WebLogic Server 관리 콘솔**에서 **잠금 및 편집**을 클릭하고 **배포**를 클릭한 다음 **설치**를 클릭합니다.

4.	**경로**에 **c:\\myway\\shoppingcart.war**을 입력합니다.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image18.png)

	**다음**을 클릭합니다.

5.	**이 배포를 응용 프로그램으로 설치**를 선택하고 **다음**을 클릭합니다.

6.	**마침**을 클릭합니다.

7.	**WebLogic Server 관리 콘솔**에서 **저장**을 클릭한 다음 **변경 내용 활성화**를 클릭합니다.

8.	**배포**를 클릭하고 **shoppingcart**를 선택한 다음 **시작**, **모든 요청 서비스**를 차례로 클릭합니다. 확인하라는 메시지가 나타나면 **예**를 클릭합니다.

9.	로컬에서 실행 중인 쇼핑 카트 응용 프로그램을 보려면 브라우저에서 <http://localhost:7001/shoppingcart>를 엽니다.

10.	가상 컴퓨터의 끝점 만들기:

	1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

	2.	**찾아보기**를 클릭합니다.

	3.	**가상 컴퓨터**를 클릭합니다.

	4.	가상 컴퓨터를 선택합니다.

	5.	**설정**을 클릭합니다.

	6.	**Endpoints**를 클릭합니다.

	7.	**추가**를 클릭합니다.

	8.	끝점의 이름을 지정합니다.

		1. 프로토콜에 **TCP**를 사용합니다.

		2. 공용 포트에 **80**을 사용합니다.

		3. 개인 포트에 **7001**을 사용합니다.

	9.	나머지 옵션은 그대로 둡니다.

	10. **확인**을 클릭합니다.

11.	7001 포트에 대한 방화벽을 통해 인바운드 연결을 허용합니다.

	1.	가상 컴퓨터에 로그인합니다.

	2.	**Windows 시작**을 클릭하고 **고급 보안이 포함된 Windows 방화벽**을 입력한 다음 **고급 보안이 포함된 Windows 방화벽** 아이콘을 클릭합니다. 이렇게 하면 **고급 보안이 포함된 Windows 방화벽** 관리 콘솔이 열립니다.

	3.	방화벽 관리 콘솔의 왼쪽 창에서 **인바운드 규칙**을 클릭한 다음(**인바운드 규칙**이 보이지 않는 경우 왼쪽 창의 맨 위에 있는 노드를 확장함) 오른쪽 창에서 새 규칙을 클릭합니다.

	4.	**규칙 유형**에 대해 **포트**를 선택하고 **다음**을 클릭합니다.

	5.	**프로토콜 및 포트**는 **TCP**를 선택하고 **특정 로컬 포트**를 선택하여 포트에 대해 **7001**을 입력하고 **다음**을 클릭합니다.

	6.	**연결 허용**을 선택하고 **다음**을 클릭합니다.

	7.	규칙이 적용되는 프로필에 대해 기본값을 적용하고 **다음**을 클릭합니다.

	8.	규칙 이름 및 설명(선택 사항)을 지정하고 **마침**을 클릭합니다.

12.	인터넷에서 실행 중인 쇼핑 카트 응용 프로그램을 보려면 브라우저에서 `http://<<unique_domain_name>>/shoppingcart` 형식의 URL을 엽니다. **가상 컴퓨터**를 클릭한 다음 Oracle WebLogic Server를 실행하기 위해 사용 중인 가상 컴퓨터를 선택하여 [Azure 포털](https://ms.portal.azure.com/) 내에서 <<*unique\_domain\_name*>>의 값을 확인할 수 있습니다.


##추가 리소스
지금까지 Oracle WebLogic Server를 실행 중인 가상 컴퓨터를 설정했습니다. 추가 정보는 다음 항목을 참조하세요.

-	[Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](virtual-machines-windows-classic-oracle-considerations.md)

-	[Oracle WebLogic Server 제품 설명서](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Microsoft Azure에서 Linux를 사용하는 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Azure용 Oracle 가상 컴퓨터 이미지](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0601_2016-->