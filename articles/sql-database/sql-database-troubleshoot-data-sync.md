---
title: "Azure SQL 데이터 동기화 문제 해결 | Microsoft Docs"
description: "Azure SQL 데이터 동기화와 관련된 일반적인 문제를 해결하는 방법을 알아봅니다."
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Azure SQL 데이터 동기화(미리 보기) 문제 해결

이 문서에서는 SQL 데이터 동기화(미리 보기) 팀에 알려진 현재 문제를 해결하는 방법을 설명합니다. 문제에 대한 해결 방법이 있으면 여기에 제공됩니다.

SQL 데이터 동기화의 개요는 [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화(미리 보기)](sql-database-sync-data.md)를 참조하세요.
                                                           
## <a name="my-client-agent-doesnt-work"></a>클라이언트 에이전트가 작동하지 않음

### <a name="description-and-symptoms"></a>설명 및 증상

클라이언트 에이전트를 사용하려고 할 때 다음 오류 메시지가 표시됩니다.

“예외와 함께 동기화가 실패했습니다. www.microsoft.com/.../05:GetBatchInfoResult 매개 변수를 deserialize하려는 동안 오류가 발생했습니다. 자세한 내용은 InnerException을 참조하세요.

“내부 예외 메시지: ‘Microsoft.Synchronization.ChangeBatch’ 형식은 기본 생성자가 없으므로 잘못된 컬렉션 형식입니다.”


### <a name="cause"></a>Cause

이 오류는 SQL 데이터 동기화(미리 보기)와 관련된 문제입니다.

문제의 가장 유력한 원인은 다음과 같습니다.

-   Windows 8 Developer Preview를 실행 중인 경우 또는

-   .NET 4.5가 설치되어 있는 경우.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

클라이언트 에이전트를 Windows 8 Developer Preview를 실행하지 않거나 .NET Framework 4.5가 설치되어 있지 않은 컴퓨터에 설치하도록 합니다.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>제거를 취소한 후 클라이언트 에이전트가 작동하지 않음

### <a name="description-and-symptoms"></a>설명 및 증상

클라이언트 에이전트가 제거를 취소했는데도 작동하지 않습니다.

### <a name="cause"></a>원인

이 문제는 SQL 데이터 동기화(미리 보기) 클라이언트 에이전트가 자격 증명을 저장하지 않기 때문에 발생합니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

두 가지 해결 방법이 있습니다.

-   먼저, services.msc를 사용하여 클라이언트 에이전트에 대한 자격 증명을 다시 입력합니다.

-   둘째, 이 클라이언트 에이전트를 제거하고 새로 설치합니다. 최신 클라이언트 에이전트를 [다운로드 센터](http://go.microsoft.com/fwlink/?linkid=221479)에서 다운로드하여 설치합니다.

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>내 데이터베이스가 에이전트 드롭다운에 나열되지 않음

### <a name="description-and-symptoms"></a>설명 및 증상

동기화 그룹에 기존 SQL Server 데이터베이스를 추가하려고 할 때 데이터베이스가 드롭다운에 나열되지 않습니다.

### <a name="cause"></a>원인

이 문제의 잠재적 원인에는 몇 가지가 있습니다.

-   클라이언트 에이전트와 동기화 그룹이 서로 다른 데이터 센터에 있습니다.

-   클라이언트 에이전트의 데이터베이스 목록이 최신 버전이 아닙니다.

### <a name="solution"></a>해결 방법

솔루션은 원인에 따라 달라집니다.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>클라이언트 에이전트와 동기화 그룹이 서로 다른 데이터 센터에 있는 경우

클라이언트 에이전트와 동기화 그룹이 모두 동일한 데이터 센터에 있어야 합니다. 다음 중 하나를 수행하여 이 구성을 설정할 수 있습니다.

-   동기화 그룹과 동일한 데이터 센터에 새 에이전트를 만듭니다. 그런 다음 데이터베이스를 해당 에이전트에 등록합니다. 자세한 정보는 [방법: SQL 데이터 동기화(미리 보기) 클라이언트 에이전트 설치](#install-a-sql-data-sync-client-agent)를 참조하세요.

-   현재 동기화 그룹을 삭제합니다. 그런 다음 에이전트와 동일한 데이터 센터에 다시 만듭니다.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>클라이언트 에이전트의 데이터베이스 목록이 최신 버전이 아닌 경우

클라이언트 에이전트 서비스를 중지한 다음 다시 시작합니다.
로컬 에이전트는 첫 번째 에이전트 키 전송 시에만 연관된 데이터베이스의 목록을 다운로드합니다. 후속 에이전트 키 제출에는 해당되지 않습니다. 따라서 에이전트가 이동하는 동안 등록된 데이터베이스는 원래 에이전트 인스턴스에 표시되지 않습니다.

## <a name="client-agent-doesnt-start-error-1069"></a>클라이언트 에이전트가 시작하지 않음(오류 1069)

### <a name="description-and-symptoms"></a>설명 및 증상

에이전트가 SQL Server를 호스팅하는 컴퓨터에서 실행되고 있지 않습니다. 에이전트를 수동으로 시작하려고 하면 오류 대화 상자에 “오류 1069: 서비스가 로그온 실패로 인해 시작되지 않았습니다.”라는 오류 메시지가 표시됩니다.

![데이터 동기화 오류 1069 대화 상자](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>원인

이 오류의 가능한 원인은 에이전트를 만들고 로그인 암호를 지정한 후 로컬 서버의 암호가 변경되었기 때문입니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

에이전트의 암호를 현재 서버 암호로 업데이트합니다.

1. SQL 데이터 동기화(미리 보기) 클라이언트 에이전트 미리 보기 서비스를 찾습니다.

    a. **시작**을 클릭합니다.

    b. 검색 상자에 “services.msc”를 입력합니다.

    c. 검색 결과에서 “서비스”를 클릭합니다.

    d. **서비스** 창에서 **SQL 데이터 동기화(미리 보기) 에이전트 미리 보기**에 대한 항목으로 스크롤합니다.

2. 항목을 마우스 오른쪽 단추로 클릭하고 **중지**를 선택합니다.

3. 항목을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

4. **SQL 데이터 동기화(미리 보기) 에이전트 미리 보기 속성** 창에서 **로그인** 탭을 클릭합니다.

5. 암호 텍스트 상자에 암호를 입력합니다.

6. 암호 확인 텍스트 상자에서 암호를 확인합니다.

7. **적용**을 클릭한 다음 **확인**을 클릭합니다.

8. **서비스** 창에서 **SQL 데이터 동기화(미리 보기) 에이전트 미리 보기** 서비스를 마우스 오른쪽 단추로 클릭한 다음 **시작**을 클릭합니다.

9. **서비스** 창을 닫습니다.

## <a name="i-get-a-disk-out-of-space-message"></a>“디스크 공간 부족” 메시지가 표시됨

### <a name="cause"></a>원인

“디스크 공간 부족” 메시지는 삭제되어야 하는 파일이 남아 있는 경우 나타날 수 있습니다. 이 조건은 바이러스 백신 소프트웨어로 인해 발생하거나 삭제 작업을 시도할 때 파일이 열리기 때문일 수 있습니다.

### <a name="solution"></a>해결 방법

해결 방법은 `%temp%`(`del \*sync\* /s`)에 있는 동기화 파일을 수동으로 삭제한 다음, 하위 디렉터리도 제거하는 것입니다.

> [!IMPORTANT]
> 동기화가 완료될 때까지 기다린 후 파일을 삭제하세요.

## <a name="i-cannot-delete-my-sync-group"></a>동기화 그룹을 삭제할 수 없음

### <a name="description-and-symptoms"></a>설명 및 증상

동기화 그룹을 삭제하려고 했지만 실패했습니다.

### <a name="causes"></a>원인

다음 중 하나가 동기화 그룹을 삭제하지 못한 원인일 수 있습니다.

-   클라이언트 에이전트가 오프라인 상태입니다.

-   클라이언트 에이전트가 설치되지 않았거나 누락되었습니다. 

-   데이터베이스가 오프라인 상태입니다. 

-   동기화 그룹이 프로비전 또는 동기화되는 중입니다. 

### <a name="solutions"></a>솔루션

동기화 그룹 삭제 오류를 해결하려면 다음을 확인합니다.

-   클라이언트 에이전트가 온라인 상태인지 확인한 다음 다시 시도합니다.

-   클라이언트 에이전트가 설치되지 않았거나 누락된 경우:

    a. 파일이 있는 경우 SQL 데이터 동기화(미리 보기) 설치 폴더에서 에이전트 XML 파일을 제거합니다.

    b. 에이전트를 같은/다른 온-프레미스 컴퓨터에 설치하고, 오프라인으로 표시되는 에이전트에 대해 생성된 포털에서 에이전트 키를 제출합니다.

-   **SQL 데이터 동기화(미리 보기) 서비스가 중지되었습니다.**

    a. **시작** 메뉴에서 서비스를 검색합니다.

    b. 검색 결과에서 서비스를 클릭합니다.

    c. **SQL 데이터 동기화(미리 보기) 미리 보기** 서비스를 찾습니다.

    d. 서비스 상태가 **중지됨**인 경우 서비스 이름을 마우스 오른쪽 단추로 클릭하고 드롭다운 메뉴에서 **시작**을 선택합니다.

-   SQL Databases 및 SQL Server 데이터베이스가 모두 온라인 상태인지 확인합니다.

-   프로비전 또는 동기화 프로세스가 완료될 때까지 기다립니다. 그런 다음 동기화 그룹 삭제를 다시 시도합니다.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>클라이언트 에이전트와 연결된 온-프레미스 데이터베이스에 대한 포털 UI에서 동기화에 실패함

### <a name="description-and-symptoms"></a>설명 및 증상

에이전트와 연결된 온-프레미스 데이터베이스에 대한 SQL 데이터 동기화(미리 보기) 포털 UI에서 동기화에 실패했습니다. 에이전트를 실행하는 로컬 컴퓨터에서 디스크 공간이 부족함을 나타내는 System.IO.IOException 오류가 이벤트 로그에 표시됩니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

%TEMP% 디렉터리가 있는 드라이브에 공간을 만듭니다.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>온-프레미스 SQL Server 데이터베이스를 등록 취소할 수 없음

### <a name="cause"></a>원인

대개 이미 삭제된 데이터베이스를 등록 취소하려고 시도하는 경우입니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

온-프레미스 SQL Server 데이터베이스를 등록 취소하려면 데이터베이스를 선택하고 **강제 삭제**를 클릭합니다.

이 작업으로 동기화 그룹에서 데이터베이스를 제거하지 못하면 다음 작업을 수행합니다.

1. 클라이언트 에이전트 호스트 서비스를 중지한 다음 다시 시작합니다.

    a. 시작 메뉴를 클릭합니다.

    b. 검색 상자에 *services.msc*를 입력합니다.

    c. 결과 창의 프로그램 섹션에서 **서비스**를 두 번 클릭합니다.

    d. **SQL 데이터 동기화(미리 보기)** 서비스를 찾아 마우스 오른쪽 단추로 클릭합니다.

    e. 서비스가 실행 중인 경우 중지합니다.

    f. 마우스 오른쪽 단추로 클릭하고 **시작**을 선택합니다.

    g. 데이터베이스가 더 이상 등록되지 않았는지 확인합니다. 더 이상 등록되지 않은 경우 완료된 것입니다. 그렇지 않은 경우 다음 단계를 진행합니다.

2. 클라이언트 에이전트 앱(SqlAzureDataSyncAgent)을 엽니다.

3. **자격 증명 편집**을 클릭하고 연결할 수 있도록 데이터베이스에 대한 자격 증명을 제공합니다.

4. 등록 취소를 진행합니다.

## <a name="i-cannot-submit-the-agent-key"></a>에이전트 키를 제출할 수 없음

### <a name="description-and-symptoms"></a>설명 및 증상

에이전트에 대한 키를 만들거나 다시 만든 후 SqlAzureDataSyncAgent 응용 프로그램을 통해 해당 키를 제출하려고 하지만 제출이 완료되지 않습니다.

![동기화 오류 대화 상자 - 에이전트 키를 제출할 수 없음](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

계속하기 전에 다음 조건에 해당하는 오류가 문제의 원인이 아닌지 확인합니다.

-   SQL 데이터 동기화(미리 보기) Windows 서비스가 실행 중입니다.

-   SQL 데이터 동기화(미리 보기) 미리 보기 Windows 서비스에 대한 서비스 계정에 네트워크 액세스 권한이 있습니다.

-   클라이언트 에이전트가 로케이터 서비스에 연결할 수 있습니다. 다음 레지스트리 키에 “https://locator.sync.azure.com/LocatorServiceApi.svc” 값이 있는지 확인합니다.

    -   x86 컴퓨터: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   x64 컴퓨터: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>원인

에이전트 키는 각 로컬 에이전트를 고유하게 식별합니다. 키는 작동하기 위해 다음 두 가지 조건을 충족해야 합니다.

-   SQL 데이터 동기화(미리 보기) 서버와 로컬 컴퓨터의 클라이언트 에이전트 키가 동일해야 합니다.

-   클라이언트 에이전트 키는 한 번만 사용할 수 있습니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

에이전트가 작동하지 않는 경우 이러한 조건 중 하나 이상이 충족되지 않기 때문입니다. 에이전트가 다시 작동하려면 다음을 수행합니다.

1. 새로운 키를 생성합니다.

2. 에이전트에 새 키를 적용합니다.

에이전트에 새 키를 적용하려면 다음 작업을 수행합니다.

1. 파일 탐색기를 사용하여 에이전트 설치 디렉터리로 이동합니다. 기본 설치 디렉터리는 `c:\\program files (x86)\\microsoft sql data sync`입니다.

2. `bin` 하위 디렉터리를 두 번 클릭합니다.

3. `SqlAzureDataSyncAgent` 응용 프로그램을 시작합니다.

4. **에이전트 키 제출**을 클릭합니다.

5. 제공된 공간에 클립보드의 키를 붙여넣습니다.

6. **확인**을 클릭합니다.

7. 프로그램을 닫습니다.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>시스템 서비스를 시작할 수 있는 권한이 없음

### <a name="cause"></a>원인

이 오류는 두 가지 상황에서 발생합니다.

-   사용자 이름 및/또는 암호가 올바르지 않는 경우

-   지정된 사용자 계정에 서비스로 로그온할 수 있는 충분한 권한이 없는 경우

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

사용자 계정에 서비스로 로그온할 수 있는 자격 증명을 부여합니다.

1. **시작 | 제어판 | 관리 도구 | 로컬 보안 정책 | 로컬 정책 | 사용자 권한 관리**로 이동합니다.

2. **서비스로 로그온** 항목을 찾아 클릭합니다.

3. **서비스로 로그온 속성** 대화 상자에 사용자 계정을 추가합니다.

4. **적용**을 클릭한 다음 **확인**을 클릭합니다.

5. 창을 닫습니다.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>로컬 동기화 에이전트 앱을 로컬 동기화 서비스에 연결할 수 없음

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

다음 단계를 수행합니다.

1. 앱을 종료합니다.

2. 구성 요소 서비스 패널을 엽니다.

    a. 작업 표시줄에서 검색 상자에 “services.msc”를 입력합니다.

    b. 검색 결과에서 “서비스”를 두 번 클릭합니다.

3. “SQL 데이터 동기화(미리 보기) 미리 보기” 서비스를 중지한 다음 다시 시작합니다.

4. 앱을 다시 시작합니다.

## <a name="install-uninstall-or-repair-fails"></a>설치, 제거 또는 복구 실패

### <a name="cause"></a>원인

오류에 대한 많은 잠재적 원인이 있습니다. 이 오류에 대한 특정 원인을 확인하려면 로그를 검토해야 합니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

발생한 오류에 대한 특정 원인을 찾으려면 Windows Installer 로그를 생성하고 검토해야 합니다. 명령줄에서 로깅을 켤 수 있습니다. 예를 들어, 다운로드한 AgentServiceSetup.msi 파일이 LocalAgentHost.msi라고 가정합니다. 다음 명령줄을 사용하여 로그 파일을 생성하고 검사합니다.

-   설치 시: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   제거 시: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

또한 Windows Installer에서 수행한 모든 설치에 대한 로깅을 사용할 수 있습니다. Microsoft 기술 자료 문서 [Windows Installer 로깅을 설정하는 방법](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)은 Windows Installer에 대한 로깅을 켜기 위한 원클릭 솔루션을 제공합니다. 또한 이러한 로그의 위치도 제공합니다.

## <a name="a-database-has-an-out-of-date-status"></a>데이터베이스가 “만료” 상태임

### <a name="cause"></a>원인

SQL 데이터 동기화(미리 보기)는 45일 이상(데이터베이스가 오프라인 상태가 된 시점부터 계산) 오프라인 상태인 데이터베이스를 서비스에서 제거합니다. 데이터베이스가 45일 이상 오프라인 상태였다가 다시 온라인 상태가 된 경우 상태는 “만료”로 설정됩니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

데이터베이스가 45일 이상 오프라인 상태가 되지 않도록 확인하여 “만료” 상태를 방지할 수 있습니다.

데이터베이스의 상태가 “만료”인 경우 다음 작업을 수행해야 합니다.

1. 동기화 그룹에서 “만료” 데이터베이스를 제거합니다.

2. 데이터베이스를 다시 동기화 그룹에 추가합니다.

> [!WARNING]
> 이 데이터베이스가 오프라인 상태일 때의 모든 변경 내용은 손실 됩니다.

## <a name="a-sync-group-has-an-out-of-date-status"></a>동기화 그룹이 “만료” 상태임

### <a name="cause"></a>원인

하나 이상의 변경 내용을 45일의 전체 유지 기간 동안 적용하지 못한 경우 동기화 그룹이 만료될 수 있습니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

“만료” 상태를 방지하려면 정기적으로 기록 뷰어에서 동기화 작업의 결과 확인하고 적용되지 않은 모든 변경 내용을 조사하여 해결합니다.

동기화 그룹의 상태가 “만료”인 경우 동기화 그룹을 삭제하고 다시 만들어야 합니다.

## <a name="i-see-erroneous-data-in-my-tables"></a>테이블에 잘못된 데이터가 표시됨

### <a name="description-and-symptoms"></a>설명 및 증상

이름이 동일하지만 스키마가 서로 다른 데이터베이스에서 테이블이 동기화에 포함된 경우 동기화 후에 이러한 테이블에 잘못된 데이터가 표시됩니다.

### <a name="cause-and-fix"></a>원인 및 해결 방법

SQL 데이터 동기화(미리 보기) 프로비전 프로세스는 이름이 같지만 스키마가 서로 다른 테이블에 동일한 추적 테이블을 사용합니다. 결과적으로, 두 테이블의 변경 내용이 동일한 추적 테이블에 반영되고 동기화하는 동안 잘못된 데이터의 원인이 되는 이 동작이 변경됩니다.

### <a name="resolution-or-workaround"></a>해결책 또는 해결 방법

서로 다른 스키마에 속해 있는 경우에도 동기화에 포함된 테이블의 이름이 서로 다르게 되어 있는지 확인합니다.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>동기화에 성공한 후 일관성 없는 기본 키 데이터가 표시됨

### <a name="description-and-symptoms"></a>설명 및 증상

동기화가 성공한 것으로 보고된 후 로그에 실패했거나 건너뛴 행이 없다고 표시되었는데, 기본 키 데이터가 동기화 그룹의 데이터베이스 간에 일치하지 않는 것으로 표시됩니다.

### <a name="cause"></a>원인

이 동작은 의도된 것입니다. 기본 키 열의 변경 내용은 기본 키가 변경된 행의 데이터와 일치하지 않습니다.

### <a name="resolution-or-workaround"></a>해결책 또는 해결 방법

이 문제를 방지하려면 기본 키에 있는 데이터가 변경되지 않도록 해야 합니다.

이 문제가 발생한 후에 해결하려면 영향을 받은 행을 동기화 그룹의 모든 끝점에서 드롭시킨 다음 행을 다시 삽입해야 합니다.

## <a name="i-see-a-significant-degradation-in-performance"></a>성능이 크게 저하됨

### <a name="description-and-symptoms"></a>설명 및 증상

데이터 동기화 UI도 시작할 수 없을 정도로 성능이 크게 저하되었습니다.

### <a name="cause"></a>원인

가장 가능성이 높은 원인은 동기화 루프입니다. 동기화 루프는 동기화 그룹 A에 의한 동기화가 동기화 B에 의한 동기화를 트리거할 때 발생하며, 결과적으로 동기화 그룹 A에 의한 동기화를 트리거합니다. 루프에 두 개 이상의 동기화 그룹이 포함되는 실제 상황은 더 복잡할 수 있으나, 중요한 사실은 동기화 그룹이 다른 동기화 그룹을 겹침으로써 동기화의 순환 트리거가 발생한다는 점입니다.

### <a name="resolution-or-workaround"></a>해결책 또는 해결 방법

최선의 해결책은 예방입니다. 동기화 그룹에 순환 참조가 포함되지 없는지 확인합니다. 하나의 동기화 그룹에 의해 동기화되는 행은 다른 동기화 그룹에서 동기화할 수 없습니다.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>연결된 온-프레미스 데이터베이스를 연결할 수 없는 경우 클라이언트 에이전트를 포털에서 삭제할 수 없음

### <a name="description-and-symptoms"></a>설명 및 증상

SQL 데이터 동기화(미리 보기) 클라이언트 에이전트에 등록된 로컬 끝점(즉, 데이터베이스)을 연결할 수 없는 경우 클라이언트 에이전트를 삭제할 수 없습니다.

### <a name="cause"></a>원인

연결할 수 없는 데이터베이스가 에이전트에 여전히 등록되어 있으므로 로컬 에이전트를 삭제할 수 없습니다. 에이전트를 삭제하려고 하면 삭제 프로세스는 데이터베이스에 연결하려고 시도하지만 실패합니다.

### <a name="resolution-or-workaround"></a>해결책 또는 해결 방법

“강제 삭제”를 사용하여 연결할 수 없는 데이터베이스를 삭제합니다.

> [!NOTE]
> “강제 삭제” 후 동기화 메타데이터 테이블이 남아 있는 경우 deprovisioningutil.exe를 사용하여 정리합니다.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>에이전트 제거/중지한 후 3분 이내에 동기화 그룹을 삭제할 수 없음

### <a name="description-and-symptoms"></a>설명 및 증상

동기화 그룹을 연결된 SQL 데이터 동기화(미리 보기) 클라이언트 에이전트를 제거/중지한 후 3분 이내에 삭제할 수 없습니다.

### <a name="resolution-or-workaround"></a>해결책 또는 해결 방법

1. 연결된 동기화 에이전트가 온라인 상태일 때 동기화 그룹을 제거합니다(권장).

2. 에이전트가 오프라인이지만 설치된 경우 온-프레미스 컴퓨터에서 온라인 상태로 전환합니다. 그런 다음 에이전트의 상태가 SQL 데이터 동기화(미리 보기) 포털에서 온라인으로 표시되기를 기다렸다가 동기화 그룹을 제거합니다.

3. 에이전트가 제거되어서 오프라인 상태인 경우 다음 작업을 수행합니다. 그런 다음 동기화 그룹 삭제를 다시 시도합니다.

    a.  파일이 있는 경우 SQL 데이터 동기화(미리 보기) 설치 폴더에서 에이전트 XML 파일을 제거합니다.

    b.  에이전트를 동일하거나 다른 온-프레미스 컴퓨터에 설치하고, 오프라인으로 표시되는 에이전트에 대해 생성된 포털에서 에이전트 키를 제출합니다.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>내 동기화 그룹이 처리 중 상태에서 중단됨

### <a name="description-and-symptoms"></a>설명 및 증상

SQL 데이터 동기화(미리 보기)의 동기화 그룹이 오랜 시간 처리 중 상태이며, 중지 명령에 응답하지 않고, 로그에 새 항목이 표시되지 않습니다.

### <a name="causes"></a>원인

다음 조건 중 하나로 인해 동기화 그룹이 처리 중 상태에서 중단될 수 있습니다.

-   **클라이언트 에이전트가 오프라인 상태입니다.** 클라이언트 에이전트가 온라인 상태인지 확인한 다음 다시 시도합니다.

-   **클라이언트 에이전트가 설치되지 않았거나 누락되었습니다.** 클라이언트 에이전트가 설치되지 않았거나 누락된 경우:

    1. 파일이 있는 경우 SQL 데이터 동기화(미리 보기) 설치 폴더에서 에이전트 XML 파일을 제거합니다.

    2. 에이전트를 동일한/다른 온-프레미스 컴퓨터에 설치합니다. 그런 다음 오프라인으로 표시된 에이전트에 대해 생성된 포털에서 에이전트 키를 제출합니다.

-   **SQL 데이터 동기화(미리 보기) 서비스가 중지되었습니다.**

    1. **시작** 메뉴에서 서비스를 검색합니다.

    2. 검색 결과에서 서비스를 클릭합니다.

    3. **SQL 데이터 동기화(미리 보기)** 서비스를 찾습니다.

    4. 서비스 상태가 **중지됨**인 경우 서비스 이름을 마우스 오른쪽 단추로 클릭하고 드롭다운 메뉴에서 **시작**을 선택합니다.

### <a name="solution-or-workaround"></a>솔루션 또는 해결 방법

문제를 해결할 수 없는 경우 동기화 그룹의 상태를 Microsoft 지원을 통해 다시 설정할 수 있습니다. 상태를 다시 설정하려면 구독 ID 및 다시 설정해야 하는 그룹에 대한 동기화 그룹 ID를 포함하여 [Azure SQL Database 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)에서 포럼 게시물을 작성합니다. Microsoft 지원 엔지니어가 게시물에 응답하여 상태가 다시 설정되면 알려드립니다.

## <a name="next-steps"></a>다음 단계
SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

-   [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화](sql-database-sync-data.md)
-   [Azure SQL 데이터 동기화 시작](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL 데이터 동기화 모범 사례](sql-database-best-practices-data-sync.md)

-   SQL Data Sync 구성 방법을 보여주는 전체 PowerShell 예제:
    -   [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [SQL 데이터 동기화 REST API 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database에 대한 자세한 내용은 다음을 참조하세요.

-   [SQL Database 개요](sql-database-technical-overview.md)
-   [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)
