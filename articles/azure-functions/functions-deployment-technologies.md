---
title: Azure 함수의 배포 기술
description: Azure Functions에 코드를 배포할 수 있는 다양한 방법을 알아봅니다.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277129"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure 함수의 배포 기술

몇 가지 다른 기술을 사용하여 Azure Functions 프로젝트 코드를 Azure에 배포할 수 있습니다. 이 문서에서는 이러한 기술에 대한 전체 목록을 제공하고, 함수의 맛을 사용할 수 있는 기술을 설명하고, 각 방법을 사용할 때 발생하는 사항을 설명하고, 다양한 시나리오에서 사용할 수 있는 최상의 방법에 대한 권장 사항을 제공합니다. . Azure Functions에 배포를 지원하는 다양한 도구는 컨텍스트에 따라 올바른 기술에 맞게 조정됩니다. 일반적으로 zip 배포는 Azure Functions에 권장되는 배포 기술입니다.

## <a name="deployment-technology-availability"></a>배포 기술 가용성

Azure Functions는 Windows 및 Linux에서 플랫폼 간 로컬 개발 및 호스팅을 지원합니다. 현재 세 가지 호스팅 계획을 사용할 수 있습니다.

+ [소비](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [전용 (앱 서비스)](functions-scale.md#app-service-plan)

각 계획에는 다른 동작이 있습니다. Azure Functions의 각 풍미에 대해 모든 배포 기술을 사용할 수 있는 것은 아닙니다. 다음 차트는 운영 체제및 호스팅 계획의 각 조합에 대해 지원되는 배포 기술을 보여 주며 있습니다.

| 배포 기술 | 윈도우 소비 | 윈도우 프리미엄 | 윈도우 전용  | 리눅스 소비 | 리눅스 프리미엄 | 리눅스 전용 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 외부 패키지 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| 우편 배포 |✔|✔|✔|✔|✔|✔|
| Docker 컨테이너 | | | | |✔|✔|
| 웹 배포 |✔|✔|✔| | | |
| 원본 제어 |✔|✔|✔| |✔|✔|
| 로컬 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 클라우드 동기화<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| 포털 편집 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> [수동 트리거 동기화가](#trigger-syncing)필요한 배포 기술 .  
<sup>2</sup> 포털 편집은 프리미엄 및 전용 계획을 사용하여 리눅스에서 기능에 대한 HTTP 및 타이머 트리거에 대해서만 사용할 수 있습니다.

## <a name="key-concepts"></a>주요 개념

일부 주요 개념은 Azure Functions에서 배포가 작동하는 방식을 이해하는 데 중요합니다.

### <a name="trigger-syncing"></a>트리거 동기화

트리거를 변경할 때 Functions 인프라는 변경 사항을 알고 있어야 합니다. 동기화는 많은 배포 기술에서 자동으로 수행됩니다. 그러나 경우에 따라 트리거를 수동으로 동기화해야 합니다. 외부 패키지 URL, 로컬 Git, 클라우드 동기화 또는 FTP를 참조하여 업데이트를 배포할 때 트리거를 수동으로 동기화해야 합니다. 다음 세 가지 방법 중 하나로 트리거를 동기화할 수 있습니다.

* Azure 포털에서 함수 앱 다시 시작
* [마스터 키를](functions-bindings-http-webhook-trigger.md#authorization-keys)사용 `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` 하 여 HTTP POST 요청을 보냅니다.
* `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`로 HTTP POST 요청을 보냅니다. 자리 표시자를 구독 ID, 리소스 그룹 이름 및 함수 앱의 이름으로 바꿉니다.

### <a name="remote-build"></a>원격 빌드

Azure Functions는 zip 배포 후 수신하는 코드에 대한 빌드를 자동으로 수행할 수 있습니다. 이러한 빌드는 앱이 Windows 또는 Linux에서 실행되고 있는지 여부에 따라 약간 다르게 실행됩니다. 앱이 이전에 [패키지에서 실행](run-functions-from-deployment-package.md) 모드에서 실행되도록 설정된 경우 원격 빌드가 수행되지 않습니다. 원격 빌드를 사용하는 방법을 알아보려면 [배포를 압축하기](#zip-deploy)위해 이동합니다.

> [!NOTE]
> 원격 빌드에 문제가 있는 경우 기능을 사용할 수 있게 되기 전에 앱이 만들어졌기 때문일 수 있습니다(2019년 8월 1일). 새 함수 앱을 만들거나 `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` 함수 앱을 업데이트하기 위해 실행해 보십시오. 이 명령은 두 번의 시도가 성공할 수 있습니다.

#### <a name="remote-build-on-windows"></a>Windows에서 원격 빌드

Windows에서 실행되는 모든 기능 앱에는 작은 관리 앱인 SCM(또는 [Kudu)](https://github.com/projectkudu/kudu)사이트가 있습니다. 이 사이트는 Azure Functions에 대한 배포 및 빌드 논리의 대부분을 처리합니다.

앱이 Windows에 배포되면 (C#) 또는 `dotnet restore` `npm install` (자바스크립트)와 같은 언어별 명령이 실행됩니다.

#### <a name="remote-build-on-linux"></a>리눅스에 원격 빌드

Linux에서 원격 빌드를 사용하려면 다음 [응용 프로그램 설정을](functions-how-to-use-azure-function-app-settings.md#settings) 설정해야 합니다.

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

기본적으로 Azure [Functions 핵심 도구와](functions-run-local.md) [Visual Studio 코드용 Azure Functions 확장은](functions-create-first-function-vs-code.md#publish-the-project-to-azure) Linux에 배포할 때 원격 빌드를 수행합니다. 따라서 두 도구 모두 Azure에서 이러한 설정을 자동으로 만듭니다. 

Linux에서 원격으로 앱을 빌드하면 [배포 패키지에서 실행됩니다.](run-functions-from-deployment-package.md) 

##### <a name="consumption-plan"></a>소비 계획

소비 계획에서 실행 중인 Linux 기능 앱에는 배포 옵션을 제한하는 SCM/Kudu 사이트가 없습니다. 그러나 소비 계획에서 실행 중인 Linux의 기능 앱은 원격 빌드를 지원합니다.

##### <a name="dedicated-and-premium-plans"></a>전용 및 프리미엄 플랜

[전용 (앱 서비스) 계획및](functions-scale.md#app-service-plan) [프리미엄 요금제에서](functions-scale.md#premium-plan) Linux에서 실행되는 기능 앱에는 제한된 SCM/Kudu 사이트도 있습니다.

## <a name="deployment-technology-details"></a>배포 기술 세부 정보

다음 배포 방법은 Azure Functions에서 사용할 수 있습니다.

### <a name="external-package-url"></a>외부 패키지 URL

외부 패키지 URL을 사용하여 함수 앱이 포함된 원격 패키지(.zip) 파일을 참조할 수 있습니다. 파일은 제공된 URL에서 다운로드되고 앱은 [패키지에서 실행](run-functions-from-deployment-package.md) 모드에서 실행됩니다.

>__그것을 사용하는 방법 :__ 응용 `WEBSITE_RUN_FROM_PACKAGE` 프로그램 설정에 추가합니다. 이 설정의 값은 URL(실행하려는 특정 패키지 파일의 위치)이어야 합니다. [포털에서](functions-how-to-use-azure-function-app-settings.md#settings) 또는 [Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)를 사용하여 설정을 추가할 수 있습니다. 
>
>Azure Blob 저장소를 사용하는 경우 [SAS(공유 액세스 서명이](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 있는 개인 컨테이너)를 사용하여 함수에 패키지에 대한 액세스 권한을 부여합니다. 응용 프로그램이 다시 시작될 때마다 콘텐츠의 복사본을 가져옵니다. 참조는 응용 프로그램의 수명 동안 유효해야 합니다.

>__사용 시기:__ 외부 패키지 URL은 사용자가 [원격 빌드를](#remote-build) 발생하지 않으려는 경우 사용량 계획에서 Linux에서 실행되는 Azure Functions에 대해 지원되는 유일한 배포 방법입니다. 함수 앱이 참조하는 패키지 파일을 업데이트할 때 [트리거를 수동으로 동기화하여](#trigger-syncing) 응용 프로그램이 변경되었음을 Azure에 알려야 합니다.

### <a name="zip-deploy"></a>우편 배포

zip 배포를 사용하여 함수 앱이 포함된 .zip 파일을 Azure로 푸시합니다. 선택적으로 [앱이 패키지에서 실행을](run-functions-from-deployment-package.md)시작하도록 설정하거나 [원격 빌드가](#remote-build) 발생하도록 지정할 수 있습니다.

>__그것을 사용하는 방법 :__ 즐겨 찾는 클라이언트 도구: [Visual Studio 코드,](functions-develop-vs-code.md#publish-to-azure) [Visual Studio](functions-develop-vs.md#publish-to-azure)또는 [Azure Functions 핵심 도구를](functions-run-local.md#project-file-deployment)사용하여 명령줄에서 배포합니다. 기본적으로 이러한 도구는 zip 배포를 사용하고 [패키지에서 실행됩니다.](run-functions-from-deployment-package.md) 핵심 도구와 비주얼 스튜디오 코드 확장 모두 Linux에 배포할 때 [원격 빌드를](#remote-build) 사용할 수 있습니다. 함수 앱에 .zip 파일을 수동으로 배포하려면 [.zip 파일 또는 URL에서 배포의](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)지침을 따릅니다.

>zip 배포를 사용하여 배포할 때 [패키지에서 실행되도록](run-functions-from-deployment-package.md)앱을 설정할 수 있습니다. 패키지에서 실행하려면 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정 `1`값을 로 설정합니다. zip 배포를 권장합니다. 응용 프로그램에 대한 로드 시간이 빨라지며 VS 코드, Visual Studio 및 Azure CLI의 기본값입니다. 

>__사용 시기:__ Zip 배포는 Azure Functions에 권장되는 배포 기술입니다.

### <a name="docker-container"></a>Docker 컨테이너

함수 앱이 포함된 Linux 컨테이너 이미지를 배포할 수 있습니다.

>__그것을 사용하는 방법 :__ 프리미엄 또는 전용 계획에서 Linux 함수 앱을 만들고 실행할 컨테이너 이미지를 지정합니다. 다음 두 가지 방법으로 수행할 수 있습니다.
>
>* Azure 포털에서 Azure 앱 서비스 계획에서 Linux 함수 앱을 만듭니다. **게시의**경우 **Docker 이미지를**선택한 다음 컨테이너를 구성합니다. 이미지가 호스팅되는 위치를 입력합니다.
>* Azure CLI를 사용하여 앱 서비스 계획에서 Linux 함수 앱을 만듭니다. 방법을 알아보려면 [사용자 지정 이미지를 사용하여 Linux에서 함수 만들기를](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)참조하십시오.
>
>[Azure Functions 핵심 도구에서](functions-run-local.md)사용자 지정 컨테이너를 사용하여 기존 [`func deploy`](functions-run-local.md#publish) 앱에 배포하려면 명령을 사용합니다.

>__사용 시기:__ 함수 앱이 실행되는 Linux 환경을 보다 세한 제어가 필요한 경우 Docker 컨테이너 옵션을 사용합니다. 이 배포 메커니즘은 Linux에서 실행 중인 함수에만 사용할 수 있습니다.

### <a name="web-deploy-msdeploy"></a>웹 배포(MSDeploy)

Web Deploy 패키지 및 Azure에서 Windows에서 실행 중인 기능 앱을 포함하여 모든 IIS 서버에 Windows 응용 프로그램을 배포합니다.

>__그것을 사용하는 방법 :__ [Azure 함수에 대한 시각적 스튜디오 도구를](functions-create-your-first-function-visual-studio.md)사용합니다. 패키지 **파일에서 실행(권장) 확인란을** 선택 취소합니다.
>
>웹 배포 [3.6을](https://www.iis.net/downloads/microsoft/web-deploy) 다운로드하고 `MSDeploy.exe` 직접 호출할 수도 있습니다.

>__사용 시기:__ 웹 배포가 지원되며 문제가 없지만 기본 설정 메커니즘은 [패키지 실행이 활성화된 zip 배포입니다.](#zip-deploy) 자세한 내용은 Visual [Studio 개발 가이드를](functions-develop-vs.md#publish-to-azure)참조하십시오.

### <a name="source-control"></a>원본 제어

소스 제어를 사용하여 함수 앱을 Git 리포지토리에 연결합니다. 해당 리포지토리의 코드 업데이트는 배포를 트리거합니다. 자세한 내용은 [쿠두 위키를](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)참조하십시오.

>__그것을 사용하는 방법 :__ 포털의 기능 영역에서 배포 센터를 사용하여 소스 제어에서 게시를 설정합니다. 자세한 내용은 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요.

>__사용 시기:__ 소스 제어를 사용하는 것은 해당 기능 앱에서 공동 작업하는 팀의 모범 사례입니다. 소스 제어는 보다 정교한 배포 파이프라인을 가능하게 하는 좋은 배포 옵션입니다.

### <a name="local-git"></a>로컬 Git

로컬 Git을 사용하여 Git을 사용하여 로컬 컴퓨터에서 Azure Functions로 코드를 푸시할 수 있습니다.

>__그것을 사용하는 방법 :__ Azure 앱 서비스에 로컬 [Git 배포의 지침을 따릅니다.](../app-service/deploy-local-git.md)

>__사용 시기:__ 일반적으로 다른 배포 방법을 사용하는 것이 좋습니다. 로컬 Git에서 게시할 때 [트리거를 수동으로 동기화해야](#trigger-syncing)합니다.

### <a name="cloud-sync"></a>클라우드 동기화

클라우드 동기화를 사용하여 Dropbox 및 OneDrive에서 Azure 기능으로 콘텐츠를 동기화합니다.

>__그것을 사용하는 방법 :__ [클라우드 폴더에서 콘텐츠 동기화의 지침을 따릅니다.](../app-service/deploy-content-sync.md)

>__사용 시기:__ 일반적으로 다른 배포 방법을 권장합니다. 클라우드 동기화를 사용하여 게시할 때 [트리거를 수동으로 동기화해야](#trigger-syncing)합니다.

### <a name="ftp"></a>FTP

FTP를 사용하여 파일을 Azure 함수로 직접 전송할 수 있습니다.

>__그것을 사용하는 방법 :__ [FTP/s를 사용하여 콘텐츠 배포의](../app-service/deploy-ftp.md)지침을 따릅니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 권장합니다. FTP를 사용하여 게시할 때 [트리거를 수동으로 동기화해야](#trigger-syncing)합니다.

### <a name="portal-editing"></a>포털 편집

포털 기반 편집기에서 함수 앱에 있는 파일을 직접 편집할 수 있습니다(기본적으로 변경 내용을 저장할 때마다 배포).

>__그것을 사용하는 방법 :__ Azure 포털에서 함수를 편집하려면 [포털에서 함수를 만들어야](functions-create-first-azure-function.md)합니다. 단일 원본을 보존하기 위해 다른 배포 방법을 사용하면 함수를 읽기 전용으로 만들고 포털 편집이 계속되는 것을 방지할 수 있습니다. Azure 포털에서 파일을 편집할 수 있는 상태로 돌아가려면 편집 모드를 `Read/Write` 수동으로 다시 설정하고 배포 관련 응용 프로그램 설정(예:)을 `WEBSITE_RUN_FROM_PACKAGE`제거할 수 있습니다. 

>__사용 시기:__ 포털은 Azure 함수를 시작하는 좋은 방법입니다. 보다 집중적인 개발 작업을 위해 다음 클라이언트 도구 중 하나를 사용하는 것이 좋습니다.
>
>* [비주얼 스튜디오 코드](functions-create-first-function-vs-code.md)
>* [Azure 함수 핵심 도구(명령줄)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

다음 표에서는 포털 편집을 지원하는 운영 체제 및 언어를 보여 주며 있습니다.

| | 윈도우 소비 | 윈도우 프리미엄 | 윈도우 전용 | 리눅스 소비 | 리눅스 프리미엄 | 리눅스 전용 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 스크립트 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript(Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python(미리 보기) | | | | | | |
| PowerShell(미리 보기) |✔|✔|✔| | | |
| 타이핑 스크립트(노드.js) | | | | | | |

<sup>*</sup>포털 편집은 프리미엄 및 전용 계획을 사용하여 리눅스에서 기능에 대한 HTTP 및 타이머 트리거에 대해서만 사용할 수 있습니다.

## <a name="deployment-slots"></a>배포 슬롯

Azure에 함수 앱을 배포할 때 프로덕션에 직접 배포하는 대신 별도의 배포 슬롯에 배포할 수 있습니다. 배포 슬롯에 대한 자세한 내용은 [Azure Functions 배포 슬롯](../app-service/deploy-staging-slots.md) 설명서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

함수 앱 배포에 대해 자세히 알아보려면 다음 문서를 읽어보십시오. 

+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)
+ [Azure DevOps를 사용하여 지속적인 배달](functions-how-to-azure-devops.md)
+ [Azure 기능에 대한 Zip 배포](deployment-zip-push.md)
+ [패키지에서 Azure Functions 실행](run-functions-from-deployment-package.md)
+ [Azure Functions에서 함수 앱에 대한 리소스 배포 자동화](functions-infrastructure-as-code.md)
