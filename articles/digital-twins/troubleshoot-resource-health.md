---
title: 리소스 상태 이해
titleSuffix: Azure Digital Twins
description: Azure Resource Health를 사용하여 Azure Digital Twins 인스턴스의 상태를 확인하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 20afd0a696a2bbeda0e2fa8bb114ecb8b5b6a76b
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205542"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure Digital Twins 문제 해결: 리소스 상태

[Azure Resource Health](../service-health/resource-health-overview.md)를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단하고 지원을 받을 수 있습니다. 리소스의 현재 및 과거 상태를 보고합니다.

이 문서에서는 Azure Digital Twins 인스턴스에 대한 **리소스 상태** 정보를 가져오는 방법을 보여 줍니다.

## <a name="use-azure-resource-health"></a>Azure Resource Health 사용

Azure Resource Health를 사용하여 Azure Digital Twins 인스턴스가 실행 중인지 여부를 모니터링할 수 있습니다. 또한 지역 가동 중단이 인스턴스의 상태에 영향을 주는지도 확인할 수 있습니다.

인스턴스의 상태를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Digital Twins 인스턴스로 이동합니다. 포털 검색 표시줄에 이름을 입력하여 찾을 수 있습니다. 

2. 인스턴스의 메뉴에서 *지원 + 문제 해결* 아래에 있는 _**리소스 상태**_ 를 선택합니다. 그러면 리소스 상태 기록을 볼 수 있는 페이지로 연결됩니다. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="'리소스 상태' 페이지를 보여 주는 스크린샷. 지난 9일 동안의 일별 보고서를 표시하는 '상태 기록' 섹션이 있습니다. 매일 '사용 가능' 상태를 표시합니다.":::

위의 이미지에서 이 인스턴스는 *사용 가능* 으로 표시되며 지난 9일 동안의 상태였습니다. *사용 가능* 상태 및 표시될 수 있는 기타 상태 유형에 대한 자세한 내용은 [Azure Resource Health 개요](../service-health/resource-health-overview.md)를 참조하세요.

또한 [Azure Resource Health에서 리소스 유형 및 상태 검사](../service-health/resource-health-checks-resource-types.md)에서 다양한 유형의 Azure 리소스에 대한 리소스 상태를 확인하는 다양한 검사에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 Azure Digital Twins 인스턴스를 모니터링하는 다른 방법에 대해 알아보세요.
* [문제 해결: Azure Monitor의 메트릭 보기](troubleshoot-metrics.md)
* [문제 해결: 진단 설정](troubleshoot-diagnostics.md)
* [문제 해결: 경고 설정](troubleshoot-alerts.md)
