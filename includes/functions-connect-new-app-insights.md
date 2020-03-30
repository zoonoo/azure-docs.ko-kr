---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132625"
---
Functions를 사용하면 [Azure Portal]에서 함수 앱에 Application Insights 통합을 쉽게 추가할 수 있습니다.

1. 포털 [portal][Azure Portal에서] `Function Apps` 페이지 상단의 검색 창을 입력하고 함수 앱을 선택한 다음 창 상단에 **있는 응용 프로그램 인사이트가 구성되지 않은** 배너를 선택합니다. 이 배너가 표시되지 않으면 앱에 이미 응용 프로그램 인사이트가 활성화되어 있습니다.

    ![포털에서 Application Insights를 사용하도록 설정](media/functions-connect-new-app-insights/enable-application-insights.png)

1. 이미지 아래 표에 지정된 설정을 사용하여 Application Insights 리소스를 만듭니다.

   ![Application Insights 리소스 만들기](media/functions-connect-new-app-insights/ai-general.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 고유한 앱 이름 | 함수 앱과 동일한 이름을 사용하는 것이 가장 간단하며, 구독 내에서 고유해야 합니다. | 
    | **위치** | 서유럽 | 가능하다면 함수 앱과 동일한 [지역](https://azure.microsoft.com/regions/)을 사용하거나 해당 지역에 근접한 지역을 사용합니다. |

1. **확인**을 선택합니다. 함수 앱과 동일한 리소스 그룹 및 구독에 Application Insights 리소스가 만들어집니다. 리소스를 만든 후 Application Insights 창을 닫습니다.

1. 함수 앱으로 돌아가서 **애플리케이션 설정**을 선택한 다음, **애플리케이션 설정**이 나올 때까지 아래로 스크롤합니다. `APPINSIGHTS_INSTRUMENTATIONKEY`라는 설정이 표시되면 Azure에서 실행 중인 함수 앱에 대해 Application Insights 통합이 활성화됩니다.

[Azure 포털]: https://portal.azure.com
