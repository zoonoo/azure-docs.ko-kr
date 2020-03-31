---
title: 패키지에서 Azure 함수 실행
description: 함수 앱 프로젝트 파일을 포함하는 배포 패키지 파일을 탑재하여 Azure Functions 런타임이 함수를 실행하게 합니다.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365274"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>패키지에서 Azure Functions 실행

Azure의 함수 앱의 배포 패키지 파일에서 직접 함수를 실행할 수 있습니다. 다른 옵션은 함수 앱의 `d:\home\site\wwwroot` 디렉터리에 파일을 배포하는 것입니다.

이 문서에서는 패키지에서 함수를 실행하는 이점에 대해 설명합니다. 또한 함수 앱에서 이 기능을 사용하도록 설정하는 방법을 보여줍니다.

> [!IMPORTANT]
> [프리미엄 계획에서](functions-scale.md#premium-plan)Linux 함수 앱에 함수를 배포할 때항상 패키지 파일에서 실행하고 [Azure Functions 핵심 도구를 사용하여 앱을 게시해야](functions-run-local.md#project-file-deployment)합니다.

## <a name="benefits-of-running-from-a-package-file"></a>패키지 파일에서 실행하는 이점
  
패키지 파일에서 실행하는 것에 대한 여러 이점은 다음과 같습니다.

+ 파일 복사 잠금 문제의 위험을 줄여줍니다.
+ (다시 시작으로) 프로덕션 앱에 배포될 수 있습니다.
+ 앱에서 실행되는 파일을 확인할 수 있습니다.
+ [Azure Resource Manager 배포](functions-infrastructure-as-code.md)의 성능을 향상시킵니다.
+ 특히 대규모 npm 패키지 트리가 있는 JavaScript 함수의 경우 콜드 부팅 시간을 줄일 수 있습니다.

자세한 내용은 [이 공지를](https://github.com/Azure/app-service-announcements/issues/84)참조하십시오.

## <a name="enabling-functions-to-run-from-a-package"></a>패키지에서 함수를 실행하도록 설정

패키지에서 함수 앱을 실행하도록 설정하려면 함수 앱 설정에 `WEBSITE_RUN_FROM_PACKAGE` 설정을 추가하면 됩니다. `WEBSITE_RUN_FROM_PACKAGE` 설정에는 다음 값 중 하나가 있어야 합니다.

| 값  | 설명  |
|---------|---------|
| **`1`**  | Windows에서 실행되는 기능 앱에 권장됩니다. 함수 앱의 `d:\home\data\SitePackages` 폴더의 패키지 파일에서 실행합니다. zip [deploy를 사용하여 배포하지](#integration-with-zip-deployment)않는 경우 이 옵션을 사용하려면 폴더에 이름이 지정된 `packagename.txt`파일이 있어야 합니다. 이 파일에는 공백 없이 폴더에 패키지 파일 이름만 포함됩니다. |
|**`<URL>`**  | 실행하려는 특정 패키지 파일의 위치입니다. Blob Storage를 사용하는 경우 [SAS(공유 액세스 서명)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)가 포함된 프라이빗 컨테이너를 사용하여 Functions 런타임이 패키지에 액세스할 수 있게 해야 합니다. [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 Blob 스토리지 계정에 패키지 파일을 업로드할 수 있습니다. URL을 지정할 때 업데이트된 패키지를 게시한 후에도 [트리거를 동기화해야](functions-deployment-technologies.md#trigger-syncing) 합니다. |

> [!CAUTION]
> Windows에서 함수 앱을 실행할 때 외부 URL 옵션을 사용하면 콜드 스타트 성능이 저하됩니다. Windows에 함수 앱을 배포할 때 `WEBSITE_RUN_FROM_PACKAGE` zip `1` 배포를 사용하여 설정하고 게시해야 합니다.

다음은 Azure Blob Storage에 호스트된 .zip 파일에서 실행되도록 구성된 함수 앱을 보여줍니다.

![WEBSITE_RUN_FROM_ZIP 앱 설정](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 현재는 .zip 패키지 파일만 지원됩니다.

## <a name="integration-with-zip-deployment"></a>Zip 배포와 통합

[Zip 배포][Zip deployment for Azure Functions]는 Azure App Service의 기능으로서 함수 앱 프로젝트를 `wwwroot` 디렉터리에 배포할 수 있습니다. 프로젝트는 .zip 배포 파일로 패키지됩니다. 패키지를 `d:\home\data\SitePackages` 폴더에 배포하는 데 동일한 API를 사용할 수 있습니다. `1`의 `WEBSITE_RUN_FROM_PACKAGE` 앱 설정 값을 사용하여 Zip 배포 API는 `d:\home\site\wwwroot`에 파일을 추출하지 않고 `d:\home\data\SitePackages` 폴더에 패키지를 복사합니다. 또한 `packagename.txt` 파일도 만듭니다. 다시 시작하면 패키지가 읽기 전용 `wwwroot` 파일 시스템으로 탑재됩니다. Zip 배포에 대한 자세한 내용은 [Azure Functions에 대한 Zip 배포](deployment-zip-push.md)를 참조하세요.

## <a name="adding-the-website_run_from_package-setting"></a>WEBSITE_RUN_FROM_PACKAGE 설정 추가

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>문제 해결

- 패키지에서 실행읽기 `wwwroot` 전용으로 사용되므로 이 디렉터리에 파일을 쓸 때 오류가 발생합니다.
- 타르 및 gzip 형식은 지원되지 않습니다.
- 이 기능은 로컬 캐시로 구성되지 않습니다.
- 콜드 스타트 성능을 향상시면 로컬 Zip`WEBSITE_RUN_FROM_PACKAGE`옵션(=1)을 사용합니다.
- 패키지에서 실행 배포 사용자 지정 옵션`SCM_DO_BUILD_DURING_DEPLOYMENT=true`() 호환 되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
