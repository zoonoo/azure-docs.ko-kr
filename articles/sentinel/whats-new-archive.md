---
title: Azure Sentinel의 새로운 기능 보관
description: 6개월 전 또는 그 이전의 Azure Sentinel의 새로운 기능 및 변경된 기능에 대한 설명입니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 1e629b52b7904d2f671c9d472916ba0a2185a986
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104749"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Azure Sentinel의 새로운 기능 보관

기본 [Azure Sentinel의 새로운 기능](whats-new.md) 릴리스 정보 페이지에는 지난 6개월 동안의 업데이트가 포함되어 있지만 이 페이지에는 더 오래된 항목도 포함되어 있습니다.

이전에 제공된 기능에 대한 자세한 내용은 [기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)를 참조하세요.

언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.


> [!TIP]
> Microsoft 위협 헌팅 팀에서는 [Azure Sentinel 커뮤니티](https://github.com/Azure/Azure-Sentinel)에 쿼리, 플레이북, 통합 문서, Notebook을 제공하고 있으며, 여기에는 여러분의 팀이 조정하여 사용할 수 있는 특정한 [헌팅 쿼리](https://github.com/Azure/Azure-Sentinel)가 포함됩니다.
>
> 여러분도 참여할 수 있습니다! [Azure Sentinel 위협 헌터 GitHub 커뮤니티](https://github.com/Azure/Azure-Sentinel/wiki)에 참여하세요.
>

## <a name="november-2020"></a>2020년 11월

- [Azure Sentinel에서 플레이북의 상태 모니터링](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender 커넥터(공개 미리 보기)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Azure Sentinel에서 플레이북의 상태 모니터링

Azure Sentinel 플레이북은 [Azure Log Apps](../logic-apps/index.yml)에 구축된 워크플로를 기반으로 하며, 작업, 비즈니스 프로세스 및 워크플로를 예약, 자동화 및 오케스트레이션하는 데 유용한 클라우드 서비스입니다. 플레이북은 인시던트가 생성될 때 또는 인시던트를 심사하고 작업할 때 자동으로 호출될 수 있습니다. 

플레이북의 상태, 성능 및 사용에 대한 인사이트를 제공하기 위해 **플레이북 상태 모니터링** 이라는 [통합 문서](../azure-monitor/visualize/workbooks-overview.md)가 추가되었습니다. 

**플레이북 상태 모니터링** 통합 문서를 사용하여 플레이북 상태를 모니터링하거나 성공한 실행 또는 실패한 실행의 수량에서 변칙을 찾을 수 있습니다. 

이제 Azure Sentinel 템플릿 갤러리에서 **플레이북 상태 모니터링** 통합 문서를 사용할 수 있습니다.

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="샘플 플레이북 상태 모니터링 통합 문서":::

자세한 내용은 다음을 참조하세요.

- [Logic Apps 설명서](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor 설명서](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender 커넥터(공개 미리 보기)
 
Azure Sentinel용 Microsoft 365 Defender 커넥터를 사용하면 Microsoft 365 Defender에서 Azure Sentinel로 원시 이벤트 데이터 형식인 고급 헌팅 로그를 스트리밍할 수 있습니다. 

[MDATP(엔드포인트용 Microsoft Defender)](/windows/security/threat-protection/)가 [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) 보안 우산에 통합됨에 따라 이제 Microsoft 365 Defender 커넥터를 사용하여 엔드포인트용 Microsoft Defender 고급 헌팅 이벤트를 수집하고 Azure Sentinel 작업 영역에서 새로운 용도로 빌드된 테이블로 직접 스트리밍할 수 있습니다. 

Azure Sentinel 테이블은 Microsoft 365 Defender 포털에서 사용되는 것과 동일한 스키마를 기반으로 하며 고급 헌팅 로그의 집합 전체에 대한 완전한 액세스를 제공합니다. 

자세한 내용은 [Microsoft 365 Defender의 데이터를 Azure Sentinel에 연결](connect-microsoft-365-defender.md)을 참조하세요.

> [!NOTE]
> Microsoft 365 Defender의 이전 이름은 MTP(Microsoft Threat Protection)였습니다. 엔드포인트용 Microsoft Defender의 이전 이름은 MDATP(Microsoft Defender Advanced Threat Protection)였습니다.
> 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Sentinel 온보딩](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](quickstart-get-visibility.md)
