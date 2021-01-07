---
title: 전용 SQL 풀에서 Synapse 작업 영역 기능 사용 (이전의 SQL DW)
description: 이 문서에서는 고객이 작업 영역에서 기존 SQL DW 독립 실행형 인스턴스에 액세스 하 고 해당 인스턴스를 사용 하는 방법을 설명 합니다.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 5efb1df378df323585bc0ca1094451cdb095fe4e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499784"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>기존 전용 SQL 풀에서 Synapse 작업 영역 기능 사용 (이전의 SQL DW)

모든 SQL data warehouse 고객은 이제 자동화, 연결 또는 도구에 영향을 주지 않고 Synapse Studio 및 작업 영역을 통해 기존 전용 SQL 풀 (이전의 SQL DW) 인스턴스에 액세스 하 고 사용할 수 있습니다. 이 문서에서는 기존 Azure Synapse Analytics 고객이 Synapse 작업 영역 및 Studio를 통해 제공 되는 새로운 기능의 새로운 기능을 활용 하 여 기존 분석 솔루션을 구축 하 고 확장 하는 방법을 설명 합니다.   

## <a name="experience"></a>환경
 
이제 Synapse 작업 영역이 GA 되었으므로 DW 포털 개요 섹션에서 기존 전용 SQL 풀 (이전의 SQL DW) 인스턴스에 대 한 Synapse 작업 영역을 만들 수 있는 새로운 기능을 사용할 수 있습니다. 이 새로운 기능을 사용 하면 기존 데이터 웨어하우스 인스턴스를 호스트 하는 논리 서버를 새 Synapse 작업 영역에 연결할 수 있습니다. 연결은 해당 서버에서 호스트 되는 모든 데이터 웨어하우스가 작업 영역 및 Studio에서 액세스할 수 있도록 하 고 Synapse partner services (서버 리스 SQL 풀, Apache Spark 풀 및 ADF)와 함께 사용할 수 있도록 합니다. 프로 비전 단계를 완료 하 고 새로 만든 작업 영역에 대 한 연결이 설정 되는 즉시 리소스 액세스 및 사용을 시작할 수 있습니다.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="연결 된 Synapse 작업 영역":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Synapse 작업 영역 및 Studio 기능을 사용 하 여 전용 SQL 풀 액세스 및 사용 (이전의 SQL DW)
 
다음 정보는 Synapse 작업 영역 기능이 사용 하도록 설정 된 전용 SQL DW (이전의 SQL DW)를 사용할 때 적용 됩니다. 
- **SQL 기능** Synapse 작업 영역 기능이 사용 하도록 설정 된 후에는 모든 SQL 기능이 논리 SQL server와 함께 유지 됩니다. 작업 영역을 사용 하도록 설정한 후에도 SQL 리소스 공급자를 통해 서버에 액세스할 수 있습니다. 모든 관리 기능은 작업 영역을 통해 시작할 수 있으며 작업은 SQL 풀을 호스트 하는 논리적 SQL Server에서 수행 됩니다. 작업 영역을 사용 하도록 설정 하면 기존 자동화, 도구 또는 연결이 끊어지거나 중단 됩니다.  
- **리소스 이동**  Synapse 작업 영역 기능이 사용 하도록 설정 된 서버에서 리소스 이동을 시작 하면 서버와 작업 영역 간의 연결이 중단 되며 작업 영역에서 기존 전용 SQL 풀 (이전의 SQL DW) 인스턴스에 액세스할 수 없게 됩니다. 연결이 유지 되도록 하려면 두 리소스가 동일한 구독 및 리소스 그룹 내에 유지 되는 것이 좋습니다. 
- **모니터링** 작업 영역에서 Synapse Studio를 통해 전송 된 SQL 요청은 전용 SQL 풀 (이전의 SQL DW)에서 볼 수 있습니다. 다른 모든 모니터링 활동의 경우 Azure Portal 전용 SQL 풀 (이전의 SQL DW) 모니터링으로 이동할 수 있습니다. 
- **보안** 및 **액세스 제어** 위에 명시 된 대로 SQL server 및 전용 SQL 풀 (이전의 sql DW) 인스턴스의 모든 관리 기능은 논리적 SQL server에 계속 남아 있습니다. 이러한 기능에는 방화벽 규칙 관리, 서버의 Azure AD 관리자 설정 및 전용 SQL 풀 (이전의 SQL DW)의 데이터에 대 한 모든 액세스 제어가 포함 됩니다. 전용 SQL 풀 (이전의 SQL DW)에 액세스할 수 있고 Synapse 작업 영역을 통해 사용할 수 있도록 다음 단계를 수행 해야 합니다. 작업 영역 역할 멤버 자격은 전용 SQL 풀 (이전의 SQL DW) 인스턴스의 데이터에 대 한 사용 권한을 사용자에 게 부여 하지 않습니다. 일반 [sql 인증](sql-data-warehouse-authentication.md) 정책을 따라 사용자가 논리 서버에서 전용 sql 풀 (이전의 sql DW) 인스턴스에 액세스할 수 있는지 확인 합니다. 

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > 연결 된 작업 영역 Synapse Studio는 사용자가 Azure에서 사용 하는 권한에 따라 전용 풀의 이름을 표시 합니다. 사용자에 게 SQL 풀에 대 한 사용 권한이 없으면 풀 아래의 개체에 액세스할 수 없습니다. 

- **네트워크 보안** 기존 전용 SQL 풀 (이전의 SQL DW)에서 사용 하도록 설정한 Synapse 작업 영역에 데이터 침입 보호를 사용할 수 있습니다. 작업 영역에서 논리 SQL server로 관리 되는 개인 끝점 연결을 만듭니다. 서버와 작업 영역 간의 통신을 허용 하도록 개인 끝점 연결 요청을 승인 합니다.
- **스튜디오** **데이터 허브** 의 SQL 풀 작업 영역 사용 전용 sql 풀 (이전의 sql DW)은 데이터 허브의 도구 설명을 통해 식별할 수 있습니다. 
- **새 전용 sql 풀 만들기 (이전의 SQL DW)** 작업 영역 기능이 사용 하도록 설정 되 고 새 풀의 프로 비전이 논리 SQL server에서 수행 되 면 새 전용 SQL 풀을 Synapse 작업 영역 및 Studio를 통해 만들 수 있습니다. 프로 비전이 완료 되 면 새 리소스가 포털 및 스튜디오에 표시 됩니다.      

## <a name="next-steps"></a>다음 단계
기존 전용 SQL 풀에서 [Synapse 작업 영역 기능](workspace-connected-create.md) 사용 (이전의 SQL DW)
