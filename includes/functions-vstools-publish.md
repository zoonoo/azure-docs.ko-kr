---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84231523"
---
1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시 대상 선택**에서 다음 표에 지정된 게시 옵션을 사용합니다. 

    | 옵션      | Description                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions 소비 계획** | [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan)에서 실행되는 Azure 클라우드 환경에서 함수 앱을 만듭니다. 소비 계획을 사용하는 경우 함수 앱의 실행에 대해서만 비용을 지불합니다. 다른 호스팅 계획에는 비용이 더 많이 듭니다. 소비 계획 이외의 계획에서 실행하는 경우 [함수 앱의 크기 조정](../articles/azure-functions/functions-scale.md)을 관리해야 합니다.| 
    | **새로 만들기** | Azure에서 관련 리소스가 있는 새 함수 앱이 만들어집니다. <br/>**기존 항목 선택**을 선택하는 경우 Azure에 있는 기존 함수 앱의 모든 파일을 로컬 프로젝트의 파일로 덮어씁니다. 기존 함수 앱에 업데이트를 다시 게시하는 경우에만 이 옵션을 사용합니다. |
    | **패키지 파일에서 실행** | [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) 모드를 사용하도록 설정된 상태에서 [Zip 배포](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy)를 사용하여 함수 앱이 배포됩니다. 이 배포는 성능을 향상시키므로 함수를 실행하는 데 추천되는 방법입니다. <br/>이 옵션을 사용하지 않는 경우 Azure에 게시하기 전에 함수 앱 프로젝트가 로컬로 실행되지 않도록 해야 합니다. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="게시 프로필 만들기":::


1. **프로필 만들기**를 선택합니다. Visual Studio에서 Azure 계정에 아직 로그인하지 않은 경우 **로그인**을 선택합니다. Azure 체험 계정을 만들 수도 있습니다.

1. **App Service: 새로 만들기**에서 다음 표에 지정된 값을 사용합니다.

    | 설정      | 값  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. 이 이름을 수락하거나 새 이름을 입력합니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | **구독** | 사용자의 구독 | 사용할 Azure 구독입니다. 이 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/management/overview.md)** | 리소스 그룹의 이름 |  함수 앱을 만들 리소스 그룹입니다. 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[호스팅 계획](../articles/azure-functions/functions-scale.md)** | 호스팅 계획의 이름 | **새로 만들기**를 선택하여 서버리스 계획을 구성합니다. **크기** 아래에서 **소비**를 선택해야 합니다. 프로젝트를 [사용량 요금제](../articles/azure-functions/functions-scale.md#consumption-plan)에서 실행되는 함수 앱에 게시하는 경우 함수 앱의 실행에 대한 비용만 지불합니다. 다른 호스팅 계획에는 비용이 더 많이 듭니다. **소비** 이외의 계획에서 실행하는 경우 [함수 앱의 크기 조정](../articles/azure-functions/functions-scale.md)을 관리해야 합니다.  |
    | **위치** | App Service의 위치 | 사용자 또는 함수가 액세스하는 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)의 **위치**를 선택합니다. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | 범용 스토리지 계정 | Functions 런타임에는 Azure Storage 계정이 필요합니다. **새로 만들기**를 선택하여 범용 스토리지 계정을 구성합니다. [스토리지 계정 요구 사항](../articles/azure-functions/functions-scale.md#storage-account-requirements)을 충족하는 기존 계정을 선택할 수도 있습니다.  |

    ![App Service 만들기 대화 상자](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. **만들기**를 선택하여 이러한 설정으로 Azure에서 함수 앱 및 해당 관련 리소스를 만들고, 함수 프로젝트 코드를 배포합니다. 

1. **게시**를 선택하고 배포가 완료될 때까지 기다립니다. 

    배포가 완료되면 Azure에서 함수 앱의 루트 URL이 **게시** 탭에 표시됩니다. 
    
1.  게시 탭에서 **클라우드 탐색기에서 관리**를 선택합니다. 그러면 클라우드 탐색기에서 새 함수 앱 Azure 리소스가 열립니다. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="게시 프로필 만들기":::
    
    클라우드 탐색기를 통해 Visual Studio를 사용하여 사이트의 콘텐츠를 확인하고, 함수 앱을 시작 및 중지하고, Azure 및 Azure Portal에서 함수 앱 리소스를 직접 찾아볼 수 있습니다. 