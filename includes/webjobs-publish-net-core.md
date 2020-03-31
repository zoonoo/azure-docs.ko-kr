---
title: 포함 파일
description: 포함 파일
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021147"
---
1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시** 대화 상자에서 **Microsoft Azure 앱 서비스를**선택하고 **새 만들기를**선택한 다음 **게시를**선택합니다.

   ![게시 대상 선택](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 앱 **서비스 만들기** 대화 상자에서 이미지 아래의 표에 지정된 호스팅 설정을 사용합니다.

    ![App Service 만들기 대화 상자](./media/webjobs-publish-netcore/app-service-dialog.png)

    | 설정      | 제안 값  | 설명                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. |
    | **구독** | 구독 선택 | 사용할 Azure 구독입니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  함수 앱을 만들 리소스 그룹의 이름입니다. **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[호스팅 계획](../articles/app-service/overview-hosting-plans.md)** | App Service 계획 | [앱 서비스 계획은](../articles/app-service/overview-hosting-plans.md) 앱을 호스팅하는 웹 서버 팜의 위치, 크기 및 기능을 지정합니다. 단일 App Service 계획을 공유하도록 웹앱을 구성하여 여러 앱을 호스팅하는 비용을 절감할 수 있습니다. 앱 서비스 계획은 지역, 인스턴스 크기, 배율 및 SKU(무료, 공유, 기본, 표준 또는 프리미엄)를 정의합니다. 새 앱 서비스 계획을 만들려면 **새로 를** 선택합니다. |

1. **만들기를** 클릭하여 이러한 설정을 사용하여 Azure에서 WebJob 및 관련 리소스를 만들고 프로젝트 코드를 배포합니다.