---
title: 예외 볼륨의 비정상적인 증가 - Azure Application Insights
description: Azure Application Insights에서 스마트 검색을 통해 애플리케이션 예외를 모니터링하여 예외 볼륨의 비정상적인 패턴을 검색합니다.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4220415a0ab907c2cdf9268a59a4e85400cad86c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536690"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>예외 볼륨의 비정상적인 증가(미리 보기)

>[!NOTE]
>Application Insight 리소스를 경고 기반 스마트 검색(미리 보기)으로 마이그레이션할 수 있습니다. 마이그레이션은 다양한 스마트 검색 모듈에 대한 경고 규칙을 만듭니다. 일단 만들어지면 다른 Azure Monitor 경고 규칙과 마찬가지로 이러한 규칙을 관리하고 구성할 수 있습니다. 또한 이러한 규칙에 대한 작업 그룹을 구성할 수 있으므로 여러 가지 방법으로 작업을 수행하거나 새 검색에 대한 알림을 트리거할 수 있습니다.
>
> 자세한 내용은 [스마트 검색 경고 마이그레이션](../alerts/alerts-smart-detections-migration.md)을 참조하세요.

스마트 검색은 애플리케이션에서 throw된 예외를 자동으로 분석하고 예외 원격 분석에서 비정상적인 패턴을 경고할 수 있습니다.

이 기능을 사용하려면 앱에 대한 [예외 보고를 구성](./asp-net-exceptions.md#set-up-exception-reporting)하는 것 외에 특별한 설정이 필요하지 않습니다. 앱에서 충분한 예외 원격 분석을 생성하면 이 기능은 활성화됩니다.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>이 형식의 스마트 검색 알림은 언제 받게 되나요?
앱이 하루 동안 특정 유형의 예외 수가 비정상적으로 증가하는 것으로 표시되는 경우 이러한 유형의 알림을 받습니다. 이 숫자는 이전 7일 동안 계산된 기준과 비교됩니다.
Machine Learning 알고리즘을 사용하여 예외 수의 증가를 검색하고, 애플리케이션 사용량의 자연스러운 증가도 고려합니다.

## <a name="does-my-app-definitely-have-a-problem"></a>내 앱에 분명히 문제가 있나요?
아니요, 알림이 제공된다고 해서 앱에 반드시 문제가 있는 것은 아닙니다. 예외 수가 너무 많으면 대개 애플리케이션 문제를 나타내지만 이러한 예외는 심각하지 않으며 애플리케이션에서 올바로 처리될 수 있습니다.

## <a name="how-do-i-fix-it"></a>이 문제를 어떻게 해결하나요?
알림에는 진단 프로세스에서 도움이 되는 진단 정보가 포함되어 있습니다.
1. **심사.** 이 알림은 영향을 받은 사용자 수 또는 요청의 수를 보여 줍니다. 이 정보는 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 이 문제가 모든 트래픽에 영향을 주나요? 아니면 일부 작업에만 영향을 주나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 이 검색은 예외가 throw된 메서드뿐 아니라 예외 형식에 대한 정보를 포함합니다. 지원 정보에 연결된 관련 항목 및 보고서를 사용하면 문제를 추가로 진단할 수 있습니다.
