---
title: 서비스 제한
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 서비스의 한도를 보여 주는 차트입니다.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728041"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins 서비스 제한

Azure Digital Twins의 서비스 제한입니다.

> [!NOTE]
> 이 서비스의 일부 영역에는 조정 가능한 한도가 있습니다. 아래 표에 조정 가능? 열과 함께 표시되어 있습니다. 제한을 조정할 수 있는 경우 조정 가능? 값은 예입니다.
>
> 비즈니스에서 조정 가능한 제한 또는 기본 제한보다 높은 할당량이 필요한 경우 [지원 티켓을 열어](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 추가 리소스를 요청할 수 있습니다.

## <a name="limits-by-type"></a>유형별 한도

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>제한 작업

제한에 도달하면 서비스는 추가 요청을 제한합니다. 이로 인해 이러한 요청에서 429 오류 응답이 발생합니다.

이를 관리하기 위해 제한 작업에 대한 몇 가지 권장 사항은 다음과 같습니다.
* **재시도 논리 사용.** [Azure Digital Twins SDK](how-to-use-apis-sdks.md)는 실패한 요청에 대한 재시도 논리를 구현하므로 제공된 SDK로 작업하는 경우 이미 기본 제공됩니다. 그렇지 않으면 자체 애플리케이션에서 재시도 논리를 구현하는 것이 좋습니다. 서비스는 다시 시도하기 전에 대기하는 시간을 결정하는 데 사용할 수 있는 오류 응답의 `Retry-After` 헤더를 다시 보냅니다.
* **임계값 및 알림을 사용하여 근접 한도에 대해 경고합니다.** Azure Digital Twins에 대한 서비스 제한 중 일부에는 이러한 영역에서 사용량을 추적하는 데 사용할 수 있는 해당 [메트릭](troubleshoot-metrics.md)이 있습니다. 임계값에 도달했을 때 임계값을 구성하고 메트릭에 대한 경고를 설정하려면 [*문제 해결: 경고 설정*](troubleshoot-alerts.md)의 지침을 참조하세요. 메트릭이 제공되지 않는 기타 제한에 대한 알림을 설정하려면 고유한 애플리케이션 코드에서 이 논리를 구현하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

서비스 개요에서 Azure Digital Twins의 현재 릴리스에 대해 알아봅니다.
* [개요: Azure Digital Twins란?](overview.md)
