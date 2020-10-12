---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84731031"
---
Azure Functions를 사용하면 [Azure Portal]에서 함수 앱에 Application Insights 통합을 쉽게 추가할 수 있습니다.

1. [Azure Portal][Azure Portal]에서 **함수 앱**을 검색하고 선택한 다음, 함수 앱을 선택합니다. 

1. 창 상단에 있는 **Application Insights가 구성되지 않음** 배너를 선택합니다. 이 배너가 표시되지 않으면 앱이 이미 Application Insights를 사용하도록 설정되어 있을 수 있습니다.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="포털에서 Application Insights를 사용하도록 설정":::

1. **리소스 변경**을 펼치고 다음 표에 지정된 설정을 사용하여 Application Insights 리소스를 만듭니다.  

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **새 리소스 이름** | 고유한 앱 이름 | 함수 앱과 동일한 이름을 사용하는 것이 가장 간단하며, 구독 내에서 고유해야 합니다. | 
    | **위치** | 서유럽 | 가능하다면 함수 앱과 동일한 [지역](https://azure.microsoft.com/regions/)을 사용하거나 해당 지역에 근접한 지역을 사용합니다. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="포털에서 Application Insights를 사용하도록 설정":::

1. **적용**을 선택합니다. 

   함수 앱과 동일한 리소스 그룹 및 구독에 Application Insights 리소스가 만들어집니다. 리소스를 만든 후 Application Insights 창을 닫습니다.

1. 함수 앱의 **설정**에서 **구성**을 선택한 다음, **애플리케이션 설정**을 선택합니다. `APPINSIGHTS_INSTRUMENTATIONKEY`라는 설정이 표시되면 Azure에서 실행 중인 함수 앱에 대해 Application Insights 통합이 활성화됩니다.

[Azure Portal]: https://portal.azure.com
