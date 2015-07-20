<properties title="Creating an Oracle WebLogic Server 12c cluster in Azure" pageTitle="Azure에서 Oracle WebLogic Server 12c 클러스터 만들기" description="Microsoft Azure에서 Oracle WebLogic Server 12c 클러스터를 만드는 예제를 단계별로 설명합니다." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Azure에서 Oracle WebLogic Server 12c 클러스터 만들기
다음 예제에서는 Windows Server 2012에서 실행되는 Microsoft에서 제공한 Oracle WebLogic Server 12c 이미지에 따라 Azure에서 Oracle WebLogic Server 클러스터를 만드는 방법을 보여줍니다.

WebLogic Server 클러스터의 각 인스턴스는 동일한 버전의 Oracle WebLogic Server가 실행 중이어야 합니다. 이 예제에서는 Oracle WebLogic Server 12c Enterprise Edition을 사용합니다.

##클러스터에서 사용할 가상 컴퓨터 만들기
클러스터 관리 서버로 사용할 가상 컴퓨터를 만들고 클러스터의 일부로 사용하도록 가상 컴퓨터를 추가로 만듭니다.

### 관리 서버로 사용할 가상 컴퓨터 만들기

Azure에서 사용할 수 있는 **Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012** 이미지를 사용하여 가상 컴퓨터를 만듭니다. 이 가상 컴퓨터를 만드는 단계를 [Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터 만들기](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article)에서 찾을 수 있습니다. 이 자습서의 목적을 위해 가상 컴퓨터 **MYVM1 ADMIN**을 호출합니다. VM의 가상 네트워크 이름을 기록해두십시오. 클러스터에 추가할 추가 VM을 만들 때 사용합니다. (VM 이름 및 가상 네트워크 이름은 Azure에서 고유하기만 하면 무엇이든 가능합니다.)

### 클러스터에서 사용할 관리 가상 컴퓨터 만들기

Azure에서 사용할 수 있는 Oracle WebLogic Server 12c 이미지를 사용하여 관리 서버에서 관리될 추가 가상 컴퓨터를 만듭니다. 이 자습서의 목적을 위해 추가 가상 컴퓨터의 이름을 **MYVM2-MANAGED** 및 **MYVM3-MANAGED**로 지정합니다. 이 가상 컴퓨터를 만드는 단계는 다음 변경사항이 필요한 점을 *제외하고* [Azure에서 Oracle WebLogic Server 12c 가상 컴퓨터 만들기](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article)에서 찾을 수 있습니다.

-  가상 컴퓨터를 만들 때 새 가상 네트워크를 만들지 마십시오. 특히 **선택적 구성 > 가상 네트워크** 대화 상자에서 기본인 **새 가상 네트워크 만들기** 대신, 관리 서버 VM에 대해 만들어진 가상 네트워크를 선택합니다. 예: 관리 서버를 만드는 동안 **EXAMPLE**이라는 가상 네트워크를 만든 경우 관리 클러스터 VM을 만들 때 **EXAMPLE**을 선택합니다.

##도메인 만들기

1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

2. **가상 컴퓨터**를 클릭합니다.

3. 클러스터 관리 서버를 만든 가상 컴퓨터의 이름을 클릭합니다(예: **MYVM1-ADMIN**).

4. **연결**을 클릭합니다.

5. 가상 컴퓨터에 연결하려면 필요에 따라 메시지에 응답합니다. 관리자 이름 및 암호를 묻는 메시지가 표시되면 가상 컴퓨터를 만들 때 제공한 값을 사용하십시오.

6. **Fusion 미들웨어 구성 마법사** 대화 상자의 **1페이지**에서 **새 도메인 만들기**를 클릭한 후 **다음**을 클릭합니다. (**Fusion 미들웨어 구성 마법사** 대화 상자가 아직 열려있지 않은 경우, **Windows 시작**을 클릭하고 **구성 마법사**를 입력한 다음 **구성 마법사** 아이콘을 클릭하여 엽니다.)

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. **Fusion 미들웨어 구성 마법사** 대화 상자의 **2페이지**에서 **새 도메인 만들기**를 클릭한 후 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. **Fusion 미들웨어 구성 마법사** 대화 상자의 **3페이지**에서:

	1. [옵션] **weblogic**에서 선택한 값으로 사용자 이름을 변경합니다.
	
	2. WebLogic 서버 관리자의 암호를 지정하고 확인합니다.
	
	3. **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. **Fusion 미들웨어 구성 마법사** 대화 상자의 **4페이지**에서 도메인 모드에**프로덕션**을 선택하고 사용할 수 있는 JDK를 선택한 (또는 원하는 경우 JDK를 찾아본) 후 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  **Fusion 미들웨어 구성 마법사** 대화 상자의 **5페이지**에서 어떤 옵션도 선택하지 말고 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  **Fusion 미들웨어 구성 마법사** 대화 상자의 **6페이지**에서 **생성**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  **Fusion 미들웨어 구성 마법사** 대화 상자의 **7페이지**에서 도메인을 만든 후에 **다음**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  **Fusion 미들웨어 구성 마법사** 대화 상자의 **8페이지**에서 관리 서버 시작을 선택한 다음 **마침**을 클릭합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  **startWebLogic.cmd**의 명령 프롬프트가 시작됩니다. 메시지가 표시되면 WebLogic 사용자 이름과 암호를 제공합니다.

##클러스터 설정

1. 관리 가상 컴퓨터에 로그인된 상태에서 **WebLogic Server 관리 콘솔**을 실행하고 <http://localhost:7001/console>합니다. 메시지가 표시되면 WebLogic Server 사용자 이름과 암호를 제공합니다.

2. **WebLogic Server 관리 콘솔**에서 **잠금 및 편집**을 클릭합니다.

3. **도메인 구조** 창에서 **환경**을 확장한 다음 **클러스터**를 클릭합니다.

4. **클러스터 요약** 대화 상자에서 **새로 만들기**를 클릭한 다음 **클러스터**를 클릭합니다.

5. **클러스터 속성** 대화 상자에서:

	1. 클러스터의 이름을 입력합니다.

	2. **모드 메시징**은 **유니캐스트**를 선택합니다.

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. **확인**을 클릭합니다.

6. **도메인 구조** 창에서 **환경**을 확장한 다음 **서버**를 클릭합니다.

7. 첫 번째 관리되는 서버를 클러스터에 추가합니다.

	1. **새로 만들기**를 클릭합니다.

	2. **새 서버 만들기** 대화 상자에서:

		1. **서버 이름**에 첫 번째 관리되는 서버의 이름을 입력합니다. 예를 들면 **MYVM2-MANAGED** 입니다.

		2. **서버 수신 대기 주소**에 이름을 다시 입력합니다.

		3. **수신 대기 포트**에 **7008**을 입력합니다.

		4. **예, 이 서버를 기존 클러스터의 멤버로 만듭니다**를 선택합니다.

		5. **클러스터 선택** 드롭다운 목록에서 이전에 만든 클러스터를 선택합니다.

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. **다음**을 클릭합니다.

		7. **마침**을 클릭합니다.

8. 위의 단계를 사용하여 클러스터에 두 번째 관리되는 서버를 추가합니다. **서버 이름** 및 **서버 수신 대기 주소**에 두 번째 관리되는 컴퓨터의 이름을 사용합니다. **수신 대기 포트**에 **7008**을 사용합니다.

9. WebLogic Server 관리 콘솔에서 **변경 내용 활성화**를 클릭합니다.

10. 관리 가상 컴퓨터에서 **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver**에서 설정된 해당 값으로 **SERVER_HOME**이라는 환경 변수를 만듭니다. 다음 단계를 이용하여 환경 변수를 만들 수 있습니다.

	1. **Windows 시작**을 클릭하고 **제어판**을 입력하여 **제어판** 아이콘, **시스템 및 보안**, **시스템**, **고급 시스템 설정**을 차례로 클릭합니다.

	2. **고급** 탭을 클릭한 다음 **환경 변수**를 클릭합니다.

	3. **시스템 변수** 섹션에서 **새로 만들기**를 클릭하여 변수를 만듭니다.

	4. **새 시스템 변수** 대화 상자에서 변수 이름에 **SERVER_HOME**을 입력하고 해당 값에 **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver**를 입력합니다.

	5. **확인**을 클릭하여 새 환경 변수를 저장하고 **새 시스템 변수** 대화 상자를 닫습니다.

	6. 제어판에서 열려 있던 다른 대화 상자를 닫습니다.

11. 새 명령 프롬프트를 엽니다(**SERVER_HOME** 환경 변수가 적용되도록).

	>[AZURE.NOTE]나머지 단계 중 일부는 가상 컴퓨터에 로그온 한 후 명령 프롬프트를 사용해야 합니다. 명령 프롬프트를 연 후에 로그인된 컴퓨터를 쉽게 이해할 수 있도록 하려면 **title % COMPUTERNAME %**을 실행하십시오.
	>
	>**(% COMPUTERNAME %**는 컴퓨터 이름으로 자동 설정된 시스템 정의 환경 변수입니다. **title** **% COMPUTERNAME %** 명령을 실행하면 명령 프롬프트 제목 표시줄에 컴퓨터의 이름을 표시합니다.)

12. 다음 명령을 실행합니다.

		%SERVER_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain -template=c:\\mytestdomain.jar -template_name="mytestdomain" 

	이 명령은 **c:\\mytestdomain.jar**라는 jar를 만듭니다. 나중에 클러스터의 관리되는 가상 컴퓨터에 이 jar를 복사합니다.

13. 7001 포트에 대한 방화벽을 통해 인바운드 연결을 허용합니다.

	1. 가상 컴퓨터에 로그인되어 있는 동안 **Windows 시작**을 클릭하고 **고급 보안이 포함된 Windows 방화벽**을 입력한 다음 **고급 보안이 포함된 Windows 방화벽** 아이콘을 클릭합니다. 이렇게 하면 **고급 보안이 포함된 Windows 방화벽** 관리 콘솔이 열립니다.

	2. 방화벽 관리 콘솔의 왼쪽 창에서 **인바운드 규칙**을 클릭한 다음(**인바운드 규칙**이 보이지 않는 경우 왼쪽 창의 맨 위에 있는 노드를 확장함) 오른쪽 창에서 **새 규칙**을 클릭합니다.

	3. **규칙 유형**은 **포트**를 선택하고 **다음**을 클릭합니다.

	4. **프로토콜 및 포트**는 **TCP**를 선택하고 **특정 로컬 포트**를 선택하여 포트에 대해 **7001**을 입력하고 **다음**을 클릭합니다.

	5. **연결 허용**을 선택하고 **다음**을 클릭합니다.

	6. 규칙이 적용되는 프로필에 대해 기본값을 적용하고 **다음**을 클릭합니다.

	7. 규칙 이름 및 설명(선택 항목)을 지정한 다음 **마침**을 클릭합니다.

14. 관리되는 가상 컴퓨터마다:

	1. 가상 컴퓨터에 로그인합니다.

	2. **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver**에서 설정된 해당 값으로 **SERVER_HOME**이라는 환경 변수를 만듭니다.

	3. 관리 가상 컴퓨터에서 c:\\mytestdomain.jar을 관리되는 가상 컴퓨터의 c:\\mytestdomain.jar로 복사합니다.

	4. 명령 프롬프트를 엽니다(그리고 어떤 컴퓨터가 액세스되고 있는지 명확하게 하기 위해 명령 프롬프트에서 **title % COMPUTERNAME %**을 실행해야 합니다).

	5. 다음 명령을 실행합니다.

			%SERVER_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain -template=c:\\mytestdomain.jar

	6. 명령 프롬프트 현재 디렉터리를 **C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain\\bin**으로 변경합니다.

	7. start<< * MACHINENAME * >>.cmd를 실행합니다. 여기서 << * MACHINENAME * >> 관리되는 컴퓨터의 이름입니다. 예를 들어 **startMYVM2-MANAGED**입니다.

	8. 메시지가 표시되면 WebLogic Server 사용자 이름과 암호를 제공합니다.

	9. 7008 포트에 대한 방화벽을 통해 인바운드 연결을 허용합니다. (관리 서버에서 포트 7001를 여는 데 사용되는 단계를 수행하지만 관리되는 서버에 7008를 대신 사용합니다.)

15. 관리 가상 컴퓨터에서 **WebLogic Server 관리 콘솔**을 열고 <http://localhost:7001/console> 및 실행 중인 서버를 참조하십시오.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. 관리되는 가상 컴퓨터의 부하 분산된 끝점 집합을 만듭니다.

	1. [Azure 포털](https://ms.portal.azure.com/)의 **가상 컴퓨터 ** 섹션에서 첫 번째 관리되는 가상 컴퓨터(예: **MYVM2-MANAGED)**를 선택합니다. 
	2. **설정**, **끝점,** **추가**를 차례로 클릭합니다.

	3. 끝점에 이름을, 프로토콜에 **TCP**를 지정하고 공용 포트 **80** 및 개인 포트 **7008**을 지정합니다. 나머지 옵션은 그대로 둡니다.

	4. **create a load-balanced set**를 선택한 후 **완료**를 클릭합니다.

	5. 부하 분산된 집합의 이름을 지정하고 다른 매개 변수의 기본값을 적용한 다음 **완료를 클릭합니다.**

17. 가상 컴퓨터의 끝점 만들기:

	1. [Azure 포털](https://ms.portal.azure.com/)에 로그인합니다.

	2. **찾아보기**를 클릭합니다.

	3. **가상 컴퓨터**를 클릭합니다.

	4. 가상 컴퓨터를 선택합니다.

	5. **설정**을 클릭합니다.

	6. **부하 분산된 집합**을 클릭합니다.

	7. **조인**을 클릭합니다.

	8. 부하 분산된 집합 형식을 **내부**로 설정합니다.

	9. 끝점의 이름을 지정합니다.

		1. 프로토콜에 **TCP**를 사용합니다.

		2. 공용 포트에 **80**을 사용합니다.

		3. 프로브 포트에 **7008**을 사용합니다.

	10. 나머지 옵션은 그대로 둡니다.

	11. **확인**을 클릭합니다.

	12. 이 가상 컴퓨터가 다음 단계를 계속 하기 전에 부하 분산된 집합을 연결할 때까지 기다립니다.

18. [Azure 포털](https://ms.portal.azure.com/)의 **가상 컴퓨터** 섹션에서 두 번째 관리되는 가상 컴퓨터(예: **MYVM3-MANAGED**)를 선택합니다. 첫 번째 관리되는 가상 컴퓨터에 대해 만든 부하 분산 집합에 연결하려면 위의 단계를 따릅니다.

##클러스터에 응용 프로그램 배포

이제 다음 단계를 사용하여 응용 프로그램을 배포할 수 있습니다. <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war>에서 다운로드할 수 있는 Oracle shoppingcart 응용 프로그램을 배포하는 경우를 가정해 보겠습니다.

1. WebLogic Server 클러스터에 대한 관리자로 제공 중인 가상 컴퓨터에 로그인합니다(예: **MYVM1-ADMIN**). 

2. Shoppingcart.war를 로컬로 복사합니다. 예를 들어 **c:\\mywar**라는 폴더를 만들고 <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war>에서 WAR를 **c:\\mywar**로 저장합니다.

3. **WebLogic Server 관리 콘솔**을 열고 <http://localhost:7001/console>합니다. 메시지가 표시되면 WebLogic 사용자 이름과 암호를 제공합니다.

4. **WebLogic Server 관리 콘솔**에서 **잠금 및 편집**을 클릭하고 **배포**를 클릭한 다음 **설치**를 클릭합니다.

5. **경로**에 **c:\\myway\\shoppingcart.war**를 입력합니다.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	**다음**을 클릭합니다.

6. **이 배포를 응용 프로그램으로 설치**를 선택한 후 **다음**을 클릭합니다.

7. **마침**을 클릭합니다.

8. **사용 가능한 대상**에 사용자가 이전에 만든 클러스터를 선택하고 **클러스터의 모든 서버** 을 선택한 후에 **다음**을 클릭합니다.

9. **소스 접근성** 아래에서 **모든 대상에 이 응용 프로그램 복사**를 선택한 다음 **마침**을 클릭합니다.

10.  **WebLogic Server 관리 콘솔**에서 **저장**을 클릭한 다음 **변경 내용 활성화**를 클릭합니다.

11.  **배포**를 클릭하고 **shoppingcart**를 선택하고 **시작**을 클릭한 다음 **모든 요청 서비스**를 클릭합니다. 확인하라는 메시지가 나타나면 **예**를 클릭합니다.

12.  인터넷에서 실행 중인 쇼핑 카트 응용 프로그램을 보려면 `http://<<unique_domain_name>>/shoppingcart`의 형태로 URL에 대해 브라우저를 엽니다. (가상 컴퓨터를 클릭한 다음 Oracle WebLogic Server를 실행하기 위해 사용 중인 가상 컴퓨터를 선택하여 [Azure 포털](https://ms.portal.azure.com/)에서`<<unique_domain_name>>` 에 대한 값을 확인할 수 있습니다.)

## 다음 단계

클러스터가 예상 대로 작동 중인지 추가로 보려면 shoppingcart.war 프로젝트를 수정하여 브라우저 세션을 제공하는 컴퓨터 이름을 표시하고, 브라우저 세션을 시작하고, 브라우저 세션을 제공한 컴퓨터를 중단하며, 브라우저 세션을 새로 고쳐 다른 컴퓨터에서 브라우저 세션을 계속 제공하는 것을 볼 수 있습니다.

예:

1. **DWRHeader1.jspf** 파일의 위쪽에 다음 코드를 포함하도록 파일 수정:

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. 주석 줄 `Insert session descriptor element here` 뒤에 다음 코드를 포함하도록 **weblogic.xml** 파일을 수정합니다.

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. 다시 컴파일하고 업데이트된 shoppingcart.war를 다시 배포합니다.

4. 브라우저 세션을 열고 shoppingcart 응용 프로그램을 실행합니다. 쇼핑 카트에 일부 항목을 추가하고 어떤 컴퓨터가 브라우저 세션을 제공 중인지 확인합니다.

5. Azure 포털의 **가상 컴퓨터** 사용자 인터페이스에서 브라우저 세션을 제공한 VM을 선택하고 **종료**를 클릭합니다. 계속하기 전에 VM 상태가 **중지(할당 해제)**가 될 때까지 기다리십시오.

6. Shoppingcart 응용 프로그램을 실행 중인 브라우저 세션을 새로 고치고 다른 컴퓨터가 브라우저 세션을 제공 중인지 확인합니다.

7. 쇼핑 카트 링크를 클릭하고 쇼핑 카트에 이전에 추가된 항목이 있는지 확인합니다.

## 추가 리소스

지금까지 Oracle WebLogic Server를 실행 중인 클러스터를 설정했습니다. 추가 정보는 다음 항목을 참조하세요.

- [Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server 제품 설명서](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Microsoft Azure에서 Linux를 사용하는 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=July15_HO2-->