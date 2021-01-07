---
title: 리소스 상태 이해
titleSuffix: Azure Digital Twins
description: Azure Resource Health를 사용 하 여 Azure 디지털 Twins 인스턴스의 상태를 확인 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616552"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure Digital Twins 문제 해결: 리소스 상태

[Azure Resource Health](../service-health/resource-health-overview.md) 를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단 하 고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 보고 합니다.

이 문서에서는 Azure Digital Twins 인스턴스에 대 한 **리소스 상태** 정보를 가져오는 방법을 보여 줍니다.

## <a name="use-azure-resource-health"></a>Azure Resource Health 사용

Azure Resource Health는 Azure 디지털 Twins 인스턴스가 실행 중인지 여부를 모니터링 하는 데 도움이 될 수 있습니다. 이를 사용 하 여 지역 가동 중단이 인스턴스 상태에 영향을 주는지 여부를 확인할 수도 있습니다.

인스턴스의 상태를 확인 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Digital twins 인스턴스로 이동 합니다. 포털 검색 표시줄에 이름을 입력 하 여 찾을 수 있습니다. 

2. 인스턴스의 메뉴에서 *지원 + 문제 해결* 아래에 있는 _**리소스 상태**_ 를 선택 합니다. 그러면 리소스 상태 기록을 볼 수 있는 페이지로 이동 합니다. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="' 리소스 상태 ' 페이지를 보여 주는 스크린샷 지난 9 일 동안의 일별 보고서를 표시 하는 ' 상태 기록 ' 섹션이 있습니다. 매일 ' 사용 가능 ' 상태를 표시 합니다.":::

위의 이미지에서이 인스턴스는 *사용 가능한* 것으로 표시 되며 지난 9 일간의 시간입니다. 표시 *될 수 있는 상태 및* 기타 상태 유형에 대 한 자세한 내용은 [*Azure 리소스 상태 개요*](../service-health/resource-health-overview.md)를 참조 하세요.

[*리소스 유형 및 azure resource health의 상태 검사*](../service-health/resource-health-checks-resource-types.md)에서 다양 한 유형의 azure 리소스에 대 한 리소스 상태로 전환 되는 다양 한 검사에 대해 자세히 알아볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 Azure Digital Twins 인스턴스를 모니터링 하는 다른 방법에 대해 알아보세요.
* [*문제 해결: Azure Monitor을 사용 하 여 메트릭 보기*](troubleshoot-metrics.md)
* [*문제 해결: 진단을 설정*](troubleshoot-diagnostics.md)합니다.
* [*문제 해결: 경고 설정*](troubleshoot-alerts.md)
