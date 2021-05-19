---
title: Azure Resource Mover를 사용하여 지역 간 Azure SQL 리소스 이동 지원
description: Azure Resource Mover를 사용하여 지역 간 Azure SQL 리소스 이동 지원 검토
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452085"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure 지역 간 Azure SQL 리소스 이동 지원

이 문서에는 Azure Resource Mover를 사용하여 Azure 지역 간에 Azure SQL 리소스를 이동하기 위한 지원 및 필수 구성 요소가 요약되어 있습니다.

## <a name="requirements"></a>요구 사항

다음 표에 요구 사항이 요약되어 있습니다.

**기능** | **지원됨/지원되지 않음** | **세부 정보**
--- | --- | ---
**Azure SQL Database Hyperscale** | 지원되지 않음 | Resource Mover를 사용하여 Azure SQL 하이퍼스케일 서비스 계층에서 데이터베이스를 이동할 수 없습니다.
**영역 중복** | 지원됨 |  지원되는 이동 옵션:<br/><br/> - 영역 중복을 지원하는 영역 간의 이동.<br/><br/> - 영역 중복성 지원하지 않는 영역 간의 이동.<br/><br/> - 영역 중복을 지원하지 않는 영역으로 영역 중복을 지원하는 영역 간의 이동.<br/><br/> - 영역 중복을 지원하지 않는 영역으로 영역 중복성을 지원하지 않는 영역 간의 이동. 
**데이터 동기화** | 허브/동기화 데이터베이스: 지원되지 않음<br/><br/> 동기화 멤버: 지원됨 | 동기화 멤버를 이동하는 경우 새 대상 데이터베이스에 대한 데이터 동기화를 설정해야 합니다.
**기존 지역에서 복제** | 지원됨 | 기존 지역 복제본은 대상 지역의 새 주요 복제본으로 다시 매핑됩니다.<br/><br/> 이동 후에는 시드가 초기화되어야 합니다. [자세히 알아보기](../azure-sql/database/active-geo-replication-configure-portal.md)
**BYOK(Bring Your Own Key)를 통한 TDE(투명한 데이터 암호화)** | 지원됨 | 여러 지역에서 키 자격 증명 모음을 이동하는 방법에 대한 [자세한 정보를 알아보세요](../key-vault/general/move-region.md).
**서비스 관리형 키를 사용하는 TDE** | 지원됨. |  여러 지역에서 키 자격 증명 모음을 이동하는 방법에 대한 [자세한 정보를 알아보세요](../key-vault/general/move-region.md).
**동적 데이터 마스킹 규칙** | 지원됨. | 규칙은 이동의 일부로 대상 지역에 자동으로 복사됩니다. [자세히 알아봅니다](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Advanced Data Security** | 지원되지 않습니다. | 다른 해결 방법: 대상 지역에서 SQL Server 수준으로 설정합니다. [자세히 알아봅니다](../azure-sql/database/azure-defender-for-sql.md).
**방화벽 규칙** | 지원되지 않습니다. | 다른 해결 방법: 대상 지역의 SQL Server에 대한 방화벽 규칙을 설정합니다. 데이터베이스 수준 방화벽 규칙은 원본 서버에서 대상 서버로 복사됩니다. [자세히 알아봅니다](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**감사 정책** | 지원되지 않습니다. | 이동 후 정책이 기본값으로 다시 설정됩니다. 다시 설정하는 방법을 [알아봅니다](../azure-sql/database/auditing-overview.md).
**백업 보존** | 지원됨. | 원본 데이터베이스에 대한 백업 보존 정책은 대상 데이터베이스로 전달됩니다. 이동 후 설정을 수정하는 방법에 [대해 알아봅니다](../azure-sql/database/long-term-backup-retention-configure.md).
**자동 튜닝** | 지원되지 않습니다. | 다른 해결 방법: 이동 후 자동 튜닝 설정을 설정합니다. [자세히 알아봅니다](../azure-sql/database/automatic-tuning-enable.md).
**데이터베이스 경고** | 지원되지 않습니다. | 다른 해결 방법: 이동 후에 경고를 설정합니다. [자세히 알아봅니다](../azure-sql/database/alerts-insights-configure-portal.md).
**SQL Server Stretch Database** | 지원되지 않음 | Resource Mover를 사용하여 SQL Server 스트레치 데이터베이스를 이동할 수 없습니다.
**Azure Synapse Analytics** | 지원되지 않음 | Resource Mover를 사용하여 Azure Synapse Analytics를 이동할 수 없습니다.
## <a name="next-steps"></a>다음 단계

Resource Mover로 다른 영역에 [Azure SQL 리소스](tutorial-move-region-sql.md)를 시도합니다.