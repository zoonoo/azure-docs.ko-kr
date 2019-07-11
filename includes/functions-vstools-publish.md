---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1a8ecd0e53d28aed66546c41f95cc3855d1783a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608281"
---
1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2. **Azure 함수 앱**을 선택하고 **새로 만들기**를 선택한 다음, **게시**를 선택합니다.

    ![게시 대상 선택](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png) 

    **패키지 파일에서 실행(권장)** 을 클릭하면 [패키지에서 실행](../articles/azure-functions/run-functions-from-deployment-package.md) 모드가 활성화되어 [Zip 배포](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy)를 사용하여 함수 앱이 배포됩니다. 이는 함수를 실행하는 권장 방법이며 성능이 향상됩니다.

    >[!CAUTION]
    >**기존 항목 선택**을 선택하면 Azure에 있는 기존 함수 앱의 모든 파일을 로컬 프로젝트의 파일로 덮어씁니다. 기존 함수 앱에 업데이트를 다시 게시하는 경우에만 이 옵션을 사용합니다.

3. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.

4. **App Service 만들기** 대화 상자에서 이미지 아래의 테이블에 지정된 대로 **호스팅** 설정을 사용합니다.

    ![App Service 만들기 대화 상자](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | 설정      | 제안 값  | 설명                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. |
    | **구독** | 구독 선택 | 사용할 Azure 구독입니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  함수 앱을 만들 리소스 그룹의 이름입니다. **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[App Service 계획](../articles/azure-functions/functions-scale.md)** | 소비 계획 | **새로 만들기**를 클릭하여 서버리스 계획을 만든 후, **크기** 아래에서 **소비**를 선택해야 합니다. 또한 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)의 **위치**를 선택합니다. **소비** 이외의 계획에서 실행하는 경우 [함수 앱의 크기 조정](../articles/azure-functions/functions-scale.md)을 관리해야 합니다.  |
    | **[Storage 계정](../articles/storage/common/storage-quickstart-create-account.md)** | 범용 스토리지 계정 | Functions 런타임에는 Azure Storage 계정이 필요합니다. **새로 만들기**를 클릭하여 범용 스토리지 계정을 만듭니다. [저장소 계정 요구 사항](../articles/azure-functions/functions-scale.md#storage-account-requirements)을 충족하는 기존 계정을 사용할 수도 있습니다.  |

5. **만들기**를 클릭하여 이러한 설정으로 Azure에서 함수 앱 및 관련 리소스를 만들고 함수 프로젝트 코드를 배포합니다. 

6. 베포가 완료된 후에 Azure에서 함수 앱의 주소인 **사이트 URL** 값을 기록해 둡니다.

    ![게시 성공 메시지](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
