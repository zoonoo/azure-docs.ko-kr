---
title: 포함 파일
description: 포함 파일
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9013095a974a86d34399ab566bcc3fea440f2dc7
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125968"
---
1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

1. **게시** 대화 상자에서 **대상** 으로 **Azure** 를 선택한 후 **다음** 을 선택합니다. 

1. **특정 대상** 에서 **Azure WebJobs** 를 선택한 후 **다음** 을 선택합니다.

1. **App Service 인스턴스** 위에서 더하기( **+** ) 단추를 선택하여 **새 Azure WebJob을 만듭니다**.

1. **App Service(Windows)** 대화 상자에서 다음 표의 호스팅 설정을 사용합니다.

    | 설정      | 제안 값  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. |
    | **구독** | 구독 선택 | 사용할 Azure 구독입니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  함수 앱을 만들 리소스 그룹의 이름입니다. **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[호스팅 계획](../articles/app-service/overview-hosting-plans.md)** | App Service 계획 | [App Service 계획](../articles/app-service/overview-hosting-plans.md)은 위치, 크기 및 앱을 호스트하는 웹 서버 팜의 기능을 지정합니다. 단일 App Service 계획을 공유하도록 웹앱을 구성하여 여러 앱을 호스팅하는 비용을 절감할 수 있습니다. App Service 요금제는 지역, 인스턴스 크기, 스케일링 횟수, SKU(무료, 공유, 기본, 표준 또는 프리미엄)를 정의합니다. **새로 만들기** 를 선택하여 새 App Service 요금제를 만듭니다. 무료 및 기본 계층은 지속적 사이트 실행을 유지하는 Always On 옵션을 지원하지 않습니다. |

    ![App Service 만들기 대화 상자](./media/webjobs-publish-netcore/app-service-dialog.png)

1. **만들기** 를 선택하여 이러한 설정으로 Azure에 WebJob 및 관련 리소스를 만들고 프로젝트 코드를 배포합니다.

1. **마침** 을 선택하여 **게시** 페이지로 돌아갑니다.  