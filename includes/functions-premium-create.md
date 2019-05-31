---
title: 포함 파일
description: 포함 파일
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131551"
---
1. [Azure 포털](https://portal.azure.com)로 이동합니다.

1. 선택 **+ 리소스 만들기** 의 왼쪽에서 선택한 **함수 앱**합니다.

1. 에 대 한 **호스팅 계획**, 선택 **App Service 계획**을 선택한 후 **App Service 계획/위치**합니다.

    ![함수 앱 만들기](./media/functions-premium-create/create-function-app-resource.png)

1. 선택 **새로 만들기**, 형식은 **App Service 계획** 이름, 선택는 **위치** 에 [지역](https://azure.microsoft.com/regions/) 가까운 또는 다른 거의 services 함수 에 액세스 하 고 선택한 **가격 책정 계층**합니다.

    ![App Service 플랜 만들기](./media/functions-premium-create/new-app-service-plan.png)

1. 선택 합니다 **EP1** (탄력적 프리미엄)을 선택한 다음 선택 **적용**합니다.

    ![프리미엄 요금제를 선택 합니다.](./media/functions-premium-create/hosting-plan.png) 

1. 선택 **확인** 이미지 아래의 표에 지정 된 계획을 만든 다음으로 나머지 함수 앱 설정을 사용 합니다. 

    ![완성 된 앱 서비스 계획](./media/functions-premium-create/create-function-app.png)  

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. 또한 제안 된 값을 사용할 수 있습니다. |
    | **OS** | Windows | Linux는 Premium 계획에 현재 지원 되지 않습니다. |
    | **런타임 스택** | 기본 설정 언어 | 즐겨찾는 함수 프로그래밍 언어를 지원하는 런타임을 선택합니다. C# 및 F# 함수의 경우 **.NET**을 선택합니다. 선택한 지원 되는 언어만 **OS** 표시 됩니다. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 저장소 계정을 만듭니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [저장소 계정 요구 사항](../articles/azure-functions/functions-scale.md#storage-account-requirements)을 충족해야 합니다. |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Default | 가장 가까운 지원 영역에 동일한 *앱 이름*의 Application Insight 리소스를 만듭니다. 이 설정을 확장하면 **새 리소스 이름**을 변경하거나 데이터를 저장하려는 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에서 다른 **위치**를 선택합니다. |

1. 설정을 확인 되 면 선택 **만들기**합니다.

1. 포털의 오른쪽 위 모서리에 있는 [알림] 아이콘을 선택하고 **배포 성공** 메시지가 표시될 때까지 기다립니다.

    ![새 함수 앱 설정 정의](./media/functions-premium-create/function-app-create-notification.png)

1. **리소스로 이동**을 선택하여 함수 앱을 봅니다. **대시보드에 고정**을 선택할 수도 있습니다. 고정하면 대시보드에서 이 함수 앱 리소스로 쉽게 돌아올 수 있습니다.