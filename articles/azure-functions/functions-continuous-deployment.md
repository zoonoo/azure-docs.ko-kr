---
title: Azure Functions에 대한 연속 배포 | Microsoft 문서
description: 함수를 게시 하려면 Azure App Service의 연속 배포 기능을 사용 합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594556"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions에 대한 연속 배포

Azure Functions를 사용 하 여 코드를 사용 하 여 지속적으로 배포 [원본 제어 통합](functions-deployment-technologies.md#source-control)합니다. 소스 제어 통합 코드 업데이트를 트리거 Azure에 배포 하는 워크플로 수 있도록 합니다. Azure Functions를 처음 접하는 경우 검토 하 여 시작 합니다 [Azure Functions 개요](functions-overview.md)합니다.

연속 배포는 여러를 통합 하는 위치 프로젝트에 대 한 좋은 작성 글을 자주 및입니다. 연속 배포를 사용 하면 쉽게 공동 작업할 수는 팀을 허용 하는 코드에 대 한 정보의 단일 원본을 유지 합니다. 다음 소스 코드 위치에서 Azure Functions에서 연속 배포를 구성할 수 있습니다.

* [Azure 리포지토리](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure에서 함수에 대 한 배포 단위는 함수 앱입니다. 함수 앱에서 모든 함수는 동시에 배포 됩니다. 연속 배포를 사용 하면 Azure portal에서 함수 코드에 대 한 액세스로 구성 됩니다 *읽기 전용* 다른 위치를 원본으로 설정 되어 있기 때문입니다.

## <a name="requirements-for-continuous-deployment"></a>연속 배포를 위한 요구 사항

성공 하려면 연속 배포에 대 한 디렉터리 구조는 Azure Functions가 필요로 하는 기본 폴더 구조와 호환 되어야 합니다.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>연속 배포 설정

기존 함수 앱에 대 한 연속 배포를 구성 하려면 다음이 단계를 완료 합니다. 단계는 GitHub 리포지토리를 사용 하 여 통합을 시연 하기 하지만 Azure 리포지토리 또는 다른 소스 코드 리포지토리에 유사한 단계가 적용 됩니다.

1. 함수 앱에는 [Azure portal](https://portal.azure.com)를 선택 **플랫폼 기능** > **배포 센터**합니다.

    ![배포 센터 열기](./media/functions-continuous-deployment/platform-features.png)

2. **배포 센터**를 선택 **GitHub**를 선택한 후 **Authorize**합니다. 이미 GitHub를 승인한 경우 선택 **계속**합니다. 

    ![Azure App Service 배포](./media/functions-continuous-deployment/github.png)

3. GitHub에서 선택 합니다 **권한 부여 AzureAppService** 단추입니다. 

    ![Azure App Service 인증](./media/functions-continuous-deployment/authorize.png)
    
    **Deployment Center** Azure portal에서 선택 **계속**합니다.

4. 다음 빌드 공급자 중 하나를 선택 합니다.

    * **App Service 서비스 빌드**: 빌드에 필요 하지 않을 때 또는 일반 빌드를 해야 하는 경우 가장 합니다.
    * **Azure 파이프라인 (미리 보기)** : 빌드를 보다 자세히 제어 해야 하는 경우 최선의 토폴로지 이 공급자는 현재 미리 보기 상태인 합니다.

    ![빌드 공급자를 선택 합니다.](./media/functions-continuous-deployment/build.png)

5. 지정한 원본 제어 옵션에 대 한 정보를 구성 합니다. GitHub에 대 한 입력 하거나 값을 선택 해야 합니다 **조직**를 **리포지토리**, 및 **분기**합니다. 값은 코드의 위치를 기반으로 합니다. 그런 다음, **계속**을 선택합니다.

    ![GitHub 구성](./media/functions-continuous-deployment/github-specifics.png)

6. 모든 세부 정보를 검토 하 고 선택한 **완료** 배포 구성을 완료 합니다.

    ![요약](./media/functions-continuous-deployment/summary.png)

프로세스가 완료 되 면 지정된 된 소스에서 모든 코드는 앱에 배포 됩니다. 이때 배포 원본에 변경 내용이 Azure에서 함수 앱으로 배포를 트리거합니다.

## <a name="deployment-scenarios"></a>배포 시나리오

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>기존 함수를 연속 배포로 이동

함수를 이미 작성 한 경우를 [Azure portal](https://portal.azure.com) 연속 배포로 전환 하기 전에 앱의 콘텐츠를 다운로드 하려면로 이동 합니다 **개요** 함수 앱의 탭 합니다. 선택 된 **앱 콘텐츠 다운로드** 단추입니다.

![앱 콘텐츠 다운로드](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 연속 통합을 구성한 후에 Functions 포털에서 소스 파일을 더 이상 편집할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례](functions-best-practices.md)
