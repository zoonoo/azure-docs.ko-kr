---
title: Azure를 사용한 Elastic 통합 관리-Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal의 Elastic 관리에 대해 설명합니다. 진단 설정을 구성하고 리소스를 삭제하는 방법입니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: aade011c5ad9db505673ea2d03588498dba08b20
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483270"
---
# <a name="manage-the-elastic-integration-with-azure"></a>Azure를 사용한 Elastic 통합 관리

이 문서에서는 Azure로 Elastic 통합을 관리하는 방법을 설명합니다. 진단 설정을 구성하고 Elastic 리소스를 삭제하는 방법을 보여 줍니다.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>메트릭 및 로그에 대한 규칙 다시 구성

Elastic 리소스를 만들 때 Elastic으로 전송되는 로그를 구성합니다. 이러한 설정을 변경해야 하는 경우, 왼쪽 창에서 **메트릭 및 로그** 를 선택합니다. 로그가 Elastic으로 전송되는 방식에 필요한 변경 사항을 적용합니다.

두 가지 로그 유형에 대한 자세한 내용은 [빠른 시작: Elastic 시작](create.md)을 참조하세요.

:::image type="content" source="media/manage/reconfigure-logs.png" alt-text="로그 설정 변경":::

## <a name="view-monitored-resources"></a>모니터링되는 리소스 보기

Elastic에 로그를 보내는 리소스의 목록을 보려면 왼쪽 창에서 **모니터링되는 리소스** 를 선택합니다.

:::image type="content" source="media/manage/monitored-resources.png" alt-text="모니터링되는 리소스 보기":::

리소스 종류, 리소스 그룹 이름, 위치, 리소스에서 로그를 전송하고 있는지를 기준으로 목록을 필터링할 수 있습니다.

**Elastic으로의 로그** 열은 리소스가 Elastic으로 로그를 보내고 있는지 여부를 나타냅니다. 리소스가 로그를 전송하지 않는 경우 이 필드는 로그가 전송되지 않는 이유를 나타냅니다. 다음과 같은 이유가 있을 수 있습니다.

* 리소스에서 로그 전송을 지원하지 않습니다. 여기에 정의된 모든 리소스 유형 및 로그 범주에 대한 Azure 리소스 로그만 Elastic으로 로그를 보내도록 구성할 수 있습니다
* 5개의 진단 설정 수 제한에 도달했습니다. 각 Azure 리소스에는 최대 5개의 [진단 설정](../../azure-monitor/platform/diagnostic-settings.md)이 있을 수 있습니다.
* 오류로 인해 로그가 Elastic으로 전송되지 않도록 차단됩니다.
* 리소스에 대해 로그가 구성되지 않았습니다. 적절한 리소스 태그가 있는 Azure 리소스만 Elastic으로 보내집니다. 로그 구성에서 태그 규칙을 지정했습니다. 
* 지원되지 않는 지역입니다. Azure 리소스가 현재 Elastic으로 로그를 전송하지 않는 지역에 있습니다. 

## <a name="monitor-virtual-machines-using-elastic-agent"></a>Elastic 에이전트를 사용하여 가상 머신 모니터링

가상 머신에 Elastic 에이전트를 확장으로 설치할 수 있습니다. 구독에서 사용 가능한 가상 머신을 확인하려면 Elastic 리소스의 왼쪽 창에서 **가상 머신** 을 선택합니다.

:::image type="content" source="media/manage/vm-agents.png" alt-text="가상 머신 보기":::

각 가상 머신에 대해 다음 데이터가 표시됩니다.

* 리소스 이름 - 가상 머신 이름입니다.
* 리소스 상태 – 가상 머신가 중지되었는지 아니면 실행 중인지를 나타냅니다. Elastic 에이전트는 실행 중인 가상 머신에만 설치할 수 있습니다. 가상 머신이 중지된 경우 Elastic 에이전트를 설치할 수 없습니다.
* 에이전트 버전 – Elastic 에이전트 버전 번호.
* 에이전트 상태 – Elastic 에이전트가 가상 머신에서 실행되고 있는지 여부.
* 통합 사용 – Elastic 에이전트가 수집하는 주요 메트릭.
* 로그 전송 – Elastic 에이전트가 Elastic에 로그를 보내고 있는지 여부.

Elastic 에이전트를 설치하려면 가상 머신을 선택하고 **확장 설치** 를 선택합니다.

포털에 기본 인증으로 에이전트를 설치할지 확인하는 메시지가 표시됩니다. **확인** 을 선택하여 설치를 시작합니다. 포탈은 에이전트가 설치 및 프로비전될 때까지 상태를 **설치 중** 으로 표시합니다.

Elastic 에이전트가 설치되면 상태가 **설치됨** 으로 변경됩니다.

Elastic 에이전트가 설치되어 있는지 확인하려면 가상 머신을 선택하고 **확장** 으로 이동합니다.

가상 머신에서 Elastic 에이전트를 제거하려면 가상 머신을 선택하고 **확장 제거** 를 선택합니다.

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

리소스에 대한 진단 설정을 구성하려면 해당 리소스를 선택합니다. 왼쪽 창에서 **진단 설정** 을 선택합니다. 

대상 세부 정보 섹션에서 파트너 솔루션에 보내기 옵션을 선택하여 대상으로 Elastic을 선택합니다. 이 옵션은 Elastic 리소스를 만든 후에만 사용할 수 있습니다.

:::image type="content" source="media/manage/diagnostic-settings.png" alt-text="진단 설정 구성":::

## <a name="delete-elastic-resource"></a>Elastic 리소스 삭제

Elastic 리소스가 더 이상 필요하지 않은 경우 Azure Portal에서 리소스를 삭제합니다.

Azure에서 리소스를 삭제하려면 Elastic 리소스를 선택합니다. **개요** 에서 **삭제** 를 선택합니다. Elastic 리소스를 삭제할 것인지 확인합니다.

:::image type="content" source="media/manage/delete-elastic.png" alt-text="Elastic 리소스 삭제":::

Elastic 리소스가 삭제되면 로그가 더이상 Elastic로 전송되지 않습니다. Azure Marketplace를 통해 Elastic에 대한 모든 청구가 중지됩니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대한 도움말은 [Azure를 사용한 Elastic 통합 문제 해결](troubleshoot.md)을 참조하세요.
