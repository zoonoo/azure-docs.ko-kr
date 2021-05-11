---
title: 전용 SQL 풀(이전의 SQL DW)에서 Synapse 작업 영역 기능 사용하기
description: 본 문서에서는 고객이 작업 영역 내 자신들의 기존 SQL DW 독립 실행형 인스턴스에 액세스하여 이를 사용하는 방법을 설명합니다.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694617"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>기존 전용 SQL 풀(이전의 SQL DW)에서 Synapse 작업 영역 기능 사용하기

이제 모든 SQL 데이터 웨어하우스 고객이 Synapse Studio와 작업 영역을 통하여 자동화, 연결, 도구에 영향을 주지 않으면서 기존의 전용 SQL 풀(이전의 SQL DW) 인스턴스에 액세스하여 사용할 수 있습니다. 본 문서는 기존의 Azure Synapse Analytics 고객이 Synapse 작업 영역과 스튜디오를 통하여 다양한 새 기능을 활용함으로써 기존의 Analytics 솔루션을 빌드하고 확장하는 방법을 설명합니다.   

## <a name="experience"></a>환경
 
이제 Synapse 작업 영역이 GA 되었으므로 DW Portal 개요 섹션에서 기존의 전용 SQL 풀(이전의 SQL DW) 인스턴스를 위한 Synapse 작업 영역을 만들 수 있도록 하는 새 기능을 활용할 수 있습니다. 이 새로운 기능을 통하여 기존 데이터 웨어하우스 인스턴스를 호스트하는 논리 서버를 새로운 Synapse 작업 영역에 연결할 수 있습니다. 이렇게 연결하면 해당 서버에 호스트 되어 있는 모든 데이터 웨어하우스를 작업 영역과 스튜디오에서 액세스할 수 있게 되며 Synapse 파트너 서비스(서버리스 SQL 풀, Apache Spark 풀, ADF)와 함께 사용할 수 있게 됩니다. 프로비전 단계가 끝나 새로 생성된 작업 영역과의 연결이 설정되는 즉시 리소스에 액세스하여 사용할 수 있게 됩니다.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="연결된 Synapse 작업 영역":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>전용 SQL 풀(이전의 SQL DW)에 액세스하여 이를 사용할 수 있도록 Synapse 작업 영역 및 스튜디오 기능 사용하기
 
다음 정보는 Synapse 작업 영역 기능을 사용하도록 설정한 상태에서 전용 SQL DW (이전의 SQL DW)를 사용할 때 적용됩니다. 
- **SQL 기능** Synapse 작업 영역 기능을 사용하도록 한 뒤에는 SQL의 모든 기능이 논리 SQL 서버와 함께 유지됩니다. 작업 영역을 사용하도록 설정한 뒤에도 SQL 리소스 공급자를 통하여 서버에 액세스할 수 있습니다. 모든 관리 기능은 작업 영역을 통하여 시작할 수 있으며 작업은 SQL 풀을 호스트하는 논리 SQL 서버에서 수행됩니다. 작업 영역을 사용하도록 설정하였을 때는 기존의 자동화, 도구, 연결 중 아무것도 끊어지거나 중단되지 않습니다.  
- **리소스 이동** Synapse 작업 영역 기능을 사용하도록 설정한 서버에 리소스 이동을 시작하면 서버와 작업 영역 사이의 연결이 끊어지고 해당 작업 영역에서는 기존의 전용 SQL 풀(이전의 SQL DW) 인스턴스에 더 이상 액세스할 수 없습니다. 연결을 유지하여 두려면 양쪽의 리소스를 모두 같은 구독 및 리소스 그룹에 유지하는 것이 좋습니다. 
- **모니터링** 전용 SQL 풀(이전의 SQL DW)을 사용하도록 설정된 작업 영역의 Synapse Studio를 통하여 제출된 SQL 요청은 모니터 허브에서 조회할 수 있습니다. 다른 모든 모니터링 활동의 경우 Azure Portal 전용 SQL 풀(이전의 SQL DW) 모니터링으로 이동할 수 있습니다. 
- **보안** 및 **액세스 제어** 위에서 설명한 대로, SQL 서버와 전용 SQL 풀(이전의 SQL DW) 인스턴스를 관리하기 위한 모든 기능은 논리 SQL 서버에 남아 있게 됩니다. 이러한 기능 가운데는 방화벽 규칙 관리, 해당 서버의 Azure AD 관리자 설정, 전용 SQL 풀(이전의SQL DW)의 데이터에 대한 전체 액세스 제어도 포함됩니다. 전용 SQL 풀(이전의 SQL DW)을 액세스할 수 있는 상태로 유지하면서 Synapse 작업 영역을 통하여 사용할 수 있게 하려면 다음 단계를 반드시 수행하여야 합니다. 작업 영역 역할 멤버 자격은 사용자에게 전용 SQL 풀(이전의 SQL DW) 인스턴스 내의 데이터에 대한 권한을 주지 않습니다. 일반 [SQL 인증](sql-data-warehouse-authentication.md) 정책을 따라 사용자가 논리 서버의 전용 SQL 풀(이전의 SQL DW) 인스턴스에 액세스할 수 있는지 확인합니다. 전용 SQL 풀(이전의 SQL DW) 호스트 서버에 관리형 ID가 이미 할당되어 있는 상태라면, 해당 관리형 ID 이름은 작업 영역 파트너 서비스(예: ADF 파이프라인)를 지원하기 위하여 자동으로 만든 작업 영역 관리형 ID의 이름과 동일합니다.  이름이 같은 두 개의 관리형 ID가 하나의 연결된 시나리오에 있을 수 있습니다. 관리형 ID는 Azure AD 개체 ID로 구별할 수 있으며 개체 ID를 사용하여 SQL 사용자를 생성하는 기능은 곧 제공될 예정입니다.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > 연결된 작업 영역 Synapse Studio는 Azure에서 사용자가 보유한 권한에 따라 전용 풀의 이름을 표시합니다. 사용자에게 SQL 풀에 대한 권한이 없는 경우 SQL 풀에 속한 개체에는 액세스할 수 없습니다. 

- **네트워크 보안** 기존의 전용 SQL 풀(이전의 SQL DW)에서 사용하도록 설정한 Synapse 작업 영역이 데이터 침투 방지를 사용하도록 설정된 경우입니다. 작업 영역과 논리 SQL 서버를 연결하는 관리형 프라이빗 엔드포인트 연결을 만듭니다. 프라이빗 엔드포인트 연결 요청을 허용하여 서버와 작업 영역 간 통신을 승인합니다.
- **데이터 허브** 의 **스튜디오 SQL 풀** 전용 SQL 풀(이전의 SQL DW)을 사용하도록 설정된 작업 영역은 데이터 허브의 도구 설명을 통하여 식별할 수 있습니다. 
- **전용 SQL 풀(이전의 SQL DW) 새로 만들기** 작업 영역 기능을 사용하도록 설정한 뒤 Synapse 작업 영역과 스튜디오를 통하여 전용 SQL 풀을 새로 만들 수 있으며 새로운 풀에 대한 프로비전은 논리 SQL 서버에서 실행됩니다. 프로비전이 완료되면 새로운 리소스가 포털과 스튜디오에 표시됩니다.      

## <a name="next-steps"></a>다음 단계
기존의 전용 SQL 풀(이전의 SQL DW)에서 [Synapse 작업 영역 기능](workspace-connected-create.md) 사용하기
