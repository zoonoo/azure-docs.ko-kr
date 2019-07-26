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
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443967"
---
1. [Azure 포털](https://portal.azure.com)로 이동합니다.

1. 왼쪽에서 **+ 리소스 만들기** 를 선택 하 고 **함수 앱**을 선택 합니다.

1. **호스팅 계획**의 경우 **App Service 계획**을 선택한 다음 **App Service 계획/위치**를 선택 합니다.

    ![함수 앱 만들기](./media/functions-premium-create/create-function-app-resource.png)

1. **새로 만들기**를 선택 하 고, **App Service 계획** 이름을 입력 하 고, 사용자의 함수 액세스에 가까운 [지역](https://azure.microsoft.com/regions/) 또는 다른 서비스에 대 한 **위치** 를 선택 하 고 **가격 책정 계층**을 선택 합니다.

    ![App Service 플랜 만들기](./media/functions-premium-create/new-app-service-plan.png)

1. **EP1** (탄력적 프리미엄) 계획을 선택 하 고 **적용**을 선택 합니다.

    ![프리미엄 요금제 선택](./media/functions-premium-create/hosting-plan.png) 

1. **확인** 을 선택 하 여 계획을 만든 다음 이미지 아래의 표에 지정 된 대로 나머지 함수 앱 설정을 사용 합니다. 

    ![완료 된 app service 계획](./media/functions-premium-create/create-function-app.png)  

    | 설정      | 제안된 값  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. 제안 된 값을 사용할 수도 있습니다. |
    | **OS** | 기본 설정 OS | Linux와 Windows는 모두 프리미엄 계획에서 지원 됩니다. |
    | **런타임 스택** | 기본 설정 언어 | 즐겨찾는 함수 프로그래밍 언어를 지원하는 런타임을 선택합니다. C# 및 F# 함수의 경우 **.NET**을 선택합니다. 선택한 **OS** 에서 지원 되는 언어만 표시 됩니다. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 저장소 계정을 만듭니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [저장소 계정 요구 사항](../articles/azure-functions/functions-scale.md#storage-account-requirements)을 충족해야 합니다. |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 기본값 | 가장 가까운 지원 영역에 동일한 *앱 이름*의 Application Insight 리소스를 만듭니다. 이 설정을 확장하면 **새 리소스 이름**을 변경하거나 데이터를 저장하려는 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에서 다른 **위치**를 선택합니다. |

1. 설정의 유효성을 검사 한 후 **만들기**를 선택 합니다.

1. 포털의 오른쪽 위 모서리에 있는 [알림] 아이콘을 선택하고 **배포 성공** 메시지가 표시될 때까지 기다립니다.

    ![새 함수 앱 설정 정의](./media/functions-premium-create/function-app-create-notification.png)

1. **리소스로 이동**을 선택하여 함수 앱을 봅니다. **대시보드에 고정**을 선택할 수도 있습니다. 고정하면 대시보드에서 이 함수 앱 리소스로 쉽게 돌아올 수 있습니다.