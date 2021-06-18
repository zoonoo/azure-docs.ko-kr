---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.openlocfilehash: 9cbab70d37c689967c7129b3205a9194e9de9ade
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350540"
---
1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택하고 **대상** 에서 **Azure** 를 선택한 다음, **다음** 을 선택합니다.

1. **특정 대상** 의 경우 Windows에서 실행되는 함수 앱을 만드는 **Azure 함수 앱(Windows)** 을 선택합니다.

1. **함수 인스턴스** 에서 **새 Azure 함수 만들기...** 를 선택합니다. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="새 함수 앱 인스턴스 만들기":::

1. 다음 표에 지정된 값을 사용하여 새 인스턴스를 만듭니다.

    | 설정      | 값  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. 이 이름을 수락하거나 새 이름을 입력합니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | **구독** | 사용자의 구독 | 사용할 Azure 구독입니다. 이 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/management/overview.md)** | 리소스 그룹의 이름 |  함수 앱을 만들 리소스 그룹입니다. 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[계획 유형](../articles/azure-functions/functions-scale.md)** | Consumption | 프로젝트를 [사용량 요금제](../articles/azure-functions/consumption-plan.md)에서 실행되는 함수 앱에 게시하는 경우 함수 앱의 실행에 대한 비용만 지불합니다. 다른 호스팅 계획에는 비용이 더 많이 듭니다. |
    | **위치** | App Service의 위치 | 사용자 또는 함수가 액세스하는 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)의 **위치** 를 선택합니다. |
    | **[Azure Storage](../articles/azure-functions/storage-considerations.md)** | 범용 스토리지 계정 | Functions 런타임에는 Azure Storage 계정이 필요합니다. **새로 만들기** 를 선택하여 범용 스토리지 계정을 구성합니다. [스토리지 계정 요구 사항](../articles/azure-functions/storage-considerations.md#storage-account-requirements)을 충족하는 기존 계정을 선택할 수도 있습니다.  |

    ![App Service 만들기 대화 상자](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. **만들기** 를 선택하여 Azure에서 함수 앱 및 관련된 리소스를 만듭니다. 리소스 만들기 상태는 창의 왼쪽 하단에 표시됩니다. 

1. **함수 인스턴스** 로 돌아가서 **패키지 파일에서 실행** 이 선택되어 있는지 확인합니다. [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) 모드를 사용하도록 설정된 상태에서 [Zip 배포](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy)를 사용하여 함수 앱이 배포됩니다. 이는 성능이 향상되므로 함수 프로젝트에 권장되는 배포 방법입니다. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="프로필 만들기 완료":::

1. **마침** 를 선택하고 게시 페이지에서 **게시** 를 선택하여 프로젝트 파일이 포함된 패키지를 Azure의 새 함수 앱에 배포합니다. 

    배포가 완료되면 Azure에서 함수 앱의 루트 URL이 **게시** 탭에 표시됩니다. 
    
1.  게시 탭에서 **클라우드 탐색기에서 관리** 를 선택합니다. 그러면 클라우드 탐색기에서 새 함수 앱 Azure 리소스가 열립니다. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="게시 성공 메시지":::
    
    클라우드 탐색기를 통해 Visual Studio를 사용하여 사이트의 콘텐츠를 확인하고, 함수 앱을 시작 및 중지하고, Azure 및 Azure Portal에서 함수 앱 리소스를 직접 찾아볼 수 있습니다. 
