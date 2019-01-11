---
title: Azure SQL 데이터 동기화용 데이터 동기화 에이전트 | Microsoft Docs
description: Azure SQL 데이터 동기화용 데이터 동기화 에이전트를 설치 및 실행하여 온-프레미스 SQL Server 데이터베이스와 데이터를 동기화하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/12/2018
ms.openlocfilehash: b5916b781c636752ada6898cfa0c03bc02891501
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715021"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Azure SQL 데이터 동기화용 데이터 동기화 에이전트

Azure SQL 데이터 동기화용 데이터 동기화 에이전트를 설치 및 구성하여 온-프레미스 SQL Server 데이터베이스와 데이터를 동기화합니다. SQL 데이터 동기화에 대한 자세한 내용은 [SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화](sql-database-sync-data.md)를 참조하세요.

> [!IMPORTANT]
> Azure SQL 데이터 동기화는 이때 Azure SQL Database Managed Instance를 지원하지 **않습니다**.

## <a name="download-and-install"></a>다운로드 및 설치

데이터 동기화 에이전트를 다운로드하려면 [SQL Azure 데이터 동기화 에이전트](https://www.microsoft.com/download/details.aspx?id=27693)로 이동합니다.

### <a name="install-silently"></a>자동 설치

명령 프롬프트에서 데이터 동기화 에이전트를 자동으로 설치하려면 다음 예제와 비슷한 명령을 입력합니다. 다운로드한 .msi 파일의 파일 이름을 확인하고, **TARGETDIR** 및 **SERVICEACCOUNT** 인수에 대한 고유한 값을 제공합니다.

- **TARGETDIR**에 대한 값을 제공하지 않으면 기본값은 `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`입니다.

- `LocalSystem`을 **SERVICEACCOUNT**의 값으로 제공하는 경우, 온-프레미스 SQL Server에 연결하도록 에이전트를 구성할 때 SQL Server 인증을 사용합니다.

- 도메인 사용자 계정 또는 로컬 사용자 계정을 **SERVICEACCOUNT**의 값으로 제공하는 경우, **SERVICEPASSWORD** 인수를 사용하여 암호를 제공해야 합니다. 예: `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>온-프레미스 SQL Server와 데이터 동기화

하나 이상의 온-프레미스 SQL Server 데이터베이스와 데이터를 동기화할 수 있도록 데이터 동기화 에이전트를 구성하려면 [온-프레미스 SQL Server 데이터베이스 추가](sql-database-get-started-sql-data-sync.md#add-on-prem)를 참조하세요.

## <a name="agent-faq"></a> 데이터 동기화 에이전트 FAQ

### <a name="why-do-i-need-a-client-agent"></a>클라이언트 에이전트가 왜 필요한가요?

SQL 데이터 동기화 서비스는 클라이언트 에이전트를 통해 SQL Server 데이터베이스와 통신합니다. 이 보안 기능은 방화벽 뒤에 있는 데이터베이스와의 직접 통신을 방지합니다. SQL 데이터 동기화 서비스가 에이전트와 통신할 때는 암호화된 연결과 고유 토큰 또는 *에이전트 키*를 사용합니다. SQL Server 데이터베이스는 연결 문자열과 에이전트 키를 사용하여 에이전트를 인증합니다. 이러한 설계는 데이터에 높은 수준의 보안을 제공합니다.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>실행할 수 있는 로컬 에이전트의 UI의 인스턴스는 얼마나 되나요?

UI 인스턴스는 하나만 실행할 수 있습니다.

### <a name="how-can-i-change-my-service-account"></a>내 서비스 계정은 어떻게 변경하나요?

클라이언트 에이전트를 설치한 후 서비스 계정을 변경하는 유일한 방법은 제거한 후 새 서비스 계정으로 새 클라이언트 에이전트를 설치하는 것뿐입니다.

### <a name="how-do-i-change-my-agent-key"></a>내 에이전트 키는 어떻게 변경하나요?

에이전트 키는 에이전트에서 한 번만 사용할 수 있습니다. 제거 후 새 에이전트를 설치하면 재사용할 수 없고 여러 에이전트에서 사용할 수도 없습니다. 기존 에이전트에 대해 새 키를 만들어야 할 경우, 클라이언트 에이전트와 SQL 데이터 동기화 서비스에 같은 키가 기록되도록 해야 합니다.

### <a name="how-do-i-retire-a-client-agent"></a>클라이언트 에이전트는 어떻게 사용 중지하나요?

에이전트를 즉시 무효화하거나 사용 중지하려면 포털에서 키를 다시 생성하고 에이전트 UI에는 제출하지 않습니다. 키를 다시 생성하면 해당 에이전트가 온라인이던 오프라인이던 관계없이 이전 키가 무효화됩니다.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>클라이언트 에이전트를 어떻게 다른 컴퓨터로 이동하나요?

현재 실행 중인 컴퓨터가 아닌 다른 컴퓨터에서 로컬 에이전트를 실행하려면 다음을 수행합니다.

1. 원하는 컴퓨터에 에이전트를 설치합니다.
2. SQL 데이터 동기화 포털에 로그인하고 새 에이전트에 대해 에이전트 키를 다시 생성합니다.
3. 새 에이전트의 UI를 사용하여 새 에이전트 키를 제출합니다.
4. 클라이언트 에이전트가 앞서 등록된 온-프레미스 데이터베이스 목록을 다운로드하는 동안 기다립니다.
5. 연결할 수 없다고 표시된 모든 데이터베이스에 대해 데이터베이스 자격 증명을 제공합니다. 이러한 데이터베이스는 에이전트가 설치된 새 컴퓨터에서 연결할 수 있어야 합니다.

## <a name="agent-tshoot"></a> 데이터 동기화 에이전트 문제 해결

- [클라이언트 에이전트의 설치, 제거 또는 복구 실패](#agent-install)

- [제거를 취소한 후 클라이언트 에이전트가 작동하지 않음](#agent-uninstall)

- [내 데이터베이스가 에이전트 목록에 나열되지 않음](#agent-list)

- [클라이언트 에이전트가 시작하지 않음(오류 1069)](#agent-start)

- [에이전트 키를 제출할 수 없음](#agent-key)

- [연결된 온-프레미스 데이터베이스를 연결할 수 없는 경우, 클라이언트 에이전트를 포털에서 삭제할 수 없음](#agent-delete)

- [로컬 동기화 에이전트 앱을 로컬 동기화 서비스에 연결할 수 없음](#agent-connect)

### <a name="agent-install"></a> 클라이언트 에이전트의 설치, 제거 또는 복구 실패

- **원인**. 대부분의 시나리오에서 이 오류가 발생할 수 있습니다. 이 오류에 대한 특정 원인을 확인하려면 로그를 검토합니다.

- **해결 방법**. 오류의 특정 원인을 찾으려면 Windows Installer 로그를 생성하고 검토합니다. 명령 프롬프트에서 로깅을 켤 수 있습니다. 예를 들어 다운로드한 설치 파일이 `SQLDataSyncAgent-2.0-x86-ENU.msi`인 경우 다음 명령줄을 사용하여 로그 파일을 생성하고 검사합니다.

    -   설치 시: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
    -   제거 시: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    또한 Windows Installer에서 수행한 모든 설치에 대한 로깅을 켤 수 있습니다. Microsoft 기술 자료 문서 [Windows Installer 로깅을 설정하는 방법](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)은 Windows Installer에 대한 로깅을 켜기 위한 원클릭 솔루션을 제공합니다. 또한 로그의 위치도 제공합니다.

### <a name="agent-uninstall"></a> 제거를 취소한 후 클라이언트 에이전트가 작동하지 않음

클라이언트 에이전트가 제거를 취소했는데도 작동하지 않습니다.

- **원인**. 이 문제는 SQL 데이터 동기화 클라이언트 에이전트가 자격 증명을 저장하지 않기 때문에 발생합니다.

- **해결 방법**. 다음 두 가지 해결 방법을 시도할 수 있습니다.

    -   services.msc를 사용하여 클라이언트 에이전트에 대한 자격 증명을 다시 입력합니다.
    -   이 클라이언트 에이전트를 제거하고 새로 설치합니다. 최신 클라이언트 에이전트를 [다운로드 센터](https://go.microsoft.com/fwlink/?linkid=221479)에서 다운로드하여 설치합니다.

### <a name="agent-list"></a> 내 데이터베이스가 에이전트 목록에 나열되지 않음

동기화 그룹에 기존 SQL Server 데이터베이스를 추가하려고 할 때 데이터베이스가 에이전트 목록에 나타나지 않습니다.

이러한 시나리오에서 다음 오류가 발생할 수 있습니다.

- **원인**. 클라이언트 에이전트와 동기화 그룹이 서로 다른 데이터 센터에 있는 경우

- **해결 방법**. 클라이언트 에이전트와 동기화 그룹이 같은 데이터 센터에 있어야 합니다. 이렇게 설정하려는 경우 다음 두 가지 옵션이 있습니다.

    -   동기화 그룹이 있는 데이터 센터에 새 에이전트를 만듭니다. 그런 다음 데이터베이스를 해당 에이전트에 등록합니다.
    -   현재 동기화 그룹을 삭제합니다. 그런 다음 에이전트가 있는 데이터 센터에서 동기화 그룹을 다시 만듭니다.

- **원인**. 클라이언트 에이전트의 데이터베이스 목록이 최신 버전이 아닌 경우

- **해결 방법**. 클라이언트 에이전트 서비스를 중지한 다음 다시 시작합니다.

    로컬 에이전트는 첫 번째 에이전트 키 전송 시에만 연관된 데이터베이스의 목록을 다운로드합니다. 후속 에이전트 키 제출에서는 연관된 데이터베이스 목록이 다운로드되지 않습니다. 에이전트가 이동하는 동안 등록된 데이터베이스는 원래 에이전트 인스턴스에 표시되지 않습니다.

### <a name="agent-start"></a> 클라이언트 에이전트가 시작하지 않음(오류 1069)

에이전트가 SQL Server를 호스트하는 컴퓨터에서 실행되고 있지 않습니다. 에이전트를 수동으로 시작하려고 하면 대화 상자에 “오류 1069: 서비스가 로그온 실패로 인해 시작되지 않았습니다.”라는 메시지가 표시됩니다.

![데이터 동기화 오류 1069 대화 상자](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **원인**. 이 오류의 가능한 원인은 에이전트 및 에이전트 암호를 만든 후 로컬 서버의 암호가 변경되었기 때문입니다.

- **해결 방법**. 에이전트의 암호를 현재 서버 암호로 업데이트합니다.

  1. SQL 데이터 동기화 클라이언트 에이전트 서비스를 찾습니다.  
    a. **시작**을 선택합니다.  
    b. 검색 상자에 **services.msc**를 입력합니다.  
    다. 검색 결과에서 **서비스**선택합니다.  
    d. **서비스** 창에서 **SQL 데이터 동기화 에이전트**에 대한 항목으로 스크롤합니다.  
  1. 마우스 오른쪽 단추로 **SQL 데이터 동기화**를 클릭한 후 **중지**를 선택합니다.
  1. 마우스 오른쪽 단추로 **SQL 데이터 동기화**를 클릭한 후 **속성**을 선택합니다.
  1. **SQL 데이터 동기화 에이전트 속성**에서 **로그인** 탭을 선택합니다.
  1. **암호** 상자에 암호를 입력합니다.
  1. **암호 확인** 상자에 암호를 다시 입력합니다.
  1. **적용**을 선택한 다음 **확인**을 선택합니다.
  1. **서비스** 창에서 **SQL 데이터 동기화 에이전트** 서비스를 마우스 오른쪽 단추로 클릭한 다음, **시작**을 클릭합니다.
  1. **서비스** 창을 닫습니다.

### <a name="agent-key"></a> 에이전트 키를 제출할 수 없음

에이전트에 대한 키를 만들거나 다시 만든 후 SqlAzureDataSyncAgent 애플리케이션을 통해 해당 키를 제출하려고 하지만, 제출이 완료되지 않습니다.

![동기화 오류 대화 상자 - 에이전트 키를 제출할 수 없음](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **필수 조건**. 계속 진행하기 전에 다음 필수 구성 요소를 확인합니다.

  - SQL 데이터 동기화 Windows 서비스가 실행 중입니다.

  - SQL 데이터 동기화 Windows 서비스에 대한 서비스 계정에 네트워크 액세스 권한이 있습니다.

  - 아웃바운드 1433 포트가 로컬 방화벽 규칙에서 열립니다.

  - 로컬 IP는 메타데이터 데이터베이스 동기화에 대한 서버 또는 데이터베이스 방화벽 규칙에 추가됩니다.

- **원인**. 에이전트 키는 각 로컬 에이전트를 고유하게 식별합니다. 키는 다음 두 가지 조건을 충족해야 합니다.

  -   SQL 데이터 동기화 서버와 로컬 컴퓨터의 클라이언트 에이전트 키가 동일해야 합니다.
  -   클라이언트 에이전트 키는 한 번만 사용할 수 있습니다.

- **해결 방법**. 에이전트가 작동하지 않는 경우 이러한 조건 중 하나 이상이 충족되지 않기 때문입니다. 에이전트가 다시 작동하려면 다음을 수행합니다.

  1. 새로운 키를 생성합니다.
  1. 에이전트에 새 키를 적용합니다.

  에이전트에 새 키를 적용하려면

  1. 파일 탐색기에서 에이전트 설치 디렉터리로 이동합니다. 기본 설치 디렉터리는 C:\\Program Files (x86)\\Microsoft SQL Data Sync입니다.
  1. bin 하위 디렉터리를 두 번 클릭합니다.
  1. SqlAzureDataSyncAgent 애플리케이션을 엽니다.
  1. **에이전트 키 제출**을 선택합니다.
  1. 제공된 공간에 클립보드의 키를 붙여 넣습니다.
  1. **확인**을 선택합니다.
  1. 프로그램을 닫습니다.

### <a name="agent-delete"></a> 연결된 온-프레미스 데이터베이스를 연결할 수 없는 경우, 클라이언트 에이전트를 포털에서 삭제할 수 없음

SQL 데이터 동기화 클라이언트 에이전트에 등록된 로컬 엔드포인트(즉, 데이터베이스)에 연결할 수 없는 경우 클라이언트 에이전트를 삭제할 수 없습니다.

- **원인**. 연결할 수 없는 데이터베이스가 에이전트에 여전히 등록되어 있으므로 로컬 에이전트를 삭제할 수 없습니다. 에이전트를 삭제하려고 하면 삭제 프로세스는 데이터베이스에 연결하려고 시도하지만 실패합니다.

- **해결 방법**. “강제 삭제”를 사용하여 연결할 수 없는 데이터베이스를 삭제합니다.

> [!NOTE]
> “강제 삭제” 후 동기화 메타데이터 테이블이 남아 있는 경우, `deprovisioningutil.exe`를 사용하여 정리합니다.

### <a name="agent-connect"></a> 로컬 동기화 에이전트 앱을 로컬 동기화 서비스에 연결할 수 없음

- **해결 방법**. 다음 단계를 수행합니다.

  1. 앱을 종료합니다.  
  1. 구성 요소 서비스 패널을 엽니다.  
    a. 작업 표시줄에서 검색 상자에 **services.msc**를 입력합니다.  
    b. 검색 결과에서 **서비스**를 두 번 클릭합니다.  
  1. **SQL 데이터 동기화** 서비스를 중지합니다.
  1. **SQL 데이터 동기화** 서비스를 다시 시작합니다.  
  1. 앱을 다시 엽니다.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>명령 프롬프트에서 데이터 동기화 에이전트 실행

명령 프롬프트에서 다음과 같은 데이터 동기화 에이전트 명령을 실행할 수 있습니다.

### <a name="ping-the-service"></a>서비스 ping

#### <a name="usage"></a>사용 현황

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>예

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>등록된 데이터베이스 표시

#### <a name="usage"></a>사용 현황

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>예

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>에이전트 키 제출

#### <a name="usage"></a>사용 현황

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>예

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>데이터베이스 등록

#### <a name="usage"></a>사용 현황

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>예

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>데이터베이스 등록 취소

이 명령을 사용하여 데이터베이스를 등록 취소하면 데이터베이스가 완전히 프로비전을 해제합니다. 데이터베이스가 다른 동기화 그룹에 속해 있는 경우 이 작업은 다른 동기화 그룹을 중단합니다.

#### <a name="usage"></a>사용 현황

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>예

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>자격 증명 업데이트

#### <a name="usage"></a>사용 현황

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>예

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>다음 단계

SQL 데이터 동기화에 대한 자세한 내용은 다음 문서를 참조하세요.

-   개요 - [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-database-sync-data.md)
-   데이터 동기화 설정
    - 포털에서 - [자습서: Azure SQL Database와 SQL Server 온-프레미스 간에 데이터를 동기화하도록 SQL 데이터 동기화 설정](sql-database-get-started-sql-data-sync.md)
    - PowerShell 사용
        -  [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)
-   모범 사례 - [Azure SQL 데이터 동기화에 대한 모범 사례](sql-database-best-practices-data-sync.md)
-   모니터 - [Log Analytics를 사용하여 SQL 데이터 동기화 모니터링](sql-database-sync-monitor-oms.md)
-   문제 해결 - [Azure SQL 데이터 동기화 문제 해결](sql-database-troubleshoot-data-sync.md)
-   동기화 스키마 업데이트
    -   Transact-SQL 사용 - [Azure SQL 데이터 동기화에서 스키마 변경 내용 복제 자동화](sql-database-update-sync-schema.md)
    -   PowerShell 사용 - [PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트](scripts/sql-database-sync-update-schema.md)
