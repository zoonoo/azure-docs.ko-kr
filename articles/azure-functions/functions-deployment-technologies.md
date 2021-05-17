---
title: Azure Functions의 배포 기술
description: Azure Functions에 코드를 배포할 수 있는 다양한 방법을 알아봅니다.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943632"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions의 배포 기술

몇 가지 다른 기술을 사용하여 Azure Functions 프로젝트 코드를 Azure에 배포할 수 있습니다. 이 문서에서는 사용자에게 제공되는 배포 방법을 간략하게 설명하고 다양한 시나리오에서 가장 적합한 방법에 대한 권장 지침을 제공합니다. 또한 기본 배포 기술의 모든 목록과 주요 세부 정보를 제공합니다. 

## <a name="deployment-methods"></a>배포 방법

Azure에 코드를 게시하는 데 사용하는 배포 기술은 일반적으로 앱을 게시하는 방법에 따라 결정됩니다. 적절한 배포 방법은 특정 요구 사항 및 개발 주기의 시점에 따라 결정됩니다. 예를 들어, 개발 및 테스트 중에 Visual Studio Code와 같은 개발 도구에서 직접 배포할 수 있습니다. 앱이 프로덕션 환경에 있는 경우 추가 유효성 검사 및 테스트를 포함하는 자동화된 게시 파이프라인을 사용하거나 소스 제어에서 계속 게시할 수 있습니다.  

다음 표에서는 함수 프로젝트에 사용할 수 있는 배포 방법에 대해 설명합니다.

| 배포&nbsp;유형 | 메서드 | 적합한 대상... |
| -- | -- | -- |
| 도구 기반 | &bull;&nbsp;[Visual&nbsp;Studio&nbsp;Code&nbsp;게시](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio 게시](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Core Tools 게시](functions-run-local.md#publish) | 개발 중 배포 및 기타 임시 배포 배포는 도구를 통해 로컬로 관리됩니다. | 
| App Service 관리| &bull;&nbsp;[배포&nbsp;센터&nbsp;(CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[컨테이너&nbsp;배포](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  소스 제어 또는 컨테이너 레지스트리에서 연속 배포(CI/CD) 배포는 App Service 플랫폼(Kudu)에서 관리됩니다.|
| 외부 파이프라인|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub 작업](functions-how-to-github-actions.md) | 추가 유효성 검사, 테스트 및 기타 작업을 포함하는 프로덕션 및 DevOps 파이프라인은 자동화된 배포의 일부로 실행됩니다. 배포는 파이프라인을 통해 관리됩니다. |

특정 함수 배포는 해당 컨텍스트를 기준으로 하는 최고의 기술을 사용하지만 대부분의 배포 방법은 [zip 배포](#zip-deploy)를 기준으로 합니다.

## <a name="deployment-technology-availability"></a>배포 기술 가용성

Azure Functions는 Windows 및 Linux의 플랫폼 간 로컬 개발 및 호스팅을 지원합니다. 현재 다음 세 가지 호스팅 플랜을 사용할 수 있습니다.

+ [Consumption](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [전용(App Service)](dedicated-plan.md)

플랜마다 동작이 다릅니다. Azure Functions의 각 버전에 대해 모든 배포 기술을 사용할 수 있는 것은 아닙니다. 다음 차트는 운영 체제 및 호스팅 플랜의 각 조합에 대해 지원되는 배포 기술을 보여 줍니다.

| 배포 기술 | Windows 사용 | Windows 프리미엄 | Windows 전용  | Linux 사용 | Linux 프리미엄 | Linux 전용 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 외부 패키지 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip 배포 |✔|✔|✔|✔|✔|✔|
| Docker 컨테이너 | | | | |✔|✔|
| 웹 배포 |✔|✔|✔| | | |
| 원본 제어 |✔|✔|✔| |✔|✔|
| 로컬 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 클라우드 동기화<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portal 편집 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> [수동 트리거 동기화](#trigger-syncing)가 필요한 배포 기술입니다.
<sup>2</sup> Portal 편집은 프리미엄 및 전용 플랜을 사용하는 Linux의 함수에 대한 HTTP 및 타이머 트리거에 대해서만 사용할 수 있습니다.

## <a name="key-concepts"></a>주요 개념

몇 가지 주요 개념은 Azure Functions에서 배포가 작동하는 방식을 이해하는 데 중요합니다.

### <a name="trigger-syncing"></a>트리거 동기화

트리거를 변경하는 경우 Functions 인프라에 변경 내용이 전달되어야 합니다. 여러 배포 기술에 대한 동기화가 자동으로 수행됩니다. 그러나 일부 경우에는 트리거를 수동으로 동기화해야 합니다. 외부 패키지 URL, 로컬 Git, 클라우드 동기화 또는 FTP를 참조하여 업데이트를 배포할 때 수동으로 트리거를 동기화해야 합니다. 다음 세 가지 방법 중 하나로 트리거를 동기화할 수 있습니다.

* Azure Portal에서 함수 앱을 다시 시작합니다.
* [마스터 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 사용하여 HTTP POST 요청을 `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`로 보냅니다.
* HTTP POST 요청을 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`로 보냅니다. 자리 표시자를 구독 ID, 리소스 그룹 이름 및 함수 앱의 이름으로 바꿉니다.

### <a name="remote-build"></a>원격 빌드

Azure Functions는 Zip 배포 후에 수신하는 코드에서 자동으로 빌드를 수행할 수 있습니다. 이러한 빌드는 앱이 Windows 또는 Linux에서 실행되는지에 따라 약간 다르게 동작합니다. 앱이 이전에 [패키지에서 실행](run-functions-from-deployment-package.md) 모드에서 실행되도록 설정된 경우 원격 빌드가 수행되지 않습니다. 원격 빌드를 사용하는 방법을 알아보려면 [zip 배포](#zip-deploy)로 이동합니다.

> [!NOTE]
> 원격 빌드에 이슈가 있는 경우 기능을 사용할 수 있게 되기 전에 앱이 생성되었기 때문일 수 있습니다(2019년 8월 1일). 새 함수 앱을 만들거나 `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>`을 실행하여 함수 앱을 업데이트해 보세요. 이 명령은 두 번 시도해야 성공할 수 있습니다.

#### <a name="remote-build-on-windows"></a>Windows의 원격 빌드

Windows에서 실행되는 모든 함수 앱에는 작은 관리 앱인 SCM(또는 [Kudu](https://github.com/projectkudu/kudu)) 사이트가 있습니다. 이 사이트는 Azure Functions에 대한 대부분의 배포 및 빌드 논리를 처리합니다.

앱을 Windows에 배포할 때 `dotnet restore`(C#) 또는 `npm install`(JavaScript)과 같은 언어별 명령이 실행됩니다.

#### <a name="remote-build-on-linux"></a>Linux의 원격 빌드

Linux에서 원격 빌드를 사용하도록 설정하려면 다음 [애플리케이션 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 지정해야 합니다.

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

기본적으로 [Azure Functions Core Tools](functions-run-local.md) 및 [AVisual Studio Code용 Azure Functions 확장](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)은 모두 Linux에 배포할 때 원격 빌드를 수행합니다. 이로 인해 두 도구 모두 Azure에서 이러한 설정을 자동으로 만듭니다.

앱이 Linux에서 원격으로 빌드되면 [배포 패키지에서 실행](run-functions-from-deployment-package.md)됩니다.

##### <a name="consumption-plan"></a>소비 계획

소비 플랜에서 실행되는 Linux 함수 앱은 배포 옵션을 제한하는 SCM/Kudu 사이트를 포함하지 않습니다. 그러나 소비 플랜에서 실행되는 Linux의 함수 앱은 원격 빌드를 지원합니다.

##### <a name="dedicated-and-premium-plans"></a>전용 및 프리미엄 플랜

[전용(App Service) 플랜](dedicated-plan.md) 및 [프리미엄 플랜](functions-premium-plan.md)에 따라 Linux에서 실행되는 함수 앱에도 제한된 SCM/Kudu 사이트가 있습니다.

## <a name="deployment-technology-details"></a>배포 기술 세부 정보

Azure Functions에서 사용할 수 있는 배포 방법은 다음과 같습니다.

### <a name="external-package-url"></a>외부 패키지 URL

외부 패키지 URL을 사용하여 함수 앱이 포함된 원격 패키지(.zip) 파일을 참조할 수 있습니다. 파일이 제공된 URL에서 다운로드되고 앱이 [패키지에서 실행](run-functions-from-deployment-package.md) 모드에서 실행됩니다.

>__사용 방법:__ [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package)를 애플리케이션 설정에 추가합니다. 이 설정의 값은 URL(실행하려는 특정 패키지 파일의 위치)이어야 합니다. [Portal에서](functions-how-to-use-azure-function-app-settings.md#settings) 또는 [Azure CLI를 사용하여](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 설정을 추가할 수 있습니다.
>
>Azure Blob Storage를 사용하는 경우 [SAS(공유 액세스 서명)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)에서 프라이빗 컨테이너를 사용하여 Functions에 패키지에 대한 액세스 권한을 부여합니다. 애플리케이션이 다시 시작될 때마다 콘텐츠의 복사본을 가져옵니다. 참조는 애플리케이션의 수명 동안 유효해야 합니다.

>__사용 시기:__ 외부 패키지 URL은 사용자가 [원격 빌드](#remote-build)를 원하지 않으려는 경우 소비 플랜에서 Linux에서 실행되는 Azure Functions에 대해 유일하게 지원되는 배포 방법입니다. 함수 앱이 참조하는 패키지 파일을 업데이트할 때 [수동으로 트리거를 동기화](#trigger-syncing)하여 애플리케이션이 변경되었음을 Azure에 알려야 합니다.

### <a name="zip-deploy"></a>Zip 배포

Zip 배포를 사용하여 함수 앱을 포함하는 .zip 파일을 Azure에 푸시합니다. 필요에 따라 [패키지에서 실행](run-functions-from-deployment-package.md)을 시작하도록 앱을 설정하거나 [원격 빌드](#remote-build)가 발생하도록 지정할 수 있습니다.

>__사용 방법:__ [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure) 중에서 즐겨 사용하는 클라이언트 도구를 사용하여 배포하거나 [Azure Functions Core Tools](functions-run-local.md#project-file-deployment)를 사용하여 명령줄에서 배포합니다. 기본적으로 이러한 도구는 zip 배포를 사용하고 [패키지에서 실행](run-functions-from-deployment-package.md)합니다. Core Tools와 Visual Studio Code 확장은 모두 Linux에 배포할 때 [원격 빌드](#remote-build)를 사용하도록 설정합니다. .zip 파일을 함수 앱에 수동으로 배포하려면 [.zip 파일이나 URL에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)의 지침을 따르세요.

>zip 배포를 사용하여 배포하는 경우 앱이 [패키지에서 실행](run-functions-from-deployment-package.md)되도록 설정할 수 있습니다. 패키지에서 실행 하려면 [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package 애플리케이션 설정 값을 `1`로 설정합니다. zip 배포를 권장합니다. 이 방식을 사용하면 애플리케이션에 대한 로드 시간이 빨라지며, 이것이 VS Code, Visual Studio 및 Azure CLI에 대한 기본값입니다.

>__사용 시기:__ zip 배포는 Azure Functions에 권장되는 배포 기술입니다.

### <a name="docker-container"></a>Docker 컨테이너

함수 앱을 포함 하는 Linux 컨테이너 이미지를 배포할 수 있습니다.

>__사용 방법:__ 프리미엄 또는 전용 플랜에서 Linux 함수 앱을 만들고 실행할 컨테이너 이미지를 지정합니다. 다음 두 가지 방법으로 수행할 수 있습니다.
>
>* Azure Portal의 Azure App Service 플랜에서 Linux 함수 앱을 만듭니다. **게시** 에서 **Docker 이미지** 를 선택하고 컨테이너를 구성합니다. 이미지가 호스트되는 위치를 입력합니다.
>* Azure CLI를 사용하여 App Service 플랜에서 Linux 함수 앱을 만듭니다. 방법을 알아보려면 [사용자 지정 이미지를 사용하여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)를 참조하세요.
>
>사용자 지정 컨테이너를 사용하여 기존 앱에 배포하려면 [Azure Functions Core Tools](functions-run-local.md)에서 [`func deploy`](functions-run-local.md#publish) 명령을 사용합니다.

>__사용 시기:__ 함수 앱이 실행되는 Linux 환경을 보다 강력하게 제어해야 하는 경우 Docker 컨테이너 옵션을 사용합니다. 이 배포 메커니즘은 Linux에서 실행되는 Functions에만 사용할 수 있습니다.

### <a name="web-deploy-msdeploy"></a>웹 배포(MSDeploy)

웹 배포 기능은 Azure의 Windows에서 실행되는 함수 앱을 포함하는 Windows 애플리케이션을 패키징한 후 모든 IIS 서버에 배포합니다.

>__사용 방법:__ [Azure Functions용 Visual Studio Tools](functions-create-your-first-function-visual-studio.md)를 사용합니다. **패키지 파일에서 실행(권장)** 확인란을 선택 취소합니다.
>
>[웹 배포 3.6](https://www.iis.net/downloads/microsoft/web-deploy)을 다운로드하고 `MSDeploy.exe`를 직접 호출할 수도 있습니다.

>__사용 시기:__ 웹 배포가 지원되며 이슈는 없지만 기본 메커니즘은 [패키지에서 실행이 사용하도록 설정된 zip 배포](#zip-deploy)입니다. 자세히 알아보려면 [Visual Studio 개발 가이드](functions-develop-vs.md#publish-to-azure)를 참조하세요.

### <a name="source-control"></a>원본 제어

소스 제어를 사용하여 함수 앱을 Git 리포지토리에 연결합니다. 해당 리포지토리에서 코드를 업데이트하면 배포가 트리거됩니다. 자세한 내용은 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)를 참조하세요.

>__사용 방법:__ 포털의 Functions 영역에 있는 배포 센터를 사용하여 소스 제어에서의 게시를 설정합니다. 자세한 내용은 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요.

>__사용 시기:__ 소스 제어를 사용하는 것은 해당 함수 앱에서 공동으로 작업하는 팀을 위한 모범 사례입니다. 소스 제어는 보다 정교한 배포 파이프라인을 가능하게 하는 적절한 배포 옵션입니다.

### <a name="local-git"></a>로컬 Git

로컬 Git을 사용하여 로컬 머신에서 Azure Functions로 코드를 푸시할 수 있습니다.

>__사용 방법:__ [Azure App Service에 로컬 Git 배포](../app-service/deploy-local-git.md)의 지침을 따릅니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 사용하는 것이 좋습니다. 로컬 Git에서 게시하는 경우 [수동으로 트리거를 동기화](#trigger-syncing)해야 합니다.

### <a name="cloud-sync"></a>클라우드 동기화

클라우드 동기화를 사용하여 Dropbox 및 OneDrive의 콘텐츠를 Azure Functions와 동기화합니다.

>__사용 방법:__ [클라우드 폴더에서 콘텐츠 동기화](../app-service/deploy-content-sync.md)의 지침을 따릅니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 권장합니다. 클라우드 동기화를 사용하여 게시하는 경우 [수동으로 트리거를 동기화](#trigger-syncing)해야 합니다.

### <a name="ftp"></a>FTP

FTP를 사용하여 파일을 Azure Functions로 직접 전송할 수 있습니다.

>__사용 방법:__ [FTP/s를 사용하여 콘텐츠 배포](../app-service/deploy-ftp.md)의 지침을 따릅니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 권장합니다. FTP를 사용하여 게시하는 경우 [수동으로 트리거를 동기화](#trigger-syncing)해야 합니다.

### <a name="portal-editing"></a>Portal 편집

Portal 기반 편집기에서 함수 앱에 있는 파일을 직접 편집할 수 있습니다(기본적으로 변경 내용을 저장할 때마다 배포).

>__사용 방법:__ Azure Portal에서 함수를 편집하려면 [포털에서 함수를 만들어야](./functions-get-started.md) 합니다. 단일 데이터 소스(single source of truth)를 보존하기 위해 다른 배포 방법을 사용하면 함수가 읽기 전용으로 설정되고 계속해서 포털을 편집할 수 없게 됩니다. Azure Portal에서 파일을 편집할 수 있는 상태로 돌아가려면 편집 모드를 수동으로 `Read/Write`로 다시 설정하고 배포 관련 애플리케이션 설정(예: [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package))을 제거할 수 있습니다.

>__사용 시기:__ Portal은 Azure Functions를 시작하는 좋은 방법입니다. 보다 집중적인 개발 작업을 수행하려면 다음 클라이언트 도구 중 하나를 사용하는 것이 좋습니다.
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools(명령줄)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

다음 표에서는 Portal 편집을 지원하는 운영 체제 및 언어를 보여 줍니다.

| 언어 | Windows 사용 | Windows 프리미엄 | Windows 전용 | Linux 사용 | Linux 프리미엄 | Linux 전용 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 스크립트 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript(Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python(미리 보기) | | | | | | |
| PowerShell(미리 보기) |✔|✔|✔| | | |
| TypeScript(Node.js) | | | | | | |

<sup>*</sup> Portal 편집은 프리미엄 및 전용 플랜을 사용하는 Linux의 Functions에 대한 HTTP 및 타이머 트리거에 대해서만 사용할 수 있습니다.

## <a name="deployment-behaviors"></a>배포 동작

배포를 수행하는 경우 모든 기존 실행이 완료되거나 시간 초과될 수 있습니다. 제한 시간이 지나면 새 코드가 로드되어 요청 처리를 시작합니다.

이 전환을 보다 강력하게 제어해야 하는 경우 배포 슬롯을 사용해야 합니다.

## <a name="deployment-slots"></a>배포 슬롯

함수 앱을 Azure에 배포하는 경우 프로덕션에 직접 배포하는 대신 별도의 배포 슬롯에 배포할 수 있습니다. 배포 슬롯에 대한 자세한 내용은 [Azure Functions 배포 슬롯](functions-deployment-slots.md) 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

함수 앱을 배포하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)
+ [Azure DevOps를 사용한 연속 업데이트](functions-how-to-azure-devops.md)
+ [Azure Functions에 대한 Zip 배포](deployment-zip-push.md)
+ [패키지에서 Azure Functions 실행](run-functions-from-deployment-package.md)
+ [Azure Functions의 함수 앱에 대한 리소스 배포 자동화](functions-infrastructure-as-code.md)
