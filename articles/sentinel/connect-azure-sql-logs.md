---
title: Azure SQL database 진단 및 감사 로그를 Azure 센티널에 연결
description: Azure SQL database 진단 로그 및 보안 감사 로그를 Azure 센티널에 연결 하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807736"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Azure SQL database 진단 및 감사 로그 연결

Azure SQL은 사용자 개입 없이 업그레이드, 패치, 백업 및 모니터링과 같은 대부분의 데이터베이스 관리 기능을 처리 하는 완전히 관리 되는 PaaS (Platform as a Service) 데이터베이스 엔진입니다. 

Azure SQL database 커넥터를 사용 하 여 데이터베이스의 감사 및 진단 로그를 Azure 센티널로 스트리밍할 수 있으므로 모든 인스턴스에서 작업을 지속적으로 모니터링할 수 있습니다.

- 진단 로그를 연결 하면 여러 데이터 형식의 데이터베이스 진단 로그를 Azure 센티널 작업 영역으로 보낼 수 있습니다.

- 감사 로그를 연결 하면 서버 수준에서 모든 Azure SQL database의 보안 감사 로그를 스트리밍할 수 있습니다.

[AZURE SQL database 모니터링](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 감사 로그를 연결 하려면 Azure SQL Server 감사 설정에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

## <a name="connect-to-azure-sql-database"></a>Azure SQL 데이터베이스에 연결
    
1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure SQL Database** 을 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기**  를 선택 합니다.

1. 커넥터 페이지의 **구성** 섹션에서 연결할 수 있는 두 가지 로그 범주를 확인 합니다.

### <a name="connect-diagnostics-logs"></a>진단 로그 연결

1. **진단 로그** 에서 **각 Azure SQL 데이터베이스의 진단 로그를 수동으로 사용** 을 확장 합니다.

1. Azure **sql >열기** 링크를 선택 하 여 **azure sql** 리소스 블레이드를 엽니다.

1. **(선택 사항)** 데이터베이스 리소스를 쉽게 찾으려면 위쪽의 필터 표시줄에서 **필터 추가** 를 선택 합니다.
    1. **필터** 드롭다운 목록에서 **리소스 종류** 를 선택 합니다.
    1. **값** 드롭다운 목록에서 **모두 선택** 을 선택 취소 한 다음 **SQL database** 를 선택 합니다.
    1. **적용** 을 클릭합니다.
    
1. Azure 센티널로 보내려는 진단 로그의 데이터베이스 리소스를 선택 합니다.

    > [!NOTE]
    > 로그를 수집 하려는 각 데이터베이스 리소스에 대해이 단계에서 시작 하 여이 프로세스를 반복 해야 합니다.

1. 선택한 데이터베이스의 리소스 페이지에서 탐색 메뉴의 **모니터링** 아래에서 **진단 설정** 을 선택 합니다.

    1. 테이블 아래쪽의 **+ 진단 설정 추가** 링크를 선택 합니다.

    1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력 합니다.
    
    1. **대상 정보** 열에서 **Log Analytics 작업 영역으로 보내기** 확인란을 표시 합니다. 그 아래에는 두 개의 새 필드가 표시 됩니다. 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택 합니다 (Azure 센티널이 있는 경우).

    1. **범주 세부 정보** 열에서 수집 하려는 로그 및 메트릭 유형의 확인란을 표시 합니다. **로그** 및 **메트릭** 에서 사용 가능한 모든 유형을 선택 하는 것이 좋습니다.

    1. 화면 위쪽에서 **저장** 을 선택 합니다.

- 또는 제공 된 **PowerShell 스크립트** 를 사용 하 여 진단 로그를 연결할 수 있습니다.
    1. **진단 로그** 에서 **PowerShell 스크립트를 사용 하 여 사용** 을 확장 합니다.

    1. PowerShell에서 코드 블록을 복사 하 고 붙여 넣습니다.

### <a name="connect-audit-logs"></a>감사 로그 연결

1. **감사 로그 (미리 보기)** 에서 **모든 Azure SQL 데이터베이스 (서버 수준)에서 감사 로그 사용** 을 확장 합니다.

1. **AZURE sql >열기** 링크를 선택 하 여 **sql server** 리소스 블레이드를 엽니다.

1. Azure 센티널로 전송 하려는 감사 로그를 포함 하는 SQL server를 선택 합니다.

    > [!NOTE]
    > 로그를 수집 하려는 각 서버 리소스에 대해이 단계에서 시작 하 여이 프로세스를 반복 해야 합니다.

1. 선택한 서버의 리소스 페이지에서 탐색 메뉴의 **보안** 아래에 있는 **감사** 를 선택 합니다.

    1. **AZURE SQL 감사 사용** 을 설정으로 이동 **합니다.**

    1. **감사 로그 대상** 에서 **Log Analytics (미리 보기)** 를 선택 합니다.
    
    1. 표시 되는 작업 영역 목록에서 Azure 센티널이 있는 작업 영역을 선택 합니다.

    1. 화면 위쪽에서 **저장** 을 선택 합니다.

- 또는 제공 된 **PowerShell 스크립트** 를 사용 하 여 진단 로그를 연결할 수 있습니다.
    1. **감사 로그** 에서 **PowerShell 스크립트를 사용 하 여 사용** 을 확장 합니다.

    1. PowerShell에서 코드 블록을 복사 하 고 붙여 넣습니다.


> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용 하 여 지난 2 주 동안 데이터가 수집 된 경우에만 연결 상태 표시기 (데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆의 연결 아이콘)가 *연결* 됨 (녹색)으로 표시 됩니다. 두 주가 데이터 수집 없이 전달 되 면 커넥터는 연결 끊김으로 표시 됩니다. 더 많은 데이터가 제공 되는 순간에 *연결* 된 상태는를 반환 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure SQL database 진단 및 감사 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.