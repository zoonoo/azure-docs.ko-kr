---
title: Azure 가상 머신에서 마이크로 포커스 엔터프라이즈 개발자 4.0을 위한 마이크로 포커스 CICS BankDemo 설정
description: Azure 가상 시스템(VM)에서 마이크로 포커스 BankDemo 응용 프로그램을 실행하여 마이크로 포커스 엔터프라이즈 서버 및 엔터프라이즈 개발자를 사용하는 방법을 알아봅니다.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411080"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Azure에서 마이크로 포커스 엔터프라이즈 개발자4.0을 위한 마이크로 포커스 CICS BankDemo 설정

Azure에서 마이크로 포커스 엔터프라이즈 서버 4.0 및 엔터프라이즈 개발자 4.0을 설정하면 IBM z/OS 워크로드의 배포를 테스트할 수 있습니다. 이 문서에서는 엔터프라이즈 개발자와 함께 제공되는 샘플 응용 프로그램인 CICS BankDemo를 설정하는 방법을 보여 주며 있습니다.

IC는 많은 온라인 메인프레임 애플리케이션에서 사용하는 거래 플랫폼인 고객 정보 제어 시스템을 의미합니다. BankDemo 응용 프로그램은 엔터프라이즈 서버 및 엔터프라이즈 개발자가 작동하는 방법과 녹색 화면 터미널로 완성된 실제 응용 프로그램을 관리하고 배포하는 방법을 학습하는 데 적합합니다.

> [!NOTE]
> 출시 예정: Azure VM에서 [마이크로 포커스 엔터프라이즈 서버 5.0을](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) 설정하는 방법에 대한 지침입니다.

## <a name="prerequisites"></a>사전 요구 사항

- [엔터프라이즈 개발자가](set-up-micro-focus-azure.md)있는 VM . 엔터프라이즈 개발자는 개발 및 테스트 목적으로 엔터프라이즈 서버의 전체 인스턴스를 보유하고 있습니다. 이 인스턴스는 데모에 사용되는 엔터프라이즈 서버의 인스턴스입니다.

- [SQL Server 2017 익스프레스 에디션.](https://www.microsoft.com/sql-server/sql-server-editions-express) 다운로드하여 엔터프라이즈 개발자 VM에 설치합니다. 엔터프라이즈 서버는 CICS 리전 관리를 위한 데이터베이스가 필요하며 BankDemo 응용 프로그램은 BANKDEMO라는 SQL Server 데이터베이스도 사용합니다. 이 데모에서는 두 데이터베이스에 SQL Server Express를 사용하고 있다고 가정합니다. 설치 할 때 기본 설치를 선택합니다.

- [SQL 서버 관리](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) 스튜디오(SSMS). SSMS는 데이터베이스를 관리하고 T-SQL 스크립트를 실행하는 데 사용됩니다. 다운로드하여 엔터프라이즈 개발자 VM에 설치합니다.

- 무료 다운로드 할 수있는 최신 서비스 팩 또는 [비주얼 스튜디오 커뮤니티와 비주얼 스튜디오](https://visualstudio.microsoft.com/vs/community/) [2019.](https://azure.microsoft.com/downloads/)

- 룸바 데스크탑 또는 다른 3270 에뮬레이터.

## <a name="configure-the-windows-environment"></a>Windows 환경 구성

VM에 엔터프라이즈 개발자 4.0을 설치한 후에는 함께 제공되는 엔터프라이즈 서버의 인스턴스를 구성해야 합니다. 이렇게 하려면 다음과 같이 몇 가지 추가 Windows 기능을 설치해야 합니다.

1. RDP를 사용하여 만든 엔터프라이즈 서버 4.0 VM에 로그온합니다.

2. **시작** 버튼 옆의 **검색** 아이콘을 클릭하고 **Windows 기능을**입력합니다. 서버 관리자가 역할 및 기능 마법사를 추가합니다.

3. **웹 서버(IIS) 역할을**선택한 다음 다음 옵션을 확인합니다.

    - 웹 관리 도구
    - IIS 6 관리 호환성(사용 가능한 모든 기능 선택)
    - IIS 관리 콘솔
    - IIS 관리 스크립트 및 도구
    - IIS 관리 서비스

4. **월드 와이드 웹 서비스를**선택하고 다음 옵션을 확인합니다.

     응용 프로그램 개발 기능:
    - .NET 확장성
    - ASP.NET
    - 일반적인 HTTP 기능: 사용 가능한 모든 기능 추가
    - 상태 및 진단: 사용 가능한 모든 기능 추가
    - 보안:
        - 기본 인증
        - Windows 인증

5. **Windows 프로세스 정품 인증 서비스와** 모든 자식을 선택합니다.

6. **기능에**대 한, **Microsoft .NET 프레임 워크를 확인 3.5.1**그리고 다음 옵션을 확인:

    - 윈도우 커뮤니케이션 재단 HTTP 정품 인증
    - 윈도우 통신 재단 비 HTTP 정품 인증

7. **기능에**대 한, **확인 Microsoft .NET 프레임 워크 4.6**그리고 다음 옵션을 확인:

   - 명명된 파이프 활성화
   - TCP 활성화
   - TCP 포트 공유

     ![역할 및 기능 마법사 추가: 역할 서비스](media/01-demo-roles.png)

8. 모든 옵션을 선택한 경우 **다음을** 클릭하여 설치합니다.

9. Windows 기능 후 ** \> 제어판 시스템 및 \> 보안 관리 도구로**이동 하여 **서비스를**선택합니다. 아래로 스크롤하여 다음 서비스가 실행되고 있는지 확인하고 **자동으로**설정합니다.

    - **NetTcpPortSharing**
    - **Net.Pipe 수신기 어댑터**
    - **넷.tcp 리스너 어댑터**

10. IIS 및 WAS 지원을 구성하려면 메뉴에서 **마이크로 포커스 엔터프라이즈 개발자 명령 프롬프트(64비트)를** 찾아 **관리자로**실행합니다.

11. 입력 **wassetup –i** 및 **Enter**를 누릅니다.

12. 스크립트가 실행된 후 창을 닫을 수 있습니다.

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server에 대한 로컬 시스템 계정 구성

일부 엔터프라이즈 Server 프로세스는 SQL Server에 로그인하고 데이터베이스 및 기타 개체를 만들 수 있어야 합니다. 이러한 프로세스는 로컬 시스템 계정을 사용하므로 해당 계정에 sysadmin 권한을 부여해야 합니다.

1. **SSMS를** 실행하고 **연결을** 클릭하여 Windows 인증을 사용하여 로컬 SQLEXPRESS 서버에 연결합니다. **서버 이름** 목록에서 사용할 수 있어야 합니다.

2. 왼쪽에서 **보안** 폴더를 확장하고 **로그인을 선택합니다.**

3. **NT 기관\\시스템을** 선택하고 **속성을**선택합니다.

4. **서버 역할을** 선택하고 **시스템 관리자를**확인합니다.

     ![SSMS 개체 탐색기 창: 로그인 속성](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo 데이터베이스 및 모든 개체 만들기

1. **Windows 탐색기를** 열고 **C로 이동:\\사용자\\공개\\문서\\\\마이크로 포커스 엔터프라이즈 개발자\\샘플\\메인프레임\\CICS\\DotNet\\BankDemo\\SQL**.

2. **BankDemoCreateAll.SQL** 파일의 내용을 클립보드에 복사합니다.

3. **SSMS**를 엽니다. 오른쪽에서 **서버를** 클릭하고 **새 쿼리를**선택합니다.

4. 클립보드의 내용을 **새 쿼리** 상자에 붙여넣습니다.

5. 쿼리 위의 **명령** 탭에서 **실행을** 클릭하여 SQL을 실행합니다.

쿼리는 오류 없이 실행되어야 합니다. 완료되면 BankDemo 응용 프로그램에 대한 샘플 데이터베이스가 있습니다.

![SQL쿼리1.sql 출력](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>데이터베이스 테이블및 개체가 만들어졌는지 확인합니다.

1. **BANKDEMO** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새로 고침을**선택합니다.

2. **데이터베이스를** 확장하고 **테이블을**선택합니다. 다음과 같은 것을 보아야 합니다.

     ![개체 탐색기에서 확장된 BANKDEMO 테이블](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>엔터프라이즈 개발자에서 응용 프로그램 빌드

1. Visual Studio를 열고 로그인합니다.

2. **파일** 메뉴 옵션에서 **프로젝트/솔루션 열기를**선택하고 **C:\\\\\\사용자\\공개\\문서\\마이크로\\포커스\\\\엔터프라이즈\\개발자 샘플 메인프레임 CICS DotNet BankDemo를**선택하고 **sln** 파일을 선택합니다.

3. 잠시 시간을 내어 개체를 검사합니다. 코볼 프로그램은 CBL 확장과 함께 솔루션 탐색기에서 복사책(CPY) 및 JCL과 함께 표시됩니다.

4. **BankDemo2** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정을**선택합니다.

    > [!NOTE]
    > BankDemo 프로젝트는 이 데모에 사용되지 않는 HCOSS(SQL Server의 호스트 호환성 옵션)를 사용합니다.

5. **솔루션 탐색기에서** **BankDemo2** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드를**선택합니다.

    > [!NOTE]
    > HCOSS가 구성되지 않았기 때문에 솔루션 수준에서 빌드하면 오류가 발생합니다.

6. 프로젝트가 빌드되면 **출력** 창을 검사합니다. 다음 이미지와 같아야 합니다.

     ![성공적인 빌드를 보여주는 출력 창](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>BankDemo 응용 프로그램을 지역 데이터베이스에 배포

1. 엔터프라이즈 개발자 명령 프롬프트(64비트)를 관리자로 엽니다.

2. **%PUBLIC %\\문서\\마이크로\\포커스\\엔터프라이즈\\개발자\\샘플\\메인\\프레임 CICS DotNet BankDemo로**이동합니다.

3. 명령 프롬프트에서 **bankdemodbdeploy를** 실행하고 데이터베이스가 배포할 매개 변수를 포함합니다.

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 뒤로\\슬래시()가 아닌 앞으로 슬래시(/)를 사용해야 합니다. 이 스크립트는 잠시 동안 실행됩니다.

![관리: 엔터프라이즈 개발자 명령 프롬프트 창](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>.NET에 대한 엔터프라이즈 관리자에서 BankDemo 지역 만들기

1. **.NET 관리 UI에 대한 엔터프라이즈 서버를 엽니다.**

2. Windows **시작** 메뉴에서 MMC 스냅인을 시작하려면 **.NET \> 관리자에 대한 마이크로 포커스 \> 엔터프라이즈 개발자 구성 엔터프라이즈 서버를 선택합니다.** (Windows 서버의 경우 **.NET \> 관리자용 마이크로 포커스 엔터프라이즈 개발자 엔터프라이즈 서버를 선택합니다.).**

3. 왼쪽 창에서 **영역** 컨테이너를 확장한 다음 **CICS를**마우스 오른쪽 단추로 클릭합니다.

4. **지역 정의를** 선택하여 (로컬) 데이터베이스에서 호스팅되는 **BANKDEMO라는**새 CICS 리전을 만듭니다.

5. 데이터베이스 서버 인스턴스를 공급하고 **다음을**클릭한 다음 지역 이름 **BANKDEMO를**입력합니다.

     ![지역 대화 상자 정의](media/07-demo-cics.png)

6. 지역 간 데이터베이스에 대한 지역 정의 파일을 선택하려면 **C:\\\\사용자 공개\\문서\\마이크로 포커스\\엔터프라이즈\\개발자\\샘플\\메인프레임 CICS\\DotNet\\BankDemo에서**지역 **\_은행 데모\_db.config를** 찾습니다.

     ![지역 정의 - 지역 이름: BANKDEMO](media/08-demo-cics.png)

7. **마침**을 클릭합니다.

## <a name="create-xa-resource-definitions"></a>XA 리소스 정의 만들기

1. .NET 관리 UI에 **대한 엔터프라이즈 서버의** 왼쪽 창에서 **시스템을**확장한 다음 **XA 리소스 정의를 확장합니다.** 이 설정은 지역이 엔터프라이즈 서버 및 응용 프로그램 데이터베이스와 상호 운용하는 방법을 정의합니다.

2. XA 리소스 정의를 마우스 오른쪽 단추로 클릭하고 **서버 인스턴스 추가를** **선택합니다.**

3. 드롭다운 상자에서 데이터베이스 **서비스 인스턴스를**선택합니다. 로컬 컴퓨터 SQLEXPRESS가 될 것입니다.

4. **XA 리소스\\정의(기계 이름 sqlexpress)** 컨테이너 아래에서 인스턴스를 선택하고 추가 **를**클릭합니다.

5. **데이터베이스 XA 리소스 정의를** 선택한 다음 **이름** 및 **영역에**대해 **BANKDEMO를** 입력합니다.

     ![새 데이터베이스 XA 리소스 정의 화면](media/09-demo-xa.png)

6. 타원 **(...**) 을 클릭하여 연결 문자열 마법사를 불러올 수 있습니다. **서버 이름에**대해 **(로컬)\\SQLEXPRESS를**입력합니다. **로그온의**경우 **Windows 인증을**선택합니다. 데이터베이스 이름의 경우 **BANKDEMO를 입력합니다.**

     ![연결 문자열 화면 편집](media/10-demo-string.png)

7. 연결을 테스트합니다.

## <a name="start-the-bankdemo-region"></a>뱅크데모 리전 시작

> [!NOTE]
> 첫 번째 단계는 중요 합니다: 방금 만든 XA 리소스 정의 사용 하 여 지역을 설정 해야 합니다.

1. **영역 컨테이너**아래의 **BANDEMO CICS 영역으로** 이동한 다음 **작업** 창에서 지역 시작 **파일 편집을** 선택합니다. SQL 속성으로 스크롤하여 **XA 리소스 이름에**대한 **뱅크데모를** 입력하거나 타원을 사용하여 선택합니다.

2. 변경 내용을 저장하려면 **저장** 아이콘을 클릭합니다.

3. **콘솔** 창에서 **BANKDEMO CICS 영역을** 마우스 오른쪽 단추로 클릭하고 **지역 시작/중지를**선택합니다.

4. 가운데 창에 나타나는 **영역 시작/중지** 상자 의 맨 아래에서 **시작을**선택합니다. 몇 초 후에 영역이 시작됩니다.

     ![SQL 시작/중지 상자](media/11-demo-sql.png)

     ![CICS 리전 뱅크데모 - 시작 화면](media/12-demo-cics.png)

## <a name="create-a-listener"></a>리스너 만들기

BankDemo 응용 프로그램에 액세스하는 TN3270 세션에 대한 리스너를 만듭니다.

1. 왼쪽 창에서 구성 **편집기를** 확장하고 **수신기를**선택합니다.

2. 파일 **열기** 아이콘을 클릭하고 **seelistener.exe.config** 파일을 선택합니다. 이 파일은 편집되며 엔터프라이즈 서버가 시작될 때마다 로드됩니다.

3. 이전에 정의된 두 지역(ESDEMO 및 JCLDEMO)을 확인합니다.

4. BANKDEMO에 대한 새 리전을 만들려면 **지역**오른쪽 단추를 클릭하고 **지역 추가를**선택합니다.

5. **뱅크데모 리전을**선택합니다.

6. **뱅크데모 지역을** 마우스 오른쪽 단추로 클릭하고 **채널 추가를**선택하여 TN3270 채널을 추가합니다.

7. **이름에**대 한 **TN3270을**입력 합니다. **포트의**경우 **9024를**입력합니다. ESDEMO 응용 프로그램은 포트 9230을 사용하므로 다른 포트를 사용해야 합니다.

8. 파일을 저장하려면 **저장** 아이콘을 클릭하거나 **파일** \> 저장 을 **선택합니다.**

9. 리스어를 시작하려면 **리스너 시작** 아이콘을 클릭하거나 듣기 시작 **옵션을** \> **선택합니다.**

     ![리스너 구성 편집기 창](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>BankDemo 응용 프로그램에 액세스하도록 룸바 구성

마지막으로 해야 할 일은 3270 에뮬레이터인 Rumba를 사용하여 3270 세션을 구성하는 것입니다. 이 단계를 사용하면 만든 수신기를 통해 BankDemo 응용 프로그램에 액세스할 수 있습니다.

1. Windows **시작** 메뉴에서 룸바 데스크톱을 시작합니다.

2. **연결** 메뉴 항목에서 **TN3270을**선택합니다.

3. IP 주소의 **삽입** 및 입력 **127.0.0.1을** 클릭하고 사용자 정의 포트의 **경우 9024를** 클릭합니다.

4. 대화 상자 하단에서 **연결을**클릭합니다. 검은색 CICS 화면이 나타납니다.

5. **은행을** 입력하여 BankDemo 응용 프로그램의 초기 3270 화면을 표시합니다.

6. 사용자 ID의 경우 **B0001을** 입력하고 암호의 경우 아무 것도 입력합니다. 첫 번째 화면 BANK20이 열립니다.

![메인프레임 디스플레이](media/14-demo.png)
![환영 화면 메인프레임 디스플레이 - 룸바 - 서브시스템 데모 화면](media/15-demo.png)

축하합니다! 이제 마이크로 포커스 엔터프라이즈 서버를 사용하여 Azure에서 CICS 응용 프로그램을 실행중입니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 Docker 컨테이너에서 엔터프라이즈 서버 실행](run-enterprise-server-container.md)
- [메인프레임 마이그레이션 - 포털](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [메인프레임을 Azure 마이그레이션으로 신비화](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
