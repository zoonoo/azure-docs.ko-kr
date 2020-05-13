---
title: 파일 포함
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121565"
---
Functions를 사용하면 [Azure Portal]에서 함수 앱에 Application Insights 통합을 쉽게 추가할 수 있습니다.

1. [Azure Portal][Azure portal]에서 **함수 앱**을 검색 하 고 선택한 다음 함수 앱을 선택 합니다. 

1. 창 위쪽에서 **구성 되지 않음 Application Insights** 배너를 선택 합니다. 이 배너가 표시 되지 않으면 앱이 이미 Application Insights 사용 하도록 설정 되어 있는 것입니다.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="포털에서 Application Insights를 사용하도록 설정":::

1. 이미지 아래 표에 지정된 설정을 사용하여 Application Insights 리소스를 만듭니다.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Application Insights 리소스 만들기":::

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 고유한 앱 이름 | 함수 앱과 동일한 이름을 사용하는 것이 가장 간단하며, 구독 내에서 고유해야 합니다. | 
    | **위치** | 서유럽 | 가능하다면 함수 앱과 동일한 [지역](https://azure.microsoft.com/regions/)을 사용하거나 해당 지역에 근접한 지역을 사용합니다. |

1. **적용**을 선택합니다. 함수 앱과 동일한 리소스 그룹 및 구독에 Application Insights 리소스가 만들어집니다. 리소스를 만든 후 Application Insights 창을 닫습니다.

1. 함수 앱으로 돌아가서 **설정**  >  **구성**을 선택한 다음 **응용 프로그램 설정**을 선택 합니다. `APPINSIGHTS_INSTRUMENTATIONKEY`라는 설정이 표시되면 Azure에서 실행 중인 함수 앱에 대해 Application Insights 통합이 활성화됩니다.

[Azure 포털]: https://portal.azure.com
