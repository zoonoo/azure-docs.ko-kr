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
ms.openlocfilehash: 2dc5fab0966c2ead0276cd8b23ea764bd4f9ef59
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190470"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions에 대한 연속 배포

Azure Functions를 사용 하면 지속적으로 통해 코드를 배포할 수 있습니다 [원본 제어 통합](functions-deployment-technologies.md#source-control)합니다. 이 코드는 트리거를 업데이트 하는 워크플로 통해 Azure에 배포 합니다. Azure Functions를 처음 접하는 경우 시작 합니다 [Azure Functions 개요](functions-overview.md)합니다.

연속 배포는 여러를 통합 하는 프로젝트에 적합 한 옵션 및 자주 사용 하는 기여입니다. 또한 함수 코드에 대 한 정보의 단일 원본을 유지 관리할 수 있습니다. 다음 소스 코드 위치에서 Azure Functions에서 연속 배포를 구성할 수 있습니다.

* [Azure 리포지토리](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure functions에 대 한 배포 단위는 함수 앱입니다. 이 함수 앱에서 모든 함수를 동시에 배포 되는 것을 의미 합니다. Azure portal에서 함수 코드에 대 한 액세스도 구성 된 연속 배포를 설정한 후 *읽기 전용*다른 위치를 원본으로 설정 되어 있으므로, 합니다.

## <a name="requirements-for-continuous-deployment"></a>연속 배포를 위한 요구 사항

성공 하려면 연속 배포에 대 한 디렉터리 구조 Azure Functions가 필요로 하는 다음 기본 폴더 구조를 사용 하 여 호환 되어야 합니다.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>연속 배포 설정

다음 절차를 사용하여 기존 함수 앱에 대한 연속 배포를 구성합니다. 이러한 단계는 GitHub 리포지토리를 사용 하 여 통합을 시연 하기 하지만 Azure 리포지토리 또는 다른 소스 코드 리포지토리에 유사한 단계가 적용 됩니다.

1. 함수 앱에는 [Azure portal](https://portal.azure.com)를 선택 **플랫폼 기능** > **배포 센터**합니다.

    ![배포 센터](./media/functions-continuous-deployment/platform-features.png)

2. 에 **배포 센터**를 선택 **GitHub**를 선택한 후 **권한 부여**합니다. 또는 GitHub에 이미 부여한 경우 선택할 **계속**합니다. 

    ![배포 센터](./media/functions-continuous-deployment/github.png)

3. GitHub에서 선택 **권한을 부여 AzureAppService**합니다. 

    ![권한 부여](./media/functions-continuous-deployment/authorize.png)
    
    Azure portal에서 **Deployment Center**를 선택 **계속**합니다.

4. 다음 빌드 공급자 중 하나를 선택 합니다.

    * **App Service 서비스 빌드** -빌드를 필요 하지 않을 때 또는 일반 빌드를 해야 하는 경우 가장 합니다.
    * **Azure 파이프라인 (미리 보기)** -빌드를 더 많이 제어 해야 하는 경우 가장 합니다. 이 공급자는 현재 미리 보기 중입니다.

    ![빌드 공급자를 선택합니다.](./media/functions-continuous-deployment/build.png)

5. 지정한 원본 제어 옵션에 대 한 정보를 구성 합니다. GitHub에 제공 해야 합니다는 **조직**를 **리포지토리**, 및 **분기** 코드의 현재 위치입니다. 그런 다음, **계속**을 선택합니다.

    ![GitHub 구성](./media/functions-continuous-deployment/github-specifics.png)

6. 마지막으로, 모든 세부 정보를 검토 하 고 선택 **완료** 배포 구성을 완료 합니다.

    ![요약](./media/functions-continuous-deployment/summary.png)

프로세스가 완료 되 면 지정된 된 소스에서 모든 코드는 앱에 배포 됩니다. 이때 배포 원본에 변경 내용이 Azure에서 함수 앱으로 배포를 트리거합니다.

## <a name="deployment-scenarios"></a>배포 시나리오

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>기존 함수를 연속 배포로 이동

함수를 이미 작성 한 경우는 [Azure portal](https://portal.azure.com) 연속 배포로 전환 하기 전에 응용 프로그램의 콘텐츠를 다운로드 하려면를 이동 해야 합니다 **개요** 함수의 탭 앱을 클릭 합니다 **앱 콘텐츠 다운로드** 단추입니다.

![앱 콘텐츠 다운로드](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 연속 통합을 구성한 후에 Functions 포털에서 소스 파일을 더 이상 편집할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례](functions-best-practices.md)
