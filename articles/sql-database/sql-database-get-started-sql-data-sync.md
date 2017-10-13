---
title: "Azure SQL 데이터 동기화 시작(미리 보기) | Microsoft Docs"
description: "이 자습서는 Azure SQL 데이터 동기화(미리 보기)를 시작하도록 도와줍니다."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.openlocfilehash: 1943e87ffd991d099ab655af5a7d16d7f1608d22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Azure SQL 데이터 동기화 시작(미리 보기)
이 자습서에서는 Azure SQL Database와 SQL Server 인스턴스를 모두 포함하는 하이브리드 동기화 그룹을 만들어 Azure SQL 데이터 동기화를 설정하는 방법에 대해 설명합니다. 새 동기화 그룹을 완벽하게 구성하고 설정한 일정에 동기화합니다.

이 자습서에서는 사용자가 SQL Database 및 SQL Server를 사용해본 경험이 있다고 가정합니다. 

SQL 데이터 동기화의 개요는 [데이터 동기화](sql-database-sync-data.md)를 참조하세요.

SQL Data Sync 구성 방법을 보여주는 전체 PowerShell 예제는 다음 문서를 참조하세요.
-   [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
-   [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!NOTE]
> 이전에 MSDN에 있었던 Azure SQL 데이터 동기화에 설정된 전체 기술 설명서는 .PDF로 제공됩니다. [여기](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)에서 다운로드하세요.

## <a name="step-1---create-sync-group"></a>1단계 - 동기화 그룹 만들기

### <a name="locate-the-data-sync-settings"></a>데이터 동기화 설정 찾기

1.  브라우저에서 Azure Portal로 이동합니다.

2.  포털의 대시보드 또는 툴바의 SQL Database 아이콘에서 SQL Database를 찾습니다.

    ![Azure SQL Database 목록](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  **SQL Database** 블레이드에서 데이터 동기화의 허브 데이터베이스로 사용하려는 기존 SQL Database를 선택합니다. SQL Database 블레이드를 엽니다.

4.  선택한 데이터베이스의 SQL Database 블레이드에서 **다른 데이터베이스에 동기화**를 선택합니다. 데이터 동기화 블레이드를 엽니다.

    ![다른 데이터베이스 옵션에 동기화](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>새 동기화 그룹 만들기

1.  데이터 동기화 블레이드에서 **새 동기화 그룹**을 선택합니다. 1단계인 **동기화 그룹 만들기**가 강조 표시된 상태로 **새 동기화 그룹** 블레이드가 열립니다. **데이터 동기화 그룹 만들기** 블레이드도 엽니다.

2.  **데이터 동기화 그룹 만들기** 블레이드에서 다음을 수행합니다.

    1.  **동기화 그룹 이름은** 필드에서 새 동기화 그룹의 이름을 입력합니다.

    2.  **동기화 메타데이터 데이터베이스** 섹션에서 새 데이터베이스를 만들지(권장) 아니면 기존 데이터베이스를 사용할지를 선택합니다.

        > [!NOTE]
        > Microsoft에서는 동기화 메타데이터 데이터베이스로 사용할 비어 있는 새 데이터베이스를 만들도록 권장합니다. 데이터 동기화는 이 데이터베이스에서 테이블을 만들고 자주 실행되는 워크로드를 실행합니다. 이 데이터베이스는 선택한 지역에서 모든 동기화 그룹의 동기화 메타데이터 데이터베이스로 자동으로 공유됩니다. 동기화 메타데이터 데이터베이스 또는 해당 이름을 삭제하지 않고 변경할 수 없습니다.

        **새 데이터베이스**를 선택한 경우 **새 데이터베이스 만들기**를 선택합니다. **SQL Database** 블레이드를 엽니다. **SQL Database** 블레이드에서 새 데이터베이스의 이름을 지정하고 구성합니다. 그런 다음 **확인**을 선택합니다.

        **기존 데이터베이스 사용**을 선택한 경우 목록에서 데이터베이스를 선택합니다.

    3.  **자동 동기화** 섹션에서 먼저 **켜기** 또는 **끄기**를 선택합니다.

        **켜기**를 선택한 경우 **동기화 빈도** 섹션에서 숫자를 입력하고 초, 분, 시간 또는 일을 선택합니다.

        ![동기화 빈도 지정](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  **충돌 해결** 섹션에서 "허브 우선" 또는 "구성원 우선"을 선택합니다.

        ![충돌 해결 방법 지정](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  **확인**을 선택하고 새 동기화 그룹을 만들고 배포할 때까지 기다립니다.

## <a name="step-2---add-sync-members"></a>2단계 - 동기화 구성원 추가

새 동기화 그룹을 만들고 배포한 후에 2단계인 **동기화 구성원 추가**는 **새 동기화 그룹** 블레이드에서 강조 표시됩니다.

**허브 데이터베이스** 섹션에서 허브 데이터베이스가 있는 SQL Database 서버에 기존 자격 증명을 입력합니다. 이 섹션에서 *새* 자격 증명을 입력하지 않습니다.

![동기화 그룹에 추가된 허브 데이터베이스](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

## <a name="add-an-azure-sql-database"></a>Azure SQL Database 추가

**구성원 데이터베이스** 섹션에서 필요에 따라 **Azure 데이터베이스 추가**를 선택하여 Azure SQL Database를 동기화 그룹에 추가합니다. **Azure 데이터베이스 구성** 블레이드를 엽니다.

**Azure 데이터베이스 구성** 블레이드에서 다음을 수행합니다.

1.  **동기화 구성원 이름** 필드에서 새 동기화 구성원의 이름을 제공합니다. 이 이름은 데이터베이스 자체의 이름과 구분됩니다.

2.  요금 청구를 위해 **구독** 필드에서 연결된 Azure 구독을 선택합니다.

3.  **Azure SQL Server** 필드에서 기존 SQL Database 서버를 선택합니다.

4.  **Azure SQL Database** 필드에서 기존 SQL Database를 선택합니다.

5.  **동기화 방향** 필드에서 양방향 동기화, 허브 수신 또는 허브 발신을 선택합니다.

    ![새 SQL Database 동기화 구성원 추가](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  **사용자 이름** 및 **암호** 필드에서 구성원 데이터베이스가 있는 SQL Database 서버에 기존 자격 증명을 입력합니다. 이 섹션에서 *새* 자격 증명을 입력하지 않습니다.

7.  **확인**을 선택하고 새 동기화 구성원을 만들고 배포할 때까지 기다립니다.

    ![새 SQL Database 동기화 구성원 추가](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

## <a name="add-an-on-premises-sql-server-database"></a>온-프레미스 SQL Server 데이터베이스 추가

**구성원 데이터베이스** 섹션에서 필요에 따라 **온-프레미스 데이터베이스 추가**를 선택하여 온-프레미스 SQL Server를 동기화 그룹에 추가합니다. **온-프레미스 구성** 블레이드를 엽니다.

**온-프레미스 구성** 블레이드에서 다음을 수행합니다.

1.  **동기화 에이전트 게이트웨이 선택**을 선택합니다. **동기화 에이전트 선택** 블레이드를 엽니다.

    ![동기화 에이전트 게이트웨이 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  **동기화 에이전트 게이트웨이 선택** 블레이드에서 기존 에이전트를 사용하거나 새 에이전트를 만들지 선택합니다.

    **기존 에이전트**를 선택한 경우 목록에서 기존 에이전트를 선택합니다.

    **새 에이전트 만들기**를 선택한 경우 다음을 수행합니다.

    1.  제공된 링크에서 클라이언트 동기화 에이전트 소프트웨어를 다운로드하고 SQL Server가 있는 컴퓨터에 설치합니다.
 
        > [!IMPORTANT]
        > 클라이언트 에이전트가 서버와 통신할 수 있도록 방화벽에서 아웃바운드 TCP 포트 1433을 열어야 합니다.


    2.  에이전트의 이름을 입력합니다.

    3.  **키 만들기 및 생성**을 선택합니다.

    4.  에이전트 키를 클립보드에 복사합니다.
        
        ![새 동기화 에이전트 만들기](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  **확인**을 선택하여 **동기화 에이전트 선택** 블레이드를 닫습니다.

    6.  SQL Server 컴퓨터에서 클라이언트 동기화 에이전트 앱을 찾아 실행합니다.

        ![데이터 동기화 클라이언트 에이전트 앱](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  동기화 에이전트 앱에서 **에이전트 키 전송**을 선택합니다. **동기화 메타데이터 데이터베이스 구성** 대화 상자가 열립니다.

    8.  **동기화 메타데이터 데이터베이스 구성** 대화 상자에 Azure Portal에서 복사된 에이전트 키를 붙여 넣습니다. 또한 메타데이터 데이터베이스가 있는 Azure SQL Database 서버에 기존 자격 증명을 입력합니다. (새 메타데이터 데이터베이스를 만든 경우 이 데이터베이스는 허브 데이터베이스와 동일한 서버에 위치합니다.) **확인**을 선택하고 구성이 완료되기를 기다립니다.

        ![에이전트 키 및 서버 자격 증명을 입력합니다.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   이 시점에서 방화벽 오류가 발생하면 Azure에 대해 SQL Server 컴퓨터에서 들어오는 트래픽을 허용하는 방화벽 규칙을 만들어야 합니다. 포털에서 규칙을 수동으로 만들 수 있지만 SSMS(SQL Server Management Studio)에서 더 쉽게 만들 수 있습니다. SSMS에서 Azure의 허브 데이터베이스에 연결하려고 합니다. 데이터베이스 이름을 \<hub_database_name\>.database.windows.net으로 입력합니다. 대화 상자의 단계를 따라 Azure 방화벽 규칙을 구성합니다. 그런 다음 클라이언트 동기화 에이전트 앱에 반환합니다.

    9.  클라이언트 동기화 에이전트 앱에서 **등록**을 클릭하여 에이전트와 SQL Server 데이터베이스를 등록합니다. **SQL Server 구성** 대화 상자가 열립니다.

        ![SQL Server 데이터베이스를 추가하고 구성합니다.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. **SQL Server 구성** 대화 상자에서 연결에 SQL Server 인증을 사용할지 아니면 Windows 인증을 사용할지를 선택합니다. SQL Server 인증을 선택한 경우 기존 자격 증명을 입력합니다. 동기화할 SQL Server 이름 및 데이터베이스의 이름을 제공합니다. **연결 테스트**를 선택하여 설정을 테스트합니다. 그런 다음 **저장**을 선택합니다. 등록된 데이터베이스가 목록에 나타납니다.

        ![SQL Server 데이터베이스를 지금 등록합니다.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. 이제 클라이언트 동기화 에이전트 앱을 닫을 수 있습니다.

    12. 포털의 **온-프레미스 구성** 블레이드에서 **데이터베이스 선택**을 선택합니다. **데이터베이스 선택** 블레이드를 엽니다.

    13. **데이터베이스 선택** 블레이드의 **동기화 구성원 이름** 필드에서 새 동기화 구성원의 이름을 제공합니다. 이 이름은 데이터베이스 자체의 이름과 구분됩니다. 목록에서 데이터베이스를 선택합니다. **동기화 방향** 필드에서 양방향 동기화, 허브 수신 또는 허브 발신을 선택합니다.

        ![온-프레미스 데이터베이스 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. **확인**을 선택하여 **데이터베이스 선택** 블레이드를 닫습니다. 그런 다음 **확인**을 선택하여 **온-프레미스 구성** 블레이드을 닫고 새 동기화 구성원을 만들고 배포할 때까지 기다립니다. 마지막으로 **확인**을 클릭하여 **동기화 구성원 선택** 블레이드를 닫습니다.

        ![동기화 그룹에 추가된 온-프레미스 데이터베이스](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  SQL 데이터 동기화 및 로컬 에이전트에 연결하려면 역할 `DataSync_Executor`에 사용자 이름을 추가합니다. 데이터 동기화는 SQL Server 인스턴스에서 이 역할을 만듭니다.

## <a name="step-3---configure-sync-group"></a>3단계 - 동기화 그룹 구성

새 동기화 그룹 구성원을 만들고 배포한 후에 3단계인 **동기화 그룹 구성**이 **새 동기화 그룹** 블레이드에서 강조 표시됩니다.

1.  **테이블** 블레이드의 동기화 그룹 구성원 목록에서 데이터베이스를 선택한 다음 선택 **스키마 새로 고침**을 선택합니다.

2.  사용 가능한 테이블 목록에서 동기화하려는 테이블을 선택합니다.

    ![동기화할 테이블 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  기본적으로 테이블의 모든 열이 선택됩니다. 모든 열을 동기화하지 않으려면 동기화하지 않으려는 열에 확인란을 사용하지 않도록 설정합니다. 선택한 기본 키 열을 남겨 두어야 합니다.

    ![동기화할 필드 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  마지막으로 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계
축하합니다. SQL Database 인스턴스와 SQL Server 데이터베이스가 모두 포함된 동기화 그룹을 만들었습니다.

SQL Database 및 SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

-   [전체 SQL 데이터 동기화 기술 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)
-   [SQL 데이터 동기화 REST API 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)
-   [SQL 데이터베이스 개요](sql-database-technical-overview.md)
-   [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)
