<properties urlDisplayName="Install SQL Server" pageTitle="Azure에서 SQL Server 가상 컴퓨터 프로비전 " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="Azure에서 SQL Server 가상 컴퓨터를 만들고 구성하는 방법을 설명하는 자습서입니다." metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure에서 SQL Server 가상 컴퓨터 프로비전" authors="selcint" solutions="" manager="jeffreyg" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="selcint" />

# Azure에서 SQL Server 가상 컴퓨터 프로비전

Azure 가상 컴퓨터 갤러리에는 Microsoft SQL Server가 포함된 몇 개의 이미지가 있습니다. 갤러리에서 가상 컴퓨터 이미지 중 하나를 선택하고 몇 번의 클릭으로 Azure 환경에 가상 컴퓨터를 프로비전할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

-   [Azure 관리 포털에 연결 및 갤러리에서 가상 컴퓨터 프로비전][Azure 관리 포털에 연결 및 갤러리에서 가상 컴퓨터 프로비전]
-   [원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료][원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료]
-   [다른 컴퓨터의 SQL Server Management Studio를 사용하여 가상 컴퓨터에 연결하는 구성 단계 완료][다른 컴퓨터의 SQL Server Management Studio를 사용하여 가상 컴퓨터에 연결하는 구성 단계 완료]
-   [다음 단계][다음 단계]

## <span id="Provision"></span>Azure 관리 포털에 연결 및 갤러리에서 가상 컴퓨터 프로비전</a>

1.  사용자 계정을 사용하여 [Azure 관리 포털][Azure 관리 포털]에 로그인합니다. Azure 계정이 없는 경우 [Azure 무료 평가판][Azure 무료 평가판]을 방문하세요.

2.  Azure 관리 포털 웹 페이지의 왼쪽 아래에서 **+새로 만들기**, **계산**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.

3.  **가상 컴퓨터 만들기** 페이지에서 SQL Server가 포함된 가상 컴퓨터 이미지를 선택한 후 페이지의 오른쪽 아래에서 다음 화살표를 클릭합니다. Azure에서 지원되는 SQL Server 이미지에 대한 최신 정보는 [Azure 가상 컴퓨터의 SQL Server][Azure 가상 컴퓨터의 SQL Server] 문서 집합의 [Azure 가상 컴퓨터에서 SQL Server 시작][Azure 가상 컴퓨터에서 SQL Server 시작] 토픽을 참조하세요.

    > [WACOM.NOTE] 플랫폼 이미지인 SQL Server 평가 버전을 사용하여 만든 가상 컴퓨터가 있는 경우 갤러리에서 분당 유료 버전 이미지로 업그레이드할 수 없습니다. 두 가지 옵션 중에서 선택할 수 있습니다.

    > -   먼저, 갤러리에서 분당 유료 SQL Server 버전을 사용하여 새 가상 컴퓨터를 만들고 [데이터 디스크를 사용하여 Azure에서 가상 컴퓨터 간에 SQL Server 데이터베이스 파일 및 스키마를 마이그레이션하는 방법][데이터 디스크를 사용하여 Azure에서 가상 컴퓨터 간에 SQL Server 데이터베이스 파일 및 스키마를 마이그레이션하는 방법]의 단계를 따라 새 가상 컴퓨터에 데이터베이스 파일을 마이그레이션할 수 있습니다. **또는**

    > -   [다른 버전의 SQL Server 2014로 업그레이드][다른 버전의 SQL Server 2014로 업그레이드](영문)의 단계를 수행하여 [Azure에서 Software Assurance를 통한 라이선스 이동][Azure에서 Software Assurance를 통한 라이선스 이동] 계약에 따라 SQL Server 평가판 버전의 기존 인스턴스를 다른 버전의 SQL Server로 업그레이드할 수 있습니다. 라이선스가 부여된 SQL Server를 구입하는 방법에 대한 자세한 내용은 [SQL Server 구매 방법][SQL Server 구매 방법](영문)을 참조하세요.

4.  첫 번째 **가상 컴퓨터 구성** 페이지에서 다음 정보를 입력합니다.

    -   **가상 컴퓨터 이름**을 입력합니다.
    -   **새 사용자 이름** 상자에 VM 로컬 관리자 계정의 고유한 사용자 이름을 입력합니다.
    -   **새 암호** 상자에 강력한 암호를 입력합니다. 자세한 내용은 [강력한 암호][강력한 암호]를 참조하세요.
    -   **암호 확인** 상자에 암호를 다시 입력합니다.
    -   드롭다운 목록에서 적절한 **크기**를 선택합니다.

    > [WACOM.NOTE] 가상 컴퓨터의 크기는 프로비전 중 지정합니다.
    > A2는 프로덕션 작업용으로 권장되는 최소 크기입니다.
    > SQL Server Enterprise Edition을 사용할 경우 가상 컴퓨터의 최소 크기는 A3입니다.
    > SQL Server Enterprise Edition을 사용할 경우 A3 이상을 선택합니다.
    > SQL Server 2012 또는 2014 Enterprise Optimized for Transactional Workloads 이미지를 사용할 경우 A4를 선택합니다.
    > SQL Server 2012 또는 2014 Enterprise Optimized for Data Warehousing Workloads 이미지를 사용할 경우 A7을 선택합니다.
    > 선택한 크기에 따라 구성할 수 있는 데이터 디스크 수가 제한됩니다. 사용 가능한 가상 컴퓨터 크기 및 가상 컴퓨터에 연결할 수 있는 데이터 디스크 수에 대한 최신 정보는 [Azure의 가상 컴퓨터 크기][Azure의 가상 컴퓨터 크기]를 참조하세요.

    오른쪽 아래에 있는 다음 화살표를 클릭하여 계속합니다.

    ![VM 구성][VM 구성]

5.  두 번째 **가상 컴퓨터 구성** 페이지에서 네트워킹, 저장소 및 가용성에 대한 리소스를 구성합니다.

    -   **클라우드 서비스** 상자에서 **새 클라우드 서비스 만들기**를 선택합니다.
    -   **클라우드 서비스 DNS 이름** 상자에 **TESTNAME.cloudapp.net** 형식으로 이름이 완성되도록 선택한 DNS 이름의 첫 번째 부분을 입력합니다.
    -   **지역/선호도 그룹/가상 네트워크** 상자에서 이 가상 이미지를 호스트할 영역을 선택합니다.
    -   **저장소 계정**에서 기존 저장소 계정 또는 자동으로 생성된 저장소 계정을 선택합니다.
    -   **가용성 집합** 상자에서 **(없음)**을 선택합니다.
    -   약관을 읽고 동의합니다.

6.  다음 화살표를 클릭하여 계속합니다.

7.  오른쪽 아래에서 확인 표시를 클릭하여 계속합니다.

8.  Azure에서 가상 컴퓨터를 준비하는 동안 기다립니다. 다음을 통해 진행할 가상 컴퓨터 상태를 예상합니다.

    -   시작 중(프로비전 중)
    -   중지
    -   시작 중(프로비전 중)
    -   실행 중(프로비전 중)
    -   실행 중

## <span id="RemoteDesktop"></span>원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료</a>

1.  프로비전이 완료되면 가상 컴퓨터의 이름을 클릭하여 대시보드 페이지로 이동합니다. 페이지 맨 아래에 있는 **연결**을 클릭합니다.

2.  Windows 원격 데스크톱 프로그램(`%windir%\system32\mstsc.exe`)을 사용하여 rpd 파일을 열도록 선택합니다.

3.  **Windows 보안** 대화 상자에서, 이전 단계에서 지정한 로컬 관리자 계정의 암호를 입력합니다. 가상 컴퓨터의 자격 증명을 확인하도록 요청될 수도 있습니다.

4.  이 가상 컴퓨터에 처음 로그온하는 경우 데스크톱 설정, Windows 업데이트 및 Windows 초기 구성 작업(sysprep) 완료를 포함하여 여러 프로세스를 완료해야 할 수 있습니다. Windows sysprep가 완료되면 SQL Server 설치 프로세스에서 구성 작업을 완료합니다. 이러한 작업으로 인해 완료되는 동안 잠시 지연이 발생할 수 있습니다. SQL Server 설치가 완료될 때까지 `SELECT @@SERVERNAME`에서 올바른 이름을 반환하지 못할 수 있습니다.

Windows 원격 데스크톱을 사용하여 가상 컴퓨터에 연결된 후 가상 컴퓨터는 다른 컴퓨터와 상당히 유사하게 작동합니다. SQL Server Management Studio(가상 컴퓨터에서 실행 중인)가 설치되어 있는 기본 SQL Server 인스턴스에 일반적인 방식으로 연결합니다.

## <span id="SSMS"></span>다른 컴퓨터의 SQL Server Management Studio를 사용하여 가상 컴퓨터에 연결하는 구성 단계 완료</a>

인터넷에서 SQL Server의 인스턴스에 연결하려면 먼저 아래의 섹션에 설명된 대로 다음 작업을 완료해야 합니다.

-   [가상 컴퓨터용 TCP 끝점 만들기][가상 컴퓨터용 TCP 끝점 만들기]
-   [Windows 방화벽에서 TCP 포트 열기][Windows 방화벽에서 TCP 포트 열기]
-   [TCP 프로토콜을 수신 대기하도록 SQL Server 구성][TCP 프로토콜을 수신 대기하도록 SQL Server 구성]
-   [혼합 모드 인증을 위해 SQL Server 구성][혼합 모드 인증을 위해 SQL Server 구성]
-   [SQL Server 인증 로그인 만들기][SQL Server 인증 로그인 만들기]
-   [가상 컴퓨터의 DNS 이름 확인][가상 컴퓨터의 DNS 이름 확인]
-   [다른 컴퓨터에서 데이터베이스 엔진에 연결][다른 컴퓨터에서 데이터베이스 엔진에 연결]
-   [응용 프로그램에서 데이터베이스 엔진에 연결] (\#cdea)

연결 경로는 다음 다이어그램에 요약되어 있습니다.

![SQL Server 가상 컴퓨터에 연결][SQL Server 가상 컴퓨터에 연결]

### <span id="Endpoint"></span>가상 컴퓨터용 TCP 끝점 만들기</a>

가상 컴퓨터에는 들어오는 TCP 통신을 수신 대기하는 끝점이 있어야 합니다. 이 Azure 구성 단계에서는 들어오는 TCP 포트 트래픽을 가상 컴퓨터에 액세스 가능한 TCP 포트로 보냅니다.

1.  Azure 관리 포털에서 **가상 컴퓨터**를 클릭합니다.

2.  새로 만든 가상 컴퓨터를 클릭합니다. 가상 컴퓨터에 대한 정보가 표시됩니다.

3.  페이지 맨 위 근처에서 **끝점** 페이지를 선택한 후 페이지 맨 아래에서 **추가**를 클릭합니다.

4.  **가상 컴퓨터에 끝점 추가** 페이지에서 **독립 실행형 끝점 추가**를 클릭한 후 다음 화살표를 클릭하여 계속합니다.

5.  **끝점의 세부 정보를 지정하세요.** 페이지에서 다음 정보를 제공합니다.

    -   **이름** 상자에 끝점의 이름을 입력합니다.
    -   **프로토콜** 상자에서 **TCP**를 선택합니다. **개인 포트** 상자에 SQL Server의 수신 대기 중인 기본 포트 **1433**을 입력할 수 있습니다. 유사하게 **공용 포트** 상자에 **57500**을 입력할 수 있습니다. 많은 조직이 악의적인 보안 공격을 방지하기 위해 다른 포트 번호를 선택합니다.

6.  확인 표시를 클릭하여 계속합니다. 끝점이 만들어집니다.

### <span id="FW"></span>Windows 방화벽에서 데이터베이스 엔진의 기본 인스턴스용 TCP 포트 열기</a>

1.  Windows 원격 데스크톱을 통해 가상 컴퓨터에 연결합니다. 로그인한 후 시작 메뉴에서 **실행**을 클릭하고, **WF.msc**를 클릭한 후 **확인**을 클릭합니다.

    ![방화벽 프로그램 시작][방화벽 프로그램 시작]

2.  **고급 보안이 포함된 Windows 방화벽**의 왼쪽 창에 있는 작업 창에서 **인바운드 규칙**을 마우스 오른쪽 단추로 클릭한 후 **새 규칙**을 클릭합니다.

    ![새 규칙][새 규칙]

3.  **규칙 유형** 대화 상자에서 **포트**를 선택한 후 **다음**을 클릭합니다.

4.  **프로토콜 및 포트** 대화 상자에서 **TCP**를 선택합니다. **특정 로컬 포트**를 선택한 후 데이터베이스 엔진의 인스턴스 포트 번호(기본 인스턴스의 **1433** 또는 끝점 단계에서 개인 포트에 대해 선택한 포트)를 입력합니다.

    ![TCP 포트 1433][TCP 포트 1433]

5.  **다음**을 클릭합니다.

6.  **작업** 대화 상자에서 **연결 허용**을 선택한 후 **다음**을 클릭합니다.

    **보안 정보:** **보안 연결만 허용**을 선택하면 추가 보안을 제공할 수 있습니다. 사용자 환경에서 추가 보안을 구성하려는 경우 이 옵션을 선택하세요.

    ![연결 허용][연결 허용]

7.  **프로필** 대화 상자에서 **공개**를 선택한 후 **다음**을 클릭합니다.

    **보안 정보:** **공개**를 선택하면 인터넷을 통한 액세스가 허용됩니다. 가능하면 더 제한적인 프로필을 선택하세요.

    ![공개 프로필][공개 프로필]

8.  **이름** 대화 상자에서 이 규칙의 이름 및 설명을 입력한 후 **마침**을 클릭합니다.

    ![규칙 이름][규칙 이름]

필요한 경우 다른 구성 요소의 추가 포트를 엽니다. 자세한 내용은 [SQL Server 액세스를 허용하도록 Windows 방화벽 구성][SQL Server 액세스를 허용하도록 Windows 방화벽 구성]을 참조하세요.

### <span id="TCP"></span>TCP 프로토콜을 수신 대기하도록 SQL Server 구성</a>

1.  원격 데스크톱을 사용하여 가상 컴퓨터에 연결되어 있는 동안 시작 메뉴에서 **모든 프로그램**, **Microsoft SQL Server** *버전*, **구성 도구**, **SQL Server 구성 관리자**를 차례로 클릭합니다.

    ![SSCM 열기][SSCM 열기]

2.  SQL Server 구성 관리자의 콘솔 창에서 **SQL Server 네트워크 구성**을 확장합니다.

3.  콘솔 창에서 ***인스턴스 이름*에 대한 프로토콜**을 클릭합니다. 기본 인스턴스는 **MSSQLSERVER에 대한 프로토콜**입니다.

4.  세부 정보 창에서 TCP를 마우스 오른쪽 단추로 클릭하면 기본적으로 갤러리 이미지에 대해 사용으로 설정되어 있어야 합니다. 사용자 지정 이미지에 대해 **사용**(상태가 사용 안 함인 경우)을 클릭합니다.

    ![TCP 사용][TCP 사용]

5.  콘솔 창에서 **SQL Server 서비스**를 클릭합니다. 데이터베이스 엔진 다시 시작은 다음 단계를 완료할 때까지 연기할 수 있습니다.

6.  세부 정보 창에서 **SQL Server(*인스턴스 이름*)**(기본 인스턴스는 **SQL Server (MSSQLSERVER)**)를 마우스 오른쪽 단추로 클릭한 후 **다시 시작**을 클릭하여 SQL Server의 인스턴스를 중지했다가 다시 시작합니다.

    ![데이터베이스 엔진 다시 시작][데이터베이스 엔진 다시 시작]

7.  SQL Server 구성 관리자를 닫습니다.

SQL Server 데이터베이스 엔진용 프로토콜 사용 설정에 대한 자세한 내용은 [서버 네트워크 프로토콜 설정 또는 해제][서버 네트워크 프로토콜 설정 또는 해제]를 참조하세요.

### <span id="Mixed"></span>혼합 모드 인증을 위해 SQL Server 구성</a>

SQL Server 데이터베이스 엔진은 도메인 환경에서만 Windows 인증을 사용할 수 있습니다. 다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 혼합 모드 인증을 위해 SQL Server를 구성하세요. 혼합 모드 인증은 SQL Server 인증과 Windows 인증을 모두 허용합니다. Azure 가상 네트워크를 구성한 경우에는 혼합 모드 인증을 구성할 필요가 없습니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server][1] 문서 집합의 [Azure 가상 컴퓨터의 SQL Server에 대한 연결 고려 사항][Azure 가상 컴퓨터의 SQL Server에 대한 연결 고려 사항] 토픽을 참조하세요.

1.  원격 데스크톱을 사용하여 가상 컴퓨터에 연결한 상태로, 시작 메뉴에서 **모든 프로그램**, **Microsoft SQL Server *버전***, **SQL Server Management Studio**를 차례로 클릭합니다.

    ![SSMS 시작][SSMS 시작]

    처음으로 Management Studio를 열 때 사용자 Management Studio 환경이 만들어져야 합니다. 어느 정도 시간이 걸릴 수 있습니다.

2.  Management Studio를 열면 **서버에 연결** 대화 상자가 표시됩니다. **서버 이름** 상자에, 개체 탐색기를 사용하여 데이터베이스 엔진에 연결할 가상 컴퓨터의 이름을 입력합니다. 가상 컴퓨터 이름 대신 **(로컬)** 또는 단일 기간을 **서버 이름**으로 사용할 수도 있습니다. **Windows 인증**을 선택하고, **사용자 이름** 상자의 ***VM\_이름*\\로컬\_관리자**를 그대로 둡니다. **연결**을 클릭합니다.

    ![서버에 연결][서버에 연결]

3.  SQL Server Management Studio 개체 탐색기에서 SQL Server 인스턴스의 이름(가상 컴퓨터 이름)을 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.

    ![서버 속성][서버 속성]

4.  **보안** 페이지의 **서버 인증**에서 **SQL Server 및 Windows 인증 모드**를 선택한 후 **확인**을 클릭합니다.

    ![인증 모드 선택][인증 모드 선택]

5.  SQL Server Management Studio 대화 상자에서 **확인**을 클릭하여 SQL Server를 다시 시작해야 하는 요구 사항을 확인합니다.

6.  개체 탐색기에서 서버를 마우스 오른쪽 단추로 클릭한 후 **다시 시작**을 클릭합니다. SQL Server 에이전트가 실행 중인 경우 에이전트도 다시 시작해야 합니다.

    ![다시 시작][다시 시작]

7.  SQL Server Management Studio 대화 상자에서 **예**를 클릭하여 SQL Server를 다시 시작한다는 데 동의합니다.

### <span id="Logins"></span>SQL Server 인증 로그인 만들기</a>

다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 SQL Server 인증 로그인을 하나 이상 만들어야 합니다.

1.  SQL Server Management Studio 개체 탐색기에서 새 로그인을 만들 서버 인스턴스의 폴더를 확장합니다.

2.  **보안** 폴더를 마우스 오른쪽 단추로 클릭하고, **새로 만들기**를 가리키고 **로그인...**을 선택합니다.

    ![새 로그인][새 로그인]

3.  **로그인 - 신규** 대화 상자의 **일반** 페이지에 있는 **로그인 이름** 상자에 새 사용자의 이름을 입력합니다.

4.  **SQL Server 인증**을 선택합니다.

5.  **암호** 상자에 새 사용자의 암호를 입력합니다. **암호 확인** 상자에 암호를 다시 입력합니다.

6.  복잡성 및 강제성에 대한 암호 정책 옵션을 적용하려면 **암호 정책 강제 적용**(권장)을 선택합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

7.  만료에 대한 암호 정책 옵션을 적용하려면 **암호 만료 강제 적용**(권장)을 선택합니다. 이 확인란을 사용하려면 먼저 암호 정책 강제 적용을 선택해야 합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

8.  처음으로 로그인을 사용한 후 사용자가 새 암호를 만들도록 강제하려면 **다음 로그인할 때 반드시 암호 변경**을 선택합니다. 다른 사용자가 이 로그인을 사용할 경우에 이 옵션을 선택하는 것이 좋습니다. 사용자만 이 로그인을 사용하는 경우에는 이 옵션을 선택하지 마세요. 이 확인란을 사용하려면 먼저 암호 만료 강제 적용을 선택해야 합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

9.  **기본 데이터베이스** 목록에서 로그인의 기본 데이터베이스를 선택합니다. **master**가 이 옵션의 기본값입니다. 사용자 데이터베이스를 아직 만들지 않은 경우 **master**로 설정된 상태로 둡니다.

10. **기본 언어** 목록에서 **기본값**을 선택 값으로 둡니다.

    ![로그인 속성][로그인 속성]

11. 처음 로그인을 만드는 경우 이 로그인을 SQL Server 관리자로 지정할 수 있습니다. 그렇게 하는 경우 **서버 역할** 페이지에서 **sysadmin**을 선택합니다.

    **보안 정보:** sysadmin 고정 서버 역할의 구성원은 데이터베이스 엔진을 완전히 제어할 수 있습니다. 따라서 이 역할의 구성원은 신중하게 제한해야 합니다.

    ![sysadmin][sysadmin]

12. 확인을 클릭합니다.

SQL Server 로그인에 대한 자세한 내용은 [로그인 만들기][로그인 만들기]를 참조하세요.

### <span id="DNS"></span>가상 컴퓨터의 DNS 이름 확인</a>

다른 컴퓨터에서 SQL Server 데이터베이스 엔진에 연결하려면 가상 컴퓨터의 DNS(Domain Name System) 이름을 알아야 합니다. 이 이름은 인터넷에서 가상 컴퓨터를 식별하는 이름입니다. IP 주소를 사용할 수 있지만 Azure가 중복 또는 유지 관리를 위해 리소스를 이동할 경우 IP 주소가 변경될 수 있습니다. DNS 이름은 새 IP 주소로 리디렉션할 수 있으므로 안정적입니다.

1.  Azure 관리 포털(또는 이전 단계)에서 **가상 컴퓨터**를 선택합니다.

2.  **가상 컴퓨터 인스턴스** 페이지의 **DNS 이름** 열에서, **http://**로 시작하는 가상 컴퓨터의 DNS 이름을 찾아서 복사합니다. 사용자 인터페이스에 전체 이름이 표시되지 않을 수도 있지만 이름을 마우스 오른쪽 단추로 클릭하고 복사를 선택합니다.

### <span id="cde"></span>다른 컴퓨터에서 데이터베이스 엔진에 연결</a>

1.  인터넷에 연결된 컴퓨터에서 SQL Server Management Studio를 엽니다.

2.  **서버에 연결** 또는 **데이터베이스 엔진에 연결** 대화 상자의 **서버 이름** 상자에 가상 컴퓨터의 DNS 이름(이전 작업에서 확인된 이름) 및 공개 끝점 포트 번호를 *DNS이름,포트번호* 형식(예: **tutorialtestVM.cloudapp.net,57500**)으로 입력합니다.

3.  **인증** 상자에 **SQL Server 인증**을 선택합니다.

4.  **로그인** 상자에, 이전 작업에서 만든 로그인 이름을 입력합니다.

5.  **암호** 상자에, 이전 작업에서 만든 로그인의 암호를 입력합니다.

6.  **연결**을 클릭합니다.

    ![SSMS를 사용하여 연결][SSMS를 사용하여 연결]

### <span id="cdea"></span> 응용 프로그램에서 데이터베이스 엔진에 연결</a>

Management Studio를 사용하여 Azure 가상 컴퓨터에서 실행 중인 SQL Server 인스턴스에 연결하는 경우 다음과 유사한 연결 문자열을 사용하여 연결할 수 있어야 합니다.

    connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

자세한 내용은 [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법][SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법](영문)을 참조하세요.

## <span id="Optional"></span>다음 단계</a>

플랫폼 이미지를 사용하여 Azure 가상 컴퓨터에서 SQL Server를 만들고 구성하는 방법을 살펴봤습니다. Azure 가상 컴퓨터에서 SQL Server를 사용할 경우 라이브러리의 [Azure 가상 컴퓨터의 SQL Server][Azure 가상 컴퓨터의 SQL Server] 문서에 나와 있는 자세한 지침을 따르는 것이 좋습니다. 이 문서 집합에는 자세한 지침을 제공하는 일련의 문서 및 자습서가 포함되어 있습니다. 다음 섹션이 이 시리즈에 포함되어 있습니다.

[Azure 가상 컴퓨터의 SQL Server][Azure 가상 컴퓨터의 SQL Server]

[Azure 가상 컴퓨터에서 SQL Server 시작][Azure 가상 컴퓨터에서 SQL Server 시작]

[Azure 가상 컴퓨터의 SQL Server로 마이그레이션할 준비하기][Azure 가상 컴퓨터의 SQL Server로 마이그레이션할 준비하기]

-   데이터 디스크를 사용하여 Azure에서 가상 컴퓨터 간에 SQL Server 데이터베이스 파일 및 스키마를 마이그레이션하는 방법

[Azure 가상 컴퓨터의 SQL Server 배포][Azure 가상 컴퓨터의 SQL Server 배포]

-   CSUpload를 사용하여 데이터 디스크의 SQL Server 데이터 및 설치 파일을 온-프레미스에서 Azure로 복사하는 방법
-   Hyper-V를 사용하여 온-프레미스에서 기본 가상 컴퓨터를 만드는 방법
-   기존 온-프레미스 SQL Server 디스크를 사용하여 Azure에서 SQL Server 가상 컴퓨터를 만드는 방법
-   기존 온-프레미스 SQL Server 가상 컴퓨터를 사용하여 Azure에서 SQL Server 가상 컴퓨터를 만드는 방법
-   PowerShell을 사용하여 Azure에서 SQL Server 가상 컴퓨터를 설정하는 방법
-   연결된 데이터 디스크를 사용하여 데이터베이스 파일을 저장하는 방법

[Azure 가상 컴퓨터의 SQL Server에 대한 연결 고려 사항][2]

-   자습서: 동일한 클라우드 서비스의 SQL Server에 연결
-   자습서: 다른 클라우드 서비스의 SQL Server에 연결
-   자습서: 가상 네트워크를 통해 Azure의 SQL Server에 ASP.NET 응용 프로그램 연결

[Azure 가상 컴퓨터의 SQL Server에 대한 성능 고려 사항][Azure 가상 컴퓨터의 SQL Server에 대한 성능 고려 사항]

[Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항][Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항]

[Azure 가상 컴퓨터에서 SQL Server 문제 해결 및 모니터링][Azure 가상 컴퓨터에서 SQL Server 문제 해결 및 모니터링]

[Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구][Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구]

-   자습서: Azure의 AlwaysOn 가용성 그룹(GUI)
-   자습서: Azure의 AlwaysOn 가용성 그룹(PowerShell)
-   자습서: AlwaysOn 가용성 그룹의 리스너 구성
-   자습서: Azure Replica Wizard 추가
-   자습서: Azure에서 재해 복구를 위한 데이터베이스 미러링
-   자습서: Hybrid IT의 재해 복구를 위한 데이터베이스 미러링
-   자습서: Azure에서 고가용성을 위한 데이터베이스 미러링
-   자습서: Hybrid IT의 재해 복구를 위한 로그 전달
-   Azure에서 가용성 그룹 리스너 문제 해결

[Azure 가상 컴퓨터에서 SQL Server의 백업 및 복원][Azure 가상 컴퓨터에서 SQL Server의 백업 및 복원]

[Azure 가상 컴퓨터의 SQL Server Business Intelligence][Azure 가상 컴퓨터의 SQL Server Business Intelligence]

-   PowerShell을 사용하여 SQL Server BI 및 SharePoint 2010을 사용하는 Azure VM 만들기
-   PowerShell을 사용하여 SQL Server BI 및 SharePoint 2013을 사용하는 Azure VM 만들기
-   PowerShell을 사용하여 기본 모드 보고서 서버로 Azure VM 만들기

[Azure 가상 컴퓨터의 SQL Server 데이터 웨어하우징][Azure 가상 컴퓨터의 SQL Server 데이터 웨어하우징]

[Azure 가상 컴퓨터의 SQL Server 관련 기술 문서][Azure 가상 컴퓨터의 SQL Server 관련 기술 문서]

-   [백서: Azure 가상 컴퓨터의 Azure SQL 데이터베이스 및 SQL Server 이해][백서: Azure 가상 컴퓨터의 Azure SQL 데이터베이스 및 SQL Server 이해]

-   [백서: Azure 가상 컴퓨터의 SQL Server를 위한 응용 프로그램 패턴 및 개발 전략][백서: Azure 가상 컴퓨터의 SQL Server를 위한 응용 프로그램 패턴 및 개발 전략]

-   [백서: Azure 가상 컴퓨터에 SQL Server Business Intelligence 배포][백서: Azure 가상 컴퓨터에 SQL Server Business Intelligence 배포]

-   [백서: Azure 가상 컴퓨터의 SQL Server에 대한 성능 지침][백서: Azure 가상 컴퓨터의 SQL Server에 대한 성능 지침]

-   [백서: Reporting Services 보고서 뷰어 컨트롤 및 Microsoft Azure 가상 컴퓨터 기반 보고서 서버][백서: Reporting Services 보고서 뷰어 컨트롤 및 Microsoft Azure 가상 컴퓨터 기반 보고서 서버]

  [Azure 관리 포털에 연결 및 갤러리에서 가상 컴퓨터 프로비전]: #Provision
  [원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료]: #RemoteDesktop
  [다른 컴퓨터의 SQL Server Management Studio를 사용하여 가상 컴퓨터에 연결하는 구성 단계 완료]: #SSMS
  [다음 단계]: #Optional
  [Azure 관리 포털]: http://manage.windowsazure.com
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/
  [Azure 가상 컴퓨터의 SQL Server]: http://go.microsoft.com/fwlink/p/?LinkId=294719
  [Azure 가상 컴퓨터에서 SQL Server 시작]: http://go.microsoft.com/fwlink/p/?LinkId=294720
  [데이터 디스크를 사용하여 Azure에서 가상 컴퓨터 간에 SQL Server 데이터베이스 파일 및 스키마를 마이그레이션하는 방법]: http://go.microsoft.com/fwlink/p/?LinkId=294738
  [다른 버전의 SQL Server 2014로 업그레이드]: http://go.microsoft.com/fwlink/?LinkId=396915
  [Azure에서 Software Assurance를 통한 라이선스 이동]: http://www.windowsazure.com/ko-kr/pricing/license-mobility/
  [SQL Server 구매 방법]: http://www.microsoft.com/ko-kr/sqlserver/get-sql-server/how-to-buy.aspx
  [강력한 암호]: http://msdn.microsoft.com/library/ms161962.aspx
  [Azure의 가상 컴퓨터 크기]: http://msdn.microsoft.com/ko-kr/library/azure/dn197896.aspx
  [VM 구성]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
  [가상 컴퓨터용 TCP 끝점 만들기]: #Endpoint
  [Windows 방화벽에서 TCP 포트 열기]: #FW
  [TCP 프로토콜을 수신 대기하도록 SQL Server 구성]: #TCP
  [혼합 모드 인증을 위해 SQL Server 구성]: #Mixed
  [SQL Server 인증 로그인 만들기]: #Logins
  [가상 컴퓨터의 DNS 이름 확인]: #DNS
  [다른 컴퓨터에서 데이터베이스 엔진에 연결]: #cde
  [SQL Server 가상 컴퓨터에 연결]: ./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF
  [방화벽 프로그램 시작]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
  [새 규칙]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
  [TCP 포트 1433]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
  [연결 허용]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
  [공개 프로필]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
  [규칙 이름]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
  [SQL Server 액세스를 허용하도록 Windows 방화벽 구성]: http://msdn.microsoft.com/ko-kr/library/cc646023.aspx
  [SSCM 열기]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
  [TCP 사용]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
  [데이터베이스 엔진 다시 시작]: ./media/virtual-machines-provision-sql-server/11Restart.png
  [서버 네트워크 프로토콜 설정 또는 해제]: http://msdn.microsoft.com/ko-kr/library/ms191294.aspx
  [1]: http://go.microsoft.com/fwlink/?LinkId=294719
  [Azure 가상 컴퓨터의 SQL Server에 대한 연결 고려 사항]: http://go.microsoft.com/fwlink/?LinkId=294723
  [SSMS 시작]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
  [서버에 연결]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
  [서버 속성]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
  [인증 모드 선택]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
  [다시 시작]: ./media/virtual-machines-provision-sql-server/22Restart2.png
  [새 로그인]: ./media/virtual-machines-provision-sql-server/23New-Login.png
  [로그인 속성]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
  [sysadmin]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
  [로그인 만들기]: http://msdn.microsoft.com/ko-kr/library/aa337562.aspx
  [SSMS를 사용하여 연결]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
  [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx
  [Azure 가상 컴퓨터의 SQL Server로 마이그레이션할 준비하기]: http://go.microsoft.com/fwlink/p/?LinkId=294721
  [Azure 가상 컴퓨터의 SQL Server 배포]: http://go.microsoft.com/fwlink/p/?LinkId=294722
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=294723
  [Azure 가상 컴퓨터의 SQL Server에 대한 성능 고려 사항]: http://go.microsoft.com/fwlink/?LinkId=294724
  [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항]: http://go.microsoft.com/fwlink/p/?LinkId=294725
  [Azure 가상 컴퓨터에서 SQL Server 문제 해결 및 모니터링]: http://go.microsoft.com/fwlink/p/?LinkId=294726
  [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구]: http://go.microsoft.com/fwlink/p/?LinkId=294727
  [Azure 가상 컴퓨터에서 SQL Server의 백업 및 복원]: http://go.microsoft.com/fwlink/p/?LinkId=294728
  [Azure 가상 컴퓨터의 SQL Server Business Intelligence]: http://go.microsoft.com/fwlink/p/?LinkId=294729
  [Azure 가상 컴퓨터의 SQL Server 데이터 웨어하우징]: http://msdn.microsoft.com/library/windowsazure/dn387396.aspx
  [Azure 가상 컴퓨터의 SQL Server 관련 기술 문서]: http://msdn.microsoft.com/library/azure/dn248435.aspx
  [백서: Azure 가상 컴퓨터의 Azure SQL 데이터베이스 및 SQL Server 이해]: http://azure.microsoft.com/ko-kr/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/
  [백서: Azure 가상 컴퓨터의 SQL Server를 위한 응용 프로그램 패턴 및 개발 전략]: http://msdn.microsoft.com/library/azure/dn574746.aspx
  [백서: Azure 가상 컴퓨터에 SQL Server Business Intelligence 배포]: http://msdn.microsoft.com/library/windowsazure/dn321998.aspx
  [백서: Azure 가상 컴퓨터의 SQL Server에 대한 성능 지침]: http://msdn.microsoft.com/library/windowsazure/dn248436.aspx
  [백서: Reporting Services 보고서 뷰어 컨트롤 및 Microsoft Azure 가상 컴퓨터 기반 보고서 서버]: http://msdn.microsoft.com/library/azure/dn753698.aspx
