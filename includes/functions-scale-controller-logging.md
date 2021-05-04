---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081041"
---
| 속성 | Description |
|--|--|
|**`<DESTINATION>`**| 로그가 전송되는 대상입니다. 유효한 값은 `AppInsights` 및 `Blob`입니다.<br/>`AppInsights`를 사용하는 경우 [함수 앱에서 Application Insights를 사용할 수 있는지 확인](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration)합니다.<br/>대상을 `Blob`로 설정하면 `AzureWebJobsStorage` 애플리케이션 설정에 설정된 기본 스토리지 계정에서 `azure-functions-scale-controller`라는 Blob 컨테이너에 로그가 생성됩니다. |
|**`<VERBOSITY>`** | 로깅 수준을 지정합니다. 지원되는 값은 `None`, `Warning` 및 `Verbose`입니다.<br/>`Verbose`로 설정되면 크기 조정 컨트롤러는 모든 작업자 수 변경의 이유뿐 아니라 해당 결정을 촉발한 트리거에 대한 정보를 기록합니다. 자세한 로그에는 크기 조정 컨트롤러를 실행하기 전후에 트리거에서 사용한 트리거 경고 및 해시가 포함됩니다. |

> [!TIP]
> 크기 조정 컨트롤러 로깅을 사용하면 [함수 앱 모니터링 비용](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits)에 영향을 줍니다. 크기 조정 컨트롤러의 동작을 이해하는 데 충분한 데이터를 수집할 때까지는 로깅을 사용하는 것이 좋습니다.
