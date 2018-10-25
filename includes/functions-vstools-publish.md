---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 08/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f3fa6c75fdaf30d13231bc3072bd671614c9a668
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650695"
---
1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2. **Azure 함수 앱**을 선택하고, **새로 만들기**를 선택한 다음, **게시**를 선택합니다.

    ![게시 대상 선택](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    **Zip에서 실행**을 사용하도록 설정하면 Azure의 함수 앱이 배포 패키지에서 직접 실행됩니다. 자세한 내용은 [패키지 파일에서 Azure Functions 실행](../articles/azure-functions/run-functions-from-deployment-package.md)을 참조하세요.

3. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.

4. **App Service 만들기** 대화 상자에서 이미지 아래의 테이블에 지정된 대로 **호스팅** 설정을 사용합니다.

    ![App Service 만들기 대화 상자](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | 설정      | 제안 값  | 설명                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. |
    | **구독** | 구독 선택 | 사용할 Azure 구독입니다. |
    | **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  함수 앱을 만들 리소스 그룹의 이름입니다. **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[App Service 계획](../articles/azure-functions/functions-scale.md)** | 소비 계획 | **새로 만들기**를 클릭하여 서버리스 계획을 만든 후, **크기** 아래에서 **소비**를 선택해야 합니다. 또한 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)의 **위치**를 선택합니다. **소비** 이외의 계획에서 실행하는 경우 [함수 앱의 크기 조정](../articles/azure-functions/functions-scale.md)을 관리해야 합니다.  |
    | **[Storage 계정](../articles/storage/common/storage-quickstart-create-account.md)** | 범용 저장소 계정 | Functions 런타임에는 Azure Storage 계정이 필요합니다. **새로 만들기**를 클릭하여 범용 저장소 계정을 만듭니다. [저장소 계정 요구 사항](../articles/azure-functions/functions-scale.md#storage-account-requirements)을 충족하는 기존 계정을 사용할 수도 있습니다.  |

5. **만들기**를 클릭하여 이러한 설정으로 Azure에서 함수 앱 및 관련 리소스를 만들고 함수 프로젝트 코드를 배포합니다. 

6. 베포가 완료된 후에 Azure에서 함수 앱의 주소인 **사이트 URL** 값을 기록해 둡니다.

    ![게시 성공 메시지](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
