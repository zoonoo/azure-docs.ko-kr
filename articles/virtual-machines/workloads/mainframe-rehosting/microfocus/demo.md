---
title: Azure Virtual Machines에서 마이크로 포커스 엔터프라이즈 개발자 4.0에 대 한 마이크로 포커스 BankDemo 설정
description: Azure Virtual Machines (Vm)에서 마이크로 포커스 BankDemo 응용 프로그램을 실행 하 여 마이크로 포커스 엔터프라이즈 서버 및 엔터프라이즈 개발자를 사용 하는 방법을 알아보세요.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7fb72b9a7d0d655f99d1e5cf194f7c6f26976a37
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508052"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Azure에서 마이크로 포커스 엔터프라이즈 개발자 4.0에 대 한 마이크로 포커스 BankDemo 설정

Azure에서 마이크로 포커스 Enterprise Server 4.0 및 Enterprise Developer 4.0를 설정 하는 경우 IBM z/OS 워크 로드의 배포를 테스트할 수 있습니다. 이 문서에서는 Enterprise Developer와 함께 제공 되는 샘플 응용 프로그램 인 CICS BankDemo를 설정 하는 방법을 보여 줍니다.

CICs는 여러 온라인 메인프레임 응용 프로그램에서 사용 하는 트랜잭션 플랫폼인 고객 정보 제어 시스템을 의미 합니다. BankDemo 응용 프로그램은 엔터프라이즈 서버 및 엔터프라이즈 개발자가 작동 하는 방식과 실제 응용 프로그램을 관리 하 고 배포 하는 방법을 배우는 데 유용 합니다.

> [!NOTE]
> 출시 예정: Azure Vm에서 [마이크로 포커스 엔터프라이즈 서버 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) 을 설정 하는 방법에 대 한 지침입니다.

## <a name="prerequisites"></a>필수 구성 요소

- [엔터프라이즈 개발자](set-up-micro-focus-azure.md)를 사용 하는 VM. 엔터프라이즈 개발자는 개발 및 테스트 목적으로 엔터프라이즈 서버의 전체 인스턴스를 보유 하 고 있습니다. 이 인스턴스는 데모에 사용 되는 Enterprise Server의 인스턴스입니다.

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express) 엔터프라이즈 개발자 VM에 다운로드 하 여 설치 합니다. Enterprise Server에는 CICS 영역을 관리 하기 위한 데이터베이스가 필요 하며 BankDemo 응용 프로그램은 BANKDEMO 라는 SQL Server 데이터베이스도 사용 합니다. 이 데모에서는 두 데이터베이스 모두에 대해 SQL Server Express를 사용 한다고 가정 합니다. 를 설치할 때 기본 설치를 선택 합니다.

- SSMS ( [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) ). SSMS는 데이터베이스를 관리 하 고 T-sql 스크립트를 실행 하는 데 사용 됩니다. 엔터프라이즈 개발자 VM에 다운로드 하 여 설치 합니다.

- 최신 Service Pack 또는 [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)를 제공 하는 [visual studio 2019](https://azure.microsoft.com/downloads/) . 무료로 다운로드할 수 있습니다.

- Rumba Desktop 또는 다른 3270 에뮬레이터.

## <a name="configure-the-windows-environment"></a>Windows 환경 구성

VM에 Enterprise Developer 4.0을 설치한 후에는 함께 제공 되는 Enterprise Server 인스턴스를 구성 해야 합니다. 이렇게 하려면 다음과 같이 몇 가지 추가 Windows 기능을 설치 해야 합니다.

1. RDP를 사용 하 여 만든 Enterprise Server 4.0 VM에 로그온 합니다.

2. **시작** 단추 옆의 **검색** 아이콘을 클릭 하 고 **Windows 기능**을 입력 합니다. 서버 관리자 역할 및 기능 추가 마법사가 열립니다.

3. **웹 서버 (IIS) 역할**을 선택 하 고 다음 옵션을 선택 합니다.

    - 웹 관리 도구
    - IIS 6 관리 호환성 (사용 가능한 모든 기능 선택)
    - IIS 관리 콘솔
    - IIS 관리 스크립트 및 도구
    - IIS 관리 서비스

4. **World Wide Web 서비스**를 선택 하 고 다음 옵션을 선택 합니다.

     응용 프로그램 개발 기능:
    - .NET 확장성
    - ASP.NET
    - 일반적인 HTTP 기능: 사용 가능한 모든 기능 추가
    - 상태 및 진단: 사용 가능한 모든 기능을 추가 합니다.
    - 보안:
        - 기본 인증
        - Windows 인증

5. **Windows Process Activation Service** 및 모든 해당 하위 항목을 선택 합니다.

6. **기능의**경우 **Microsoft .NET framework 3.5.1**를 확인 하 고 다음 옵션을 선택 합니다.

    - Windows Communication Foundation HTTP 활성화
    - Windows Communication Foundation 비 HTTP 활성화

7. **기능의**경우 **Microsoft .NET framework 4.6**을 확인 하 고 다음 옵션을 선택 합니다.

   - 명명 된 파이프 활성화
   - TCP 활성화
   - TCP 포트 공유

     ![역할 및 기능 추가 마법사: 역할 서비스](media/01-demo-roles.png)

8. 모든 옵션을 선택한 경우 **다음** 을 클릭 하 여 설치 합니다.

9. Windows 기능 후 **제어판 \> 시스템 및 보안 \> 관리 도구**로 이동 하 고 **서비스**를 선택 합니다. 아래로 스크롤하여 다음 서비스가 실행 중이 고 **자동**으로 설정 되어 있는지 확인 합니다.

    - **NetTcpPortSharing**
    - **Net.Pipe 수신기 어댑터**
    - **Net.tcp 수신기 어댑터**

10. IIS 및 지원 WAS를 구성 하려면 메뉴에서 **마이크로 포커스 엔터프라이즈 개발자 명령 프롬프트 (64 비트)** 를 찾아 **관리자 권한**으로 실행 합니다.

11. **Wassetup – i** 를 입력 하 고 **enter**키를 누릅니다.

12. 스크립트를 실행 한 후 창을 닫을 수 있습니다.

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server에 대 한 로컬 시스템 계정 구성

일부 엔터프라이즈 서버 프로세스에서는 SQL Server 로그인 하 고 데이터베이스 및 기타 개체를 만들 수 있어야 합니다. 이러한 프로세스는 로컬 시스템 계정을 사용 하므로 해당 계정에 sysadmin 권한을 부여 해야 합니다.

1. **SSMS** 를 시작 하 고 **연결** 을 클릭 하 여 Windows 인증을 사용 하 여 로컬 SQLEXPRESS 서버에 연결 합니다. **서버 이름** 목록에서 사용할 수 있어야 합니다.

2. 왼쪽에서 **보안** 폴더를 확장 하 고 **로그인**을 선택 합니다.

3. **NT AUTHORITY \\ SYSTEM** 을 선택 하 고 **속성**을 선택 합니다.

4. **서버 역할** 을 선택 하 고 **sysadmin**을 확인 합니다.

     ![SSMS 개체 탐색기 창: 로그인 속성](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo 데이터베이스 및 모든 해당 개체 만들기

1. **Windows 탐색기** 를 열고 **C: \\ Users \\ Public \\ Documents \\ 마이크로 포커스 \\ 엔터프라이즈 개발자 \\ 샘플 \\ 메인프레임 \\ cics \\ DotNet \\ BankDemo \\ SQL**로 이동 합니다.

2. **BankDemoCreateAll** 파일의 내용을 클립보드에 복사 합니다.

3. **SSMS**를 엽니다. 오른쪽에서 **서버** 를 클릭 하 고 **새 쿼리**를 선택 합니다.

4. 클립보드의 내용을 **새 쿼리** 상자에 붙여넣습니다.

5. 쿼리 위의 **명령** 탭에서 **실행** 을 클릭 하 여 SQL을 실행 합니다.

쿼리가 오류 없이 실행 되어야 합니다. 완료 되 면 BankDemo 응용 프로그램에 대 한 샘플 데이터베이스가 있습니다.

![Sqlquery1.sql 출력](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>데이터베이스 테이블 및 개체가 만들어졌는지 확인 합니다.

1. **BANKDEMO** 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **새로 고침**을 선택 합니다.

2. **데이터베이스** 를 확장 하 고 **테이블**을 선택 합니다. 다음과 유사한 내용이 표시 됩니다.

     ![개체 탐색기에서 확장 된 BANKDEMO 테이블](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>엔터프라이즈 개발자에 게 응용 프로그램 빌드

1. Visual Studio를 열고 로그인합니다.

2. **파일** 메뉴 옵션에서 **프로젝트/솔루션 열기**를 선택 하 고 **C: \\ Users \\ Public \\ Documents \\ 마이크로 포커스 \\ 엔터프라이즈 개발자 \\ 샘플 \\ 메인프레임 \\ cics \\ DotNet \\ BankDemo**로 이동한 다음, **sln** 파일을 선택 합니다.

3. 잠시 시간을 사용 하 여 개체를 검사 합니다. COBOL 프로그램은 CopyBooks (CPY) 및 JCL과 함께 CBL 확장과 함께 솔루션 탐색기에 표시 됩니다.

4. **BankDemo2** 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **시작 프로젝트로 설정**을 선택 합니다.

    > [!NOTE]
    > BankDemo 프로젝트는이 데모에 사용 되지 않는 HCOSS (SQL Server에 대 한 호스트 호환성 옵션)를 사용 합니다.

5. **솔루션 탐색기**에서 **BankDemo2** 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **빌드**를 선택 합니다.

    > [!NOTE]
    > HCOSS가 구성 되지 않았으므로 솔루션 수준에서 빌드하면 오류가 발생 합니다.

6. 프로젝트를 빌드할 때 **출력** 창을 검사 합니다. 다음 이미지와 같아야 합니다.

     ![성공한 빌드가 표시 되는 출력 창](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>BankDemo 응용 프로그램을 지역 데이터베이스에 배포 합니다.

1. 관리자 권한으로 Enterprise Developer 명령 프롬프트 (64 비트)를 엽니다.

2. **% PUBLIC% \\ Documents \\ 마이크로 Focus \\ Enterprise Developer \\ samples \\ 메인프레임 \\ cics \\ DotNet \\ BankDemo**로 이동 합니다.

3. 명령 프롬프트에서 **bankdemodbdeploy** 를 실행 하 고 배포할 데이터베이스에 대 한 매개 변수를 포함 합니다. 예를 들면 다음과 같습니다.

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 슬래시 (/)가 백슬래시 ()를 사용 하지 않는지 확인 \\ 합니다. 이 스크립트는 잠시 동안 실행 됩니다.

![관리: Enterprise 개발자 명령 프롬프트 창](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>엔터프라이즈 관리자의 .NET에서 BankDemo 지역 만들기

1. **.Net 관리 UI에 대 한 엔터프라이즈 서버** 를 엽니다.

2. MMC 스냅인을 시작 하려면 Windows **시작** 메뉴에서 **마이크로 포커스 엔터프라이즈 개발자 \> 구성 \> enterprise Server for .net Admin**을 선택 합니다. Windows Server의 경우 ** \> .net 관리자에 대 한 마이크로 포커스 엔터프라이즈 개발자 엔터프라이즈 서버**를 선택 합니다.

3. 왼쪽 창에서 **지역** 컨테이너를 확장 한 다음 **cics**를 마우스 오른쪽 단추로 클릭 합니다.

4. **지역 정의** 를 선택 하 여 (로컬) 데이터베이스에서 호스트 되는 **BANKDEMO**라는 새 cics 영역을 만듭니다.

5. 데이터베이스 서버 인스턴스를 지정 하 고 **다음**을 클릭 한 다음 지역 이름 **BANKDEMO**을 입력 합니다.

     ![지역 정의 대화 상자](media/07-demo-cics.png)

6. 지역 간 데이터베이스의 지역 정의 파일을 선택 하려면 **region \_ bankdemo \_db.config** in **C: \\ Users \\ Public \\ Documents \\ 마이크로 Focus \\ Enterprise Developer \\ Samples \\ 메인프레임 \\ cics \\ DotNet \\ bankdemo**을 찾습니다.

     ![지역 영역 이름 정의: BANKDEMO](media/08-demo-cics.png)

7. **Finish**를 클릭합니다.

## <a name="create-xa-resource-definitions"></a>XA 리소스 정의 만들기

1. **Enterprise Server for .Net 관리** UI의 왼쪽 창에서 **System**, **XA 리소스 정의**를 차례로 확장 합니다. 이 설정은 지역이 엔터프라이즈 서버 및 응용 프로그램 데이터베이스와 상호 작용 하는 방법을 정의 합니다.

2. **XA 리소스 정의** 를 마우스 오른쪽 단추로 클릭 하 고 **서버 인스턴스 추가**를 선택 합니다.

3. 드롭다운 상자에서 **데이터베이스 서비스 인스턴스**를 선택 합니다. 로컬 컴퓨터 SQLEXPRESS가 됩니다.

4. **XA 리소스 정의 (machinename \\ sqlexpress)** 컨테이너에서 인스턴스를 선택 하 고 **추가**를 클릭 합니다.

5. **데이터베이스 XA 리소스 정의** 를 선택한 다음 **이름** 및 **지역**에 **BANKDEMO** 를 입력 합니다.

     ![새 데이터베이스 XA 리소스 정의 화면](media/09-demo-xa.png)

6. 줄임표 (**...**)를 클릭 하 여 연결 문자열 마법사를 엽니다. **서버 이름**에 **(local) \\ SQLEXPRESS**를 입력 합니다. **로그온**에 대해 **Windows 인증**을 선택 합니다. 데이터베이스 이름에 **BANKDEMO** 을 입력 합니다.

     ![연결 문자열 편집 화면](media/10-demo-string.png)

7. 연결을 테스트 합니다.

## <a name="start-the-bankdemo-region"></a>BANKDEMO 영역 시작

> [!NOTE]
> 첫 번째 단계는 중요 합니다. 방금 만든 XA 리소스 정의를 사용 하도록 영역을 설정 해야 합니다.

1. **지역 컨테이너**아래에서 **BANDEMO cics 지역** 으로 이동한 다음, **작업** 창에서 **지역 시작 파일 편집** 을 선택 합니다. SQL 속성으로 아래로 스크롤하고 **XA 리소스 이름**에 **bankdemo** 를 입력 하거나 줄임표를 사용 하 여 선택 합니다.

2. **저장** 아이콘을 클릭 하 여 변경 내용을 저장 합니다.

3. **콘솔** 창에서 **BANKDEMO cics 영역** 을 마우스 오른쪽 단추로 클릭 하 고 **시작/중지 영역**을 선택 합니다.

4. 가운데 창에 표시 되는 **시작/중지 영역** 상자의 아래쪽에서 **시작**을 선택 합니다. 몇 초 후에 영역이 시작 됩니다.

     ![SQL 시작/중지 상자](media/11-demo-sql.png)

     ![CICS 지역 BANKDEMO-시작 화면](media/12-demo-cics.png)

## <a name="create-a-listener"></a>수신기 만들기

BankDemo 응용 프로그램에 액세스 하는 TN3270 세션에 대 한 수신기를 만듭니다.

1. 왼쪽 창에서 **구성 편집기** 를 확장 하 고 **수신기**를 선택 합니다.

2. **파일 열기** 아이콘을 클릭 하 고 **seelistener.exe.config** 파일을 선택 합니다. 이 파일은 편집 되며 Enterprise Server가 시작 될 때마다 로드 됩니다.

3. 이전에 정의 된 두 지역 (ESDEMO 및 JCLDEMO)을 확인 합니다.

4. BANKDEMO에 대 한 새 지역을 만들려면 **영역**을 마우스 오른쪽 단추로 클릭 하 고 **영역 추가**를 선택 합니다.

5. **BANKDEMO Region**을 선택 합니다.

6. **BANKDEMO Region** 을 마우스 오른쪽 단추로 클릭 하 고 **채널 추가**를 선택 하 여 TN3270 채널을 추가 합니다.

7. **이름**에 **TN3270**를 입력 합니다. **포트**에 대해 **9024**을 입력 합니다. ESDEMO 응용 프로그램은 포트 9230를 사용 하므로 다른 포트를 사용 해야 합니다.

8. 파일을 저장 하려면 **저장** 아이콘을 클릭 하거나 **파일** \> **저장**을 선택 합니다.

9. 수신기를 시작 하려면 수신기 **시작** 아이콘을 클릭 하거나 **옵션** \> **수신기 시작**을 선택 합니다.

     ![수신기 구성 편집기 창](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>BankDemo 응용 프로그램에 액세스 하도록 Rumba 구성

Rumba, 3270 에뮬레이터를 사용 하 여 3270 세션을 구성 해야 합니다. 이 단계를 통해 만든 수신기를 통해 BankDemo 응용 프로그램에 액세스할 수 있습니다.

1. Windows **시작** 메뉴에서 Rumba Desktop을 시작 합니다.

2. **연결** 메뉴 항목에서 **TN3270**을 선택 합니다.

3. **삽입** 을 클릭 하 고 IP 주소에 **127.0.0.1** 을 입력 하 고 사용자 정의 포트에 대해 **9024** 을 입력 합니다.

4. 대화 상자 아래쪽에서 **연결**을 클릭 합니다. 블랙 CICS 화면이 나타납니다.

5. BankDemo 응용 프로그램의 초기 3270 화면을 표시 하는 **bank** 를 입력 합니다.

6. 사용자 ID에 **B0001** 를 입력 하 고 암호에 대해 모두를 입력 합니다. 첫 번째 화면 BANK20 열립니다.

![메인프레임 디스플레이 시작 화면 ](media/14-demo.png)
 ![ 메인프레임 Rumba-하위 시스템 데모 화면](media/15-demo.png)

축하합니다! 이제 마이크로 포커스 엔터프라이즈 서버를 사용 하 여 Azure에서 CICS 응용 프로그램을 실행 하 고 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 Docker 컨테이너에서 엔터프라이즈 서버 실행](run-enterprise-server-container.md)
- [메인프레임 마이그레이션-포털](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [문제 해결](../../../troubleshooting/index.yml)
- [전문가가 제공 자세히 메인프레임에서 Azure로 마이그레이션](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
