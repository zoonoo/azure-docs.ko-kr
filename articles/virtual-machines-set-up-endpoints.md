<properties urlDisplayName="Set up endpoints" pageTitle="Azure에서 가상 컴퓨터에 끝점 설정" metaKeywords="Azure config setup, configuring vm connection" description="Learn how to setup communication with a virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/29/2014" ms.author="kathydav" />

#가상 컴퓨터에 끝점을 설정하는 방법

**참고**: 호스트 이름으로 직접 가상 컴퓨터에 연결하거나 프레미스 간 연결을 설정하려는 경우 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.

Azure에서 만든 모든 가상 컴퓨터는 개인 네트워크 채널을 사용하여 동일한 클라우드 서비스 또는 가상 네트워크에 있는 다른 가상 컴퓨터와 자동으로 통신할 수 있습니다. 그러나 인터넷이나 다른 가상 네트워크의 다른 리소스가 가상 컴퓨터로의 인바운드 네트워크 트래픽을 처리하려면 끝점이 필요합니다. 

관리 포털에서 가상 컴퓨터를 만드는 경우 원격 데스크톱, Windows PowerShell 원격, SSH(보안 셸) 등에 대해 이러한 끝점을 만들 수 있습니다. 가상 컴퓨터를 만든 후 필요에 따라 추가 끝점을 만들 수 있습니다. 끝점의 네트워크 ACL(액세스 제어 목록)에 대한 규칙을 구성하여 공용 포트로 들어오는 트래픽을 관리할 수도 있습니다. 이 문서에서는 이러한 두 작업을 수행하는 방법을 보여 줍니다.

각 끝점에는 공용 포트와 개인 포트가 있습니다.

- 개인 포트는 가상 컴퓨터가 해당 끝점에서 트래픽을 수신 대기하기 위해 내부적으로 사용합니다.

- 공용 포트는 Azure 부하 분산 장치가 외부 리소스의 가상 컴퓨터와 통신하기 위해 사용합니다. 끝점을 만든 후 네트워크 ACL(액세스 제어 목록)을 사용하여 공용 포트에서 들어오는 트래픽을 격리 및 제어하는 데 도움이 되는 규칙을 정의할 수 있습니다. 자세한 내용은 [네트워크 액세스 제어 목록 정보](http://go.microsoft.com/fwlink/p/?LinkId=303816)를 참조하세요.

관리 포털을 통해 끝점을 만드는 경우 이러한 끝점에 대한 포트 및 프로토콜의 기본값이 제공됩니다. 다른 모든 끝점의 경우 끝점을 만들 때 포트와 프로토콜을 지정합니다. 리소스는 TCP 또는 UDP 프로토콜을 사용하여 끝점에 연결할 수 있습니다. TCP 프로토콜에는 HTTP 및 HTTPS 통신이 포함됩니다.  

**중요**: 방화벽 구성은 원격 데스크톱 및 SSH(보안 셸)와 연결된 포트 및 대부분의 경우 Windows PowerShell 원격에 대해 자동으로 수행됩니다. 다른 모든 끝점에 지정된 포트의 경우 게스트 운영 체제의 방화벽에 대한 구성이 자동으로 수행되지 않습니다. 끝점을 만들 때 방화벽에서 끝점을 통해 라우팅하려는 트래픽을 허용하도록 해당 포트를 구성해야 합니다.

###끝점 만들기###

1. 아직 로그인하지 않은 경우 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. **가상 컴퓨터**를 클릭하고 구성하려는 가상 컴퓨터를 선택합니다.

3. **끝점**을 클릭합니다. 끝점 페이지에는 가상 컴퓨터에 대한 모든 끝점이 나열됩니다.

	![Endpoints](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	**추가**를 클릭합니다.

	**끝점 추가** 대화 상자가 나타납니다. 끝점 유형을 선택합니다. 새 부하 분산 집합을 만들려면 독립 실행형을 선택하여 집합의 첫 번째 끝점을 만듭니다.
	
5. **이름**에 끝점의 이름을 입력합니다.

6. 프로토콜에서 **TCP** 또는 **UDP**를 지정합니다.

7. **공용 포트** 및 **개인 포트**에서 사용할 포트 번호를 입력합니다. 이러한 포트 번호는 다를 수 있습니다. 공용 포트는 Azure 외부의 통신 진입점이며 Azure 부하 분산 장치에서 사용됩니다. 가상 컴퓨터에서 개인 포트와 방화벽 규칙을 사용하여 응용 프로그램에 맞추어 트래픽을 리디렉션할 수 있습니다.

8. 이 끝점이 부하 분산 집합의 첫 번째 끝점인 경우 **부하 분산 집합 만들기**를 클릭합니다. 그런 다음 **부하 분산 집합 구성** 페이지에서 이름, 프로토콜 및 프로브 세부 정보를 지정합니다. 부하 분산 집합에는 집합 상태를 모니터링할 수 있도록 프로브가 필요합니다. 자세한 내용은 [가상 컴퓨터 부하 분산](http://www.windowsazure.com/ko-kr/manage/windows/common-tasks/how-to-load-balance-virtual-machines/)을 참조하세요.  

9.	확인 표시를 클릭하여 끝점을 만듭니다.

	이제 **끝점** 페이지에 끝점이 표시됩니다.

	![Endpoint creation successful](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

###끝점에 대한 ACL 관리###

끝점에 대한 ACL을 추가, 수정 또는 제거하려면 다음 단계를 따르세요.

**참고**: 끝점이 부하 분산 집합의 일부인 경우 끝점에 대한 ACL의 변경 내용이 집합의 모든 끝점에 적용됩니다.

1. 아직 로그인하지 않은 경우 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. **가상 컴퓨터**를 클릭하고 구성하려는 가상 컴퓨터를 선택합니다.

3. **끝점**을 클릭합니다. 끝점 페이지에는 가상 컴퓨터에 대한 모든 끝점이 나열됩니다.

    ![ACL list](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

4. 목록에서 해당 끝점을 선택합니다. 

5. **ACL 관리**를 클릭합니다.

    **ACL 세부 정보 지정** 대화 상자가 나타납니다.

    ![Specify ACL details](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6. 목록의 행을 사용하여 ACL에 대한 규칙을 추가, 삭제 또는 편집합니다. 원격 서브넷 값은 규칙으로 허용하거나 거부할 수 있는 IP 주소 범위에 해당합니다. 규칙은 첫 번째 규칙에서 시작하여 마지막 규칙까지 순서대로 평가됩니다. 따라서 가장 덜 제한적인 규칙부터 가장 제한적인 규칙 순으로 규칙을 나열해야 합니다. 예제 및 자세한 내용은 [네트워크 액세스 제어 목록 정보](http://go.microsoft.com/fwlink/p/?LinkId=303816)를 참조하세요.

<!--HONumber=35_1-->
