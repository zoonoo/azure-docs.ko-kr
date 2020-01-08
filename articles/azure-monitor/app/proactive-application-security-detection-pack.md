---
title: Azure 애플리케이션 Insights를 사용 하는 보안 검색 팩
description: 잠재적인 보안 문제에 대 한 Azure 애플리케이션 정보 및 스마트 검색을 사용 하 여 응용 프로그램을 모니터링 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: ba35b3a3e1985f49ade468c4d2e82ca579fdac2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432467"
---
# <a name="application-security-detection-pack-preview"></a>애플리케이션 보안 검색 팩(미리 보기)

Application Insights는 애플리케이션에서 생성한 원격 분석을 자동으로 분석하고 잠재적인 보안 문제를 감지합니다. 이 기능을 사용하면 잠재적인 보안 문제를 식별하고 애플리케이션을 수정하거나 필요한 보안 조치를 수행하여 문제를 처리할 수 있습니다.

이 기능을 사용하려면 [원격 분석을 보내도록 앱을 구성](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview)하는 것 외에 특별한 설정이 필요하지 않습니다.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>이 형식의 스마트 검색 알림은 언제 받게 되나요?
다음과 같은 세 가지 유형의 보안 문제가 검색됩니다.
1. 안전하지 않은 URL 액세스: 애플리케이션의 URL이 HTTP와 HTTPS 모두를 통해 액세스됩니다. HTTPS 요청을 허용하는 URL은 HTTP 요청을 허용하지 않는 것이 통례이므로, 이는 애플리케이션의 버그나 보안 문제를 나타낼 수 있습니다.
2. 안전하지 않은 양식: 애플리케이션의 양식(또는 기타 “POST” 요청)을 HTTPS 대신 HTTP를 사용합니다. HTTP를 사용하면 양식으로 보낸 사용자 데이터가 손상될 수 있습니다.
3. 의심 스러운 사용자 활동: 한 번에 동일한 사용자가 여러 국가/지역에서 응용 프로그램에 액세스 하 고 있습니다. 예를 들어 같은 사용자가 같은 시간 내에 스페인과 미국에서 애플리케이션에 액세스했습니다. 이 검색은 악의적일 가능성이 있는 애플리케이션 액세스 시도를 나타냅니다.

## <a name="does-my-app-definitely-have-a-security-issue"></a>내 앱에 확실히 보안 문제가 있나요?
아니요, 알림이 제공된다고 해서 앱에 반드시 보안 문제가 있는 것은 아닙니다. 위 시나리오의 검색은 많은 경우 보안 문제를 나타낼 수 있지만, 합당한 비즈니스 근거가 있을 수 있으며 무시해도 됩니다.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>“안전하지 않은 URL 액세스” 검색을 해결하려면 어떻게 하나요?
1. **심사.** 이 알림은 안전하지 않은 URL에 액세스한 사용자 수와 안전하지 않은 액세스의 영향을 가장 많이 받은 URL을 제공합니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 안전하지 않은 URL에 액세스한 사용자의 비율은 얼마나 되나요? 영향을 받은 URL의 수는 얼마나 되나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 이 검색은 안전하지 않은 요청의 목록과 영향을 받은 URL 및 사용자의 목록을 제공하여 문제를 자세히 진단하는 데 도움을 줍니다.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>“안전하지 않은 양식” 검색을 해결하려면 어떻게 하나요?
1. **심사.** 이 알림은 안전하지 않은 양식의 수와 데이터가 손상되었을 수 있는 사용자 수를 제공합니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 안전하지 않은 전송 수가 가장 많은 양식은 무엇이고 안전하지 않은 전송의 시간별 분포는 어떻게 되나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 이 감지는 안전하지 않은 양식의 목록과 각 양식의 안전하지 않은 전송 수 분석을 제공하여 문제를 자세히 진단하는 데 도움을 줍니다.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>“의심스러운 사용자 활동” 검색을 해결하려면 어떻게 하나요?
1. **심사.** 이 알림은 의심스러운 동작을 수행한 서로 다른 사용자의 수를 제공합니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 의심 스러운 요청이 발생 하는 국가/지역은 어디 인가요? 가장 의심스러운 사용자는 누구였나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 검색은 의심 스러운 사용자 목록과 각 사용자에 대 한 국가/지역 목록을 제공 하 여 문제를 추가로 진단 하는 데 도움을 줍니다.
