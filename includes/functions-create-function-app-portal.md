---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e6387c528bb24a66edb2fa1706adc005980dc90e
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47185018"
---
1. Azure Portal의 왼쪽 위 모서리에 있는 **새로 만들기** 단추를 선택한 다음, **계산** > **Function App**을 차례로 선택합니다.

    ![Azure Portal에서 함수 앱 만들기](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. 이미지 아래 표에 지정된 것처럼 함수 앱 설정을 사용합니다.

    ![새 함수 앱 설정 정의](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. | 
    | **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    | **OS** | Windows | 서버 없는 호스팅은 현재 Windows에서 실행될 때만 가능합니다. Linux 호스팅의 경우 [Azure CLI를 사용하여 Linux에서 실행되는 첫 번째 함수 만들기](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md)를 참조하세요. |
    | **[호스팅 계획](../articles/azure-functions/functions-scale.md)** | 소비 계획 | 함수 앱에 리소스가 할당되는 방법을 정의하는 호스팅 계획입니다. 기본 **소비 계획**에서 함수의 필요에 따라 리소스가 동적으로 추가됩니다. [서버 없는](https://azure.microsoft.com/overview/serverless-computing/) 호스팅에서는 함수가 실행되는 시간 만큼만 요금을 지불하면 됩니다. App Service 계획에서 실행하는 경우 [함수 앱의 크기 조정](../articles/azure-functions/functions-scale.md)을 관리해야 합니다.  |
    | **위치**: | 서유럽 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **런타임 스택** | 기본 설정 언어 | 즐겨찾는 함수 프로그래밍 언어를 지원하는 런타임을 선택합니다. C# 및 F# 함수의 경우 **.NET**을 선택합니다. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 저장소 계정을 만듭니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [저장소 계정 요구 사항](../articles/azure-functions/functions-scale.md#storage-account-requirements)을 충족해야 합니다. |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 기본값 | Application Insights는 기본적으로 사용됩니다. 함수 앱 근처의 위치를 선택합니다.  |

3. **만들기**를 선택하여 함수 앱을 프로비전하고 배포합니다.

4. 포털의 오른쪽 위 모서리에 있는 [알림] 아이콘을 선택하고 **배포 성공** 메시지가 표시될 때까지 기다립니다.

    ![새 함수 앱 설정 정의](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. **리소스로 이동**을 선택하여 함수 앱을 봅니다.

> [!TIP]
> 포털에서 함수 앱을 찾는 데 문제가 있는 경우 [Azure Portal에서 즐겨찾기에 Function App을 추가](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#favorite)합니다.
