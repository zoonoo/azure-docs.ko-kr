---
title: Azure Functions에 대한 연속 배포
description: Azure App Service의 지속적인 배포 기능을 사용하여 함수를 게시합니다.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277025"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions에 대한 연속 배포

Azure Functions를 사용하여 [소스 제어 통합을](functions-deployment-technologies.md#source-control)사용하여 코드를 지속적으로 배포할 수 있습니다. 소스 제어 통합을 사용하면 코드 업데이트가 Azure에 대한 배포를 트리거하는 워크플로를 사용할 수 있습니다. Azure 함수를 새로 사용하는 경우 [Azure Functions 개요를](functions-overview.md)검토하여 시작하십시오.

지속적인 배포는 여러 번의 기여를 통합하는 프로젝트에 적합합니다. 지속적인 배포를 사용하는 경우 코드에 대한 단일 소스를 유지 관리하므로 팀이 쉽게 공동 작업할 수 있습니다. 다음 소스 코드 위치에서 Azure Functions에서 지속적인 배포를 구성할 수 있습니다.

* [Azure 리포지토리](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [비트 버킷](https://bitbucket.org/)

Azure의 함수에 대한 배포 단위는 함수 앱입니다. 함수 앱의 모든 함수가 동시에 배포됩니다. 연속 배포를 사용하도록 설정하면 검색 소스가 다른 곳으로 설정되어 있으므로 Azure Portal의 함수 코드에 대한 액세스가 *읽기 전용으로* 구성됩니다.

## <a name="requirements-for-continuous-deployment"></a>지속적인 배포를 위한 요구 사항

지속적인 배포가 성공하려면 디렉터리 구조가 Azure Functions가 기대하는 기본 폴더 구조와 호환되어야 합니다.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 소비 계획에서 실행 중인 Linux 앱에는 지속적인 배포가 아직 지원되지 않습니다. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>연속 배포 설정

기존 함수 앱에 대한 연속 배포를 구성하려면 다음 단계를 완료합니다. 이 단계는 GitHub 리포지토리와의 통합을 보여 주지만 Azure 리포지토리 또는 기타 소스 코드 리포지토리에 대해 유사한 단계가 적용됩니다.

1. [Azure 포털의](https://portal.azure.com)함수 앱에서 **플랫폼 기능** > **배포 센터를**선택합니다.

    ![개방형 배포 센터](./media/functions-continuous-deployment/platform-features.png)

2. **배포 센터에서** **GitHub를**선택한 다음 **권한 부여를**선택합니다. 이미 GitHub를 승인한 경우 **계속을**선택합니다. 

    ![Azure 앱 서비스 배포 센터](./media/functions-continuous-deployment/github.png)

3. GitHub에서 **AzureAppService 권한 부여** 단추를 선택합니다. 

    ![Azure 앱 서비스 권한 부여](./media/functions-continuous-deployment/authorize.png)
    
    Azure 포털의 **배포 센터에서** **계속을**선택합니다.

4. 다음 빌드 공급자 중 하나를 선택합니다.

    * **앱 서비스 빌드 서비스**: 빌드가 필요하지 않거나 일반 빌드가 필요한 경우에 가장 적합합니다.
    * **Azure 파이프라인(미리 보기)**: 빌드를 더 많이 제어해야 하는 경우에 가장 적합합니다. 이 공급자는 현재 미리 보기 상태입니다.

    ![빌드 공급자 선택](./media/functions-continuous-deployment/build.png)

5. 지정한 소스 제어 옵션과 관련된 정보를 구성합니다. GitHub의 경우 **조직,** **저장소**및 **분기에**대한 값을 입력하거나 선택해야 합니다. 값은 코드의 위치를 기반으로 합니다. 그런 다음, **계속**을 선택합니다.

    ![GitHub 구성](./media/functions-continuous-deployment/github-specifics.png)

6. 모든 세부 정보를 검토한 다음 **완료를** 선택하여 배포 구성을 완료합니다.

    ![요약](./media/functions-continuous-deployment/summary.png)

프로세스가 완료되면 지정된 소스의 모든 코드가 앱에 배포됩니다. 이 때 배포 소스의 변경은 Azure의 함수 앱에 대한 이러한 변경 내용의 배포를 트리거합니다.

## <a name="deployment-scenarios"></a>배포 시나리오

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>기존 함수를 연속 배포로 이동

[Azure Portal에서](https://portal.azure.com) 함수를 이미 작성했으며 연속 배포로 전환하기 전에 앱의 내용을 다운로드하려면 함수 앱의 **개요** 탭으로 이동합니다. 앱 콘텐츠 다운로드 버튼을 **선택합니다.**

![앱 콘텐츠 다운로드](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 연속 통합을 구성한 후에는 Functions 포털에서 소스 파일을 더 이상 편집할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 기능에 대한 모범 사례](functions-best-practices.md)
