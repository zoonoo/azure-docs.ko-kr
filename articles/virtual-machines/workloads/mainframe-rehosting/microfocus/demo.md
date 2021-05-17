---
title: Azure Virtual Machines에서 Micro Focus Enterprise Developer 4.0에 대한 Micro Focus CICS BankDemo 설정
description: Azure VM(Virtual Machines)에서 Micro Focus BankDemo 애플리케이션을 실행하여 Micro Focus Enterprise Server 및 Enterprise Developer를 사용하는 방법을 알아봅니다.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 9ef9dd27183b6c23762dcdc2d5a844123d35559a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045963"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Azure에서 Micro Focus Enterprise Developer 4.0에 대한 Micro Focus CICS BankDemo 설정

Azure에서 Micro Focus Enterprise Server 4.0과 Enterprise Developer 4.0를 설정하면 IBM z/OS 워크로드 배포를 테스트할 수 있습니다. 이 문서에서는 Enterprise Developer와 함께 제공되는 샘플 애플리케이션인 CICS BankDemo를 설정하는 방법을 설명합니다.

CICS는 고객 정보 제어 시스템이라는 의미로, 많은 온라인 메인프레임 애플리케이션에서 사용하는 트랜잭션 플랫폼입니다. BankDemo 애플리케이션은 그린 화면 터미널을 통해서 Enterprise Server와 Enterprise Developer가 작동하는 방식, 그리고 실제 애플리케이션을 어떻게 완전히 관리하고 배포하는지를 배우는 좋은 수단입니다.

> [!NOTE]
> 출시 예정: Azure VM에서 [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) 설정 지침.

## <a name="prerequisites"></a>필수 구성 요소

- [Enterprise Developer](set-up-micro-focus-azure.md)를 사용하는 VM. Enterprise Developer는 개발 및 테스트 목적으로 Enterprise Server의 전체 인스턴스를 탑재하고 있음을 명심하세요. 이 인스턴스는 데모에 사용되는 Enterprise Server의 인스턴스입니다.

- [SQL Server 2017 Express 버전](https://www.microsoft.com/sql-server/sql-server-editions-express). Enterprise Developer VM에 다운로드하고 설치합니다. Enterprise Server에는 CICS 영역을 관리하기 위한 데이터베이스가 필요하며 BankDemo 애플리케이션은 BANKDEMO라는 SQL Server 데이터베이스도 사용합니다. 이 데모에서는 두 데이터베이스 모두에 SQL Server Express를 사용한다고 가정합니다. 설치할 때 기본 설치를 선택합니다.

- [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) SSMS는 데이터베이스를 관리하고 T-SQL 스크립트를 실행하는 데 사용됩니다. Enterprise Developer VM에 다운로드하고 설치합니다.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/)에 최신 서비스 팩 또는 무료로 다운로드할 수 있는 [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) 추가.

- Rumba 데스크톱 또는 다른 3270 에뮬레이터.

## <a name="configure-the-windows-environment"></a>Windows 환경을 구성합니다

VM에 Enterprise Developer 4.0을 설치한 후에는 함께 제공되는 Enterprise Server 인스턴스를 구성해야 합니다. 이렇게 하려면 다음과 같은 몇 가지 추가 Windows 기능을 설치해야 합니다.

1. 만든 Enterprise Server 4.0 VM에 RDP를 사용하여 로그온합니다.

2. **시작** 단추 옆에 있는 **검색** 아이콘을 클릭하고 **Windows 기능** 을 입력합니다. 서버 관리자 역할 및 기능 추가 마법사가 열립니다.

3. **IIS(웹 서버) 역할** 을 선택하고 다음 옵션을 확인합니다.

    - 웹 관리 도구
    - IIS 6 관리 호환성(사용 가능한 모든 기능 선택)
    - IIS 관리 콘솔
    - IIS 관리 스크립트 및 도구
    - IIS 관리 서비스

4. **World Wide Web 서비스** 를 선택하고 다음 옵션을 확인합니다.

     애플리케이션 개발 기능:
    - .NET 확장성
    - ASP.NET
    - 일반적인 HTTP 기능: 사용 가능한 모든 기능 추가
    - 상태 및 진단: 사용 가능한 모든 기능 추가
    - 보안:
        - 기본 인증
        - Windows 인증

5. **Windows Process Activation Service** 및 모든 자식 요소를 선택합니다.

6. **기능** 의 경우 **Microsoft .NET 프레임워크 3.5.1** 을 확인하고 다음 옵션을 확인합니다.

    - Windows Communication Foundation HTTP 활성화
    - Windows Communication Foundation 비 HTTP 활성화

7. **기능** 의 경우 **Microsoft .NET 프레임워크 4.6** 을 확인하고 다음 옵션을 확인합니다.

   - 명명된 파이프 활성화
   - TCP 활성화
   - TCP 포트 공유

     ![역할 및 기능 추가 마법사: 역할 서비스](media/01-demo-roles.png)

8. 모든 옵션을 선택한 경우 **다음** 을 클릭하여 설치합니다.

9. Windows 기능 이후 **제어판 \> 시스템 및 보안 \> 관리 도구** 로 이동하고 **서비스** 를 선택합니다. 아래로 스크롤하여 다음 서비스가 실행 중이고 **자동** 으로 설정되어 있는지 확인합니다.

    - **NetTcpPortSharing**
    - **Net.Pipe 수신기 어댑터**
    - **Net.tcp 수신기 어댑터**

10. IIS 및 WAS 지원을 구성하려면 메뉴에서 **Micro Focus Enterprise 개발자 명령 프롬프트(64비트)** 를 찾아 **관리자 권한** 으로 실행합니다.

11. **wassetup –i** 를 입력하고 **엔터** 키를 누릅니다.

12. 스크립트를 실행한 후 창을 닫을 수 있습니다.

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server에 대한 로컬 시스템 계정 구성

일부 Enterprise Server 프로세스에서는 SQL Server에 로그인하고 데이터베이스 및 기타 개체를 만들 수 있어야 합니다. 이러한 프로세스는 로컬 시스템 계정을 사용하므로 해당 계정에 sysadmin 권한을 부여해야 합니다.

1. **SSMS** 를 시작하고 **연결** 을 클릭하여 Windows 인증을 사용해 로컬 SQLEXPRESS 서버에 연결합니다. **서버 이름** 목록에서 사용할 수 있어야 합니다.

2. 왼쪽에서 **보안** 폴더를 확장하고 **로그인** 을 선택합니다.

3. **NT AUTHORITY\\SYSTEM** 을 선택하고 **속성** 을 선택합니다.

4. **서버 역할** 을 선택하고 **sysadmin** 을 확인합니다.

     ![SSMS 개체 탐색기 창: 로그인 속성](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo 데이터베이스 및 모든 해당 개체 만들기

1. **Windows 탐색기** 를 열고 **C:\\사용자\\공용\\문서\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL** 로 이동합니다.

2. **BankDemoCreateAll.SQL** 파일의 내용을 클립보드에 복사합니다.

3. **SSMS** 를 엽니다. 오른쪽에서 **서버** 를 클릭하고 **새 쿼리** 를 선택합니다.

4. 클립보드의 콘텐츠를 **새 쿼리** 상자에 붙여넣습니다.

5. **실행** 을 쿼리 위의 **명령** 탭에서 클릭하여 SQL을 실행합니다.

쿼리가 오류 없이 실행되어야 합니다. 완료되면 BankDemo 애플리케이션용 샘플 데이터베이스가 있습니다.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>데이터베이스 테이블 및 개체가 만들어졌는지 확인합니다

1. **BANKDEMO** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새로 고침** 을 선택합니다.

2. **데이터베이스** 를 확장하고 **테이블** 을 선택합니다. 다음과 유사한 출력이 표시됩니다.

     ![개체 탐색기에서 확장된 BANKDEMO 테이블](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Enterprise Developer에서 애플리케이션 빌드

1. Visual Studio를 열고 로그인합니다.

2. **파일** 메뉴 옵션에서 **프로젝트/솔루션 열기** 를 선택하고 **C:\\사용자\\공용\\문서\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo** 로 이동해서 **sln** 파일을 선택합니다.

3. 개체 검사에 잠시 시간이 소요됩니다. COBOL 프로그램은 CPY(CopyBooks) 및 JCL과 함께 CBL 확장을 사용하여 솔루션 탐색기에 표시됩니다.

4. **BankDemo2** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.

    > [!NOTE]
    > BankDemo 프로젝트는 이 데모에 사용되지 않는 HCOSS(SQL Server에 대한 호스트 호환성 옵션)를 사용합니다.

5. **솔루션 탐색기** 에서 **BankDemo2** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드** 를 선택합니다.

    > [!NOTE]
    > HCOSS가 구성되지 않았으므로 솔루션 수준에서 빌드하면 오류가 발생합니다.

6. 프로젝트를 빌드할 때 **출력** 창을 검사합니다. 다음 이미지와 같아야 합니다.

     ![성공한 빌드가 표시되는 출력 창](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>BankDemo 애플리케이션을 영역 데이터베이스에 배포합니다

1. 관리자 권한으로 Enterprise 개발자 명령 프롬프트(64비트)를 엽니다.

2. **%공용%\\문서\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo** 로 이동합니다.

3. 명령 프롬프트에서 **bankdemodbdeploy** 를 실행하고 배포할 데이터베이스에 대한 매개 변수를 포함합니다. 예를 들면 다음과 같습니다.

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 백슬래시(\\)가 아닌 슬래시(/)를 사용하고 있는지 확인합니다. 이 스크립트는 잠시 동안 실행됩니다.

![관리: Enterprise 개발자 명령 프롬프트 창](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>.NET용 Enterprise Administrator에서 BankDemo 영역 만들기

1. **Enterprise Server for .NET 관리** UI를 엽니다.

2. MMC 스냅인을 시작하려면 Windows **시작** 메뉴에서 **Micro Focus Enterprise Developer \> 구성 \>Enterprise Server for .NET 관리자** 를 선택합니다. Windows Server의 경우에는 **Micro Focus Enterprise Developer \>Enterprise Server for .NET 관리자** 를 선택합니다.

3. 왼쪽 창에서 **영역** 컨테이너를 확장한 다음 **CICS** 를 마우스 오른쪽 단추로 클릭합니다.

4. **영역 정의** 를 선택하여 로컬 데이터베이스에서 호스트되는 **BANKDEMO** 라는 새 CICS 영역을 만듭니다.

5. 데이터베이스 서버 인스턴스를 제공하고 **다음** 을 클릭한 다음 영역 이름 **BANKDEMO** 를 입력합니다.

     ![영역 정의 대화 상자](media/07-demo-cics.png)

6. 영역 간 데이터베이스의 영역 정의 파일을 선택하려면 **region\_bankdemo\_db.config** 를 **C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo** 에서 찾습니다.

     ![영역 정의 - 영역 이름: BANKDEMO](media/08-demo-cics.png)

7. **Finish** 를 클릭합니다.

## <a name="create-xa-resource-definitions"></a>XA 리소스 정의 만들기

1. **Enterprise Server for .Net 관리** UI의 왼쪽 창에서 **시스템**, **XA 리소스 정의** 를 차례로 확장합니다. 이 설정은 영역이 Enterprise Server 및 애플리케이션 데이터베이스와 상호 작용하는 방법을 정의합니다.

2. **XA 리소스 정의** 를 마우스 오른쪽 단추로 클릭하고 **서버 인스턴스 추가** 를 선택합니다.

3. 드롭다운 상자에서 **데이터베이스 서비스 인스턴스** 를 선택합니다. 로컬 컴퓨터 SQLEXPRESS가 됩니다.

4. 인스턴스를 **XA 리소스 정의(machinename\\sqlexpress)** 컨테이너에서 선택하고 **추가** 를 클릭합니다.

5. **데이터베이스 XA 리소스 정의** 를 선택한 다음 **BANKDEMO** 를 **이름** 과 **영역** 에 입력합니다.

     ![새 데이터베이스 XA 리소스 정의 화면](media/09-demo-xa.png)

6. 줄임표( **...** )를 클릭하여 연결 문자열 마법사를 브링업합니다. **서버 이름** 에 **(local)\\SQLEXPRESS** 를 입력합니다. **로그온** 하려면 **Windows 인증** 을 선택합니다. 데이터베이스 이름에 **BANKDEMO** 를 입력합니다.

     ![연결 문자열 편집 화면](media/10-demo-string.png)

7. 연결을 테스트합니다.

## <a name="start-the-bankdemo-region"></a>BANKDEMO 영역 시작

> [!NOTE]
> 첫 번째 단계가 중요합니다. 영역이 방금 만든 XA 리소스 정의를 사용하도록 설정해야 합니다.

1. **영역 컨테이너** 의 아래에서 **BANDEMO CICS 영역** 으로 이동하고, **영역 시작 파일 편집** 을 **작업** 창에서 선택합니다. 아래로 스크롤해 SQL 속성으로 이동해서 **bankdemo** 를 **XA 리소스 이름** 에 입력하거나 줄임표를 사용해 선택하세요.

2. **저장** 아이콘을 클릭해 변경 내용을 저장합니다.

3. **BANKDEMO CICS 영역** 을 **콘솔** 창에서 마우스 오른쪽 단추로 클릭해서 **영역 시작/중지** 를 선택합니다.

4. 가운데 창에 표시되는 **영역 시작/중지** 상자의 아래쪽에서 **시작** 을 선택합니다. 몇 초 후에 영역이 시작됩니다.

     ![SQL 시작/중지 상자](media/11-demo-sql.png)

     ![CICS 영역 BANKDEMO - 시작된 화면](media/12-demo-cics.png)

## <a name="create-a-listener"></a>수신기 만들기

BankDemo 애플리케이션에 액세스하는 TN3270 세션용 수신기를 만듭니다.

1. 왼쪽 창에서 **구성 편집기** 를 확장하고 **수신기** 를 선택합니다.

2. **파일 열기** 아이콘을 클릭하고 **seelistener.exe.config** 파일을 선택합니다. 이 파일은 편집되어 Enterprise Server가 시작될 때마다 로드됩니다.

3. 이전에 정의된 두 영역(ESDEMO 및 JCLDEMO)을 확인합니다.

4. BANKDEMO의 새 영역을 만들려면 **영역** 을 마우스 오른쪽 단추로 클릭하고 **영역 추가** 를 선택합니다.

5. **BANKDEMO 영역** 을 선택합니다.

6. **BANKDEMO 영역** 을 마우스 오른쪽 단추로 클릭하고 **채널 추가** 를 선택하여 TN3270 채널을 추가합니다.

7. **이름** 에 **TN3270** 을 입력합니다. **포트** 에 **9024** 를 입력합니다. ESDEMO 애플리케이션은 포트 9230을 사용하므로 다른 포트를 사용해야 합니다.

8. 파일을 저장하려면 **저장** 아이콘을 클릭하거나 **파일** \> **저장** 을 선택합니다.

9. 수신기를 시작하려면 **수신기 시작** 아이콘을 클릭하거나 **옵션** \> **수신기 시작** 을 선택합니다.

     ![수신기 구성 편집기 창](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>BankDemo 애플리케이션에 액세스하도록 Rumba 구성

마지막으로 3270 에뮬레이터인 Rumba를 사용하여 3270 세션을 구성해야 합니다. 이 단계에서 만든 수신기를 통해 BankDemo 애플리케이션에 액세스할 수 있습니다.

1. Windows **시작** 메뉴에서 Rumba 데스크톱을 시작합니다.

2. **연결** 메뉴 항목에서 **TN3270** 을 선택합니다.

3. **삽입** 을 클릭하고 IP 주소에 **127.0.0.1** 을 입력한 후 사용자 정의 포트에 **9024** 를 입력합니다.

4. 대화 상자의 아래쪽에서 **연결** 을 클릭합니다. 검은색 CICS 화면이 나타납니다.

5. BankDemo 애플리케이션의 초기 3270 화면을 표시하는 **bank** 를 입력합니다.

6. 사용자 ID에 **B0001** 을 입력하고 암호로 무엇이든 입력합니다. 첫 번째 화면 BANK20이 열립니다.

![메인프레임 디스플레이 시작 화면](media/14-demo.png)
![메인프레임 디스플레이 - Rumba - 하위 시스템 데모 화면](media/15-demo.png)

축하합니다! 이제 Micro Focus Enterprise Server를 사용하여 Azure에서 CICS 애플리케이션을 실행하고 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 Docker 컨테이너에서 Enterprise Server 실행](run-enterprise-server-container.md)
- [메인프레임 마이그레이션 - 포털](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [문제 해결](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [메인프레임에서 Azure로 마이그레이션 쉽게 설명하기](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)