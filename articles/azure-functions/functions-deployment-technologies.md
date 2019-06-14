---
title: Azure Functions의 배포 기술 | Microsoft Docs
description: Azure Functions에 코드를 배포할 수 있습니다는 다양 한 방법의 장단점에 알아봅니다.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 689e54877a5b0a405fe7b3e3981d98ba6b42edb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062947"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions의 배포 기술

몇 가지 서로 다른 기술을 사용 하 여 Azure 함수 프로젝트 코드를 Azure에 배포할 수 있습니다. 이 문서에서는 이러한 기술 중 목록은 제공 함수는 버전에 사용할 수 있는 기술을 알리는, 각 메서드를 사용 및 사용에 대 한 최상의 방법에 대 한 권장 사항을 제공 하는 경우 다루려는 내용에 대해 설명 다양 한 시나리오입니다. Azure Functions에 배포를 지 원하는 다양 한 도구는 컨텍스트에 따라 적합 한 기술에 조정 됩니다.

## <a name="deployment-technology-availability"></a>배포 기술 가용성

> [!IMPORTANT]
> Azure Functions 지원 플랫폼 로컬 개발 및 두 개의 운영 체제에서 호스트 간: Windows 및 Linux입니다. 세 가지 호스팅 계획은 현재 사용할 수 있는 다양 한 동작-를 사용 하 여 각 [소비](functions-scale.md#consumption-plan)를 [Premium](functions-scale.md#premium-plan), 및 [(App Service)를 전용](functions-scale.md#app-service-plan)합니다. 일부 배포 기술은 Azure Functions의 각 버전에 대해 사용할 수 있습니다.

| 배포 기술 | Windows 사용 | Windows 프리미엄 (미리 보기) | Windows 전용  | Linux 소비 (미리 보기) | Linux 전용 |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| 외부 패키지 URL<sup>1</sup> |✔|✔|✔|✔|✔|
| Zip 배포 |✔|✔|✔| |✔|
| Docker 컨테이너 | | | | |✔|
| 웹 배포 |✔|✔|✔| | |
| 소스 제어 |✔|✔|✔| |✔|
| 로컬 Git<sup>1</sup> |✔|✔|✔| |✔|
| Cloud sync<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| 포털 편집 |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> 필요한 배포 기술을 [수동 트리거 동기화](#trigger-syncing)합니다.
<sup>2</sup> 포털 편집 전용 계획을 사용 하 여 Linux에서 함수에 대 한 HTTP 및 타이머 트리거에 대 한 사용 됩니다.

## <a name="key-concepts"></a>주요 개념

계속 하기 전에 반드시 될 Azure Functions에서 배포가 작동 하는 방법을 이해 하는 데 중요 한 몇 가지 주요 개념에 알아봅니다.

### <a name="trigger-syncing"></a>트리거 동기화

사용자 트리거를 변경 하면 함수 인프라에서는 이러한 변경 사항을 인식 하도록 해야 합니다. 이 동기화는 여러 배포 기술에 대 한 자동으로 발생합니다. 그러나 일부 경우에서에 트리거 수동으로 동기화 해야 합니다. 외부 패키지 URL 내용, 로컬 Git, 클라우드 동기화 또는 FTP를 사용 하 여 업데이트를 배포 하면 사용자 트리거를 수동으로 동기화 하도록 해야 합니다. 세 가지 방법 중 하나에서 트리거를 동기화 할 수 있습니다.

* Azure portal에서 함수 앱을 다시 시작
* HTTP POST 요청을 보내지 `https://www.{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` 를 사용 하 여 합니다 [마스터 키](functions-bindings-http-webhook.md#authorization-keys)합니다.
* HTTP POST 요청을 보낼 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`합니다. 자리 표시자를 함수 앱의 이름과 구독 ID, 리소스 그룹 이름으로 바꿉니다.

## <a name="deployment-technology-details"></a>배포 기술 세부 정보  

### <a name="external-package-url"></a>외부 패키지 URL

__용도:__ 함수 앱을 포함 하는 원격 패키지 (.zip) 파일을 참조할 수 있습니다. 제공 된 URL에서 파일 다운로드 되 고 앱에서 실행 [패키지에서 실행](run-functions-from-deployment-package.md) 모드입니다.

__사용 방법:__ 추가 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정에 있습니다. 이 설정의 값을 URL-실행 하려면 특정 패키지 파일의 위치 해야 합니다. 설정을 추가할 수 있습니다 하거나 [포털에서](functions-how-to-use-azure-function-app-settings.md#settings) 하거나 [Azure CLI를 사용 하 여](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)입니다. Azure blob 저장소를 사용 하는 경우 사용 하 여 개인 컨테이너를 사용 해야는 [공유 액세스 서명 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) 패키지 함수 액세스 권한을 부여 합니다. 언제 든 지 응용 프로그램이 다시 시작 되어야 함을 의미 참조용 유효한 응용 프로그램의 수명 동안 콘텐츠의 복사본을 인출 됩니다 것입니다.

__사용 하는 경우:__ 이것이 Linux (미리 보기) 소비 계획에서 실행 되는 Azure Functions에 대 한 지원 되는 전용 배포 방법입니다. 함수 앱을 참조 하는 패키지 파일을 업데이트할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing) 하기가 Azure 응용 프로그램 변경 되었습니다.

### <a name="zip-deploy"></a>Zip 배포

__용도:__ Azure 함수 앱을 포함 한 zip 파일을 푸시할 수 있습니다. 선택적으로 지정할 수도 있습니다 시작 앱 [패키지에서 실행](run-functions-from-deployment-package.md) 모드입니다.

__사용 방법:__ 즐겨 사용 하 여 배포 클라이언트 도구- [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)하십시오 [Visual Studio](functions-develop-vs.md#publish-to-azure), 또는 [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)합니다. Zip 파일을 함수 앱을 수동으로 배포 하려면의 지침을 따릅니다 [zip 파일이 나 url에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)합니다.

또한 배포 zip를 통해 배포 하는 경우 사용자 지정할 수에서 해당 앱을 실행 하려면 [패키지에서 실행](run-functions-from-deployment-package.md) 설정 하 여 모드를 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정 값으로 `1`. 이 옵션을 권장 하 고 응용 프로그램에 대 한 빠른 로드 시간을 생성 합니다. 위의 클라이언트 도구에 대해 기본적으로이 이루어집니다.

__사용 하는 경우:__ 이 Windows를 실행 하는 Azure Functions 및 Dedicated 요금제에서 Linux를 실행 하는 Azure Functions에 대 한 권장 되는 배포 기술입니다.

### <a name="docker-container"></a>Docker 컨테이너

__용도:__ Azure Functions를 끌어와 실행 하는 컨테이너 이미지를 지정 합니다.

__사용 방법:__ Dedicated 요금제에서 Linux 함수 앱을 만들고에서 실행 하는 컨테이너 이미지를 지정 합니다. 다음 두 가지 방법으로 수행할 수 있습니다.

* Azure portal에서 App Service 계획에서 Linux 함수 앱을 만듭니다. 선택 **Docker 이미지** 에 대 한 **게시**, 구성 컨테이너 이미지가 호스팅되는 위치를 제공 합니다.
* Azure CLI를 통해 App Service 계획에서 Linux 함수 앱을 만듭니다. 에 대해 알아봅니다 검토 하 여는 방법 [사용자 지정 이미지를 사용 하 여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)합니다.

에 사용자 지정 컨테이너를 사용 하 여 기존 앱을 배포 하려면 사용 합니다 [ `func deploy` ](functions-run-local.md#publish) 명령을 합니다 [Azure Functions 핵심 도구](functions-run-local.md)합니다.

__사용 하는 경우:__ 이 옵션을 사용 Linux 환경을 통해 더 많이 제어 해야 하는 경우 함수 앱이 실행 됩니다. 이 배포 메커니즘에만 Linux App Service 계획에서 실행 되는 함수에 대 한 제공 됩니다.

### <a name="web-deploy-msdeploy"></a>웹 배포 (MSDeploy)

__용도:__ 패키지 및 Windows를 실행 하 여 Azure 함수 앱을 비롯 한 모든 IIS 서버에 Windows 응용 프로그램을 배포 합니다.

__사용 방법:__ 사용 합니다 [Azure Functions 용 Visual Studio tools](functions-create-your-first-function-visual-studio.md), 눈금 없고는 `Run from package file (recommended)` 확인란 합니다.

또는 호출 `MSDeploy.exe` 다운로드 한 후 직접 [웹 배포 3.6](https://www.iis.net/downloads/microsoft/web-deploy)합니다.

__사용 하는 경우:__ 이 배포 기술을 사용할 수 있으며 아무 문제가 이지만 기본 메커니즘은 이제 [Zip 패키지와 함께 배포 실행에서 사용 하도록 설정](#zip-deploy)합니다. 자세한 내용은 참조는 [Visual Studio 개발 가이드](functions-develop-vs.md#publish-to-azure)합니다.

### <a name="source-control"></a>소스 제어

__용도:__ 해당 리포지토리의 코드에 대 한 업데이트 배포를 트리거하는 git 리포지토리까지 함수 앱을 연결할 수 있습니다. 자세한 내용은 잠시 살펴 합니다 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)합니다.

__사용 방법:__ 원본 제어에서 게시를 설정 하는 Azure Functions 포털에서 배포 센터를 사용 합니다. 자세한 내용은 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요.

__사용 하는 경우:__ 소스 제어를 사용 하 여 팀 공동 작업 하는 함수 앱에 대 한 모범 사례 이며이 더 복잡 한 배포 파이프라인을 사용 하도록 설정 하는 유용한 옵션입니다.

### <a name="local-git"></a>로컬 git

__용도:__ 있습니다 Git를 사용 하 여 Azure Functions를 로컬 컴퓨터에서 코드를 푸시합니다.

__사용 방법:__ 지침을 따르세요 [Azure App Service에 로컬 Git 배포](../app-service/deploy-local-git.md)합니다.

__사용 하는 경우:__ 일반적으로 다른 배포 방법이 권장 됩니다. 로컬 git에서 게시할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing)합니다.

### <a name="cloud-sync"></a>클라우드 동기화

__용도:__ Azure Functions에 Dropbox 및 OneDrive에서 콘텐츠를 동기화 할 수 있습니다.

__사용 방법:__ 지침을 따릅니다 [클라우드 폴더에서 콘텐츠 동기화](../app-service/deploy-content-sync.md)합니다.

__사용 하는 경우:__ 일반적으로 다른 배포 방법이 권장 됩니다. 클라우드 동기화를 사용 하 여 게시할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing)합니다.

### <a name="ftp"></a>FTP

__용도:__ 사용 하면 직접 Azure Functions로 전송 파일.

__사용 방법:__ 지침을 따릅니다 [FTP/s를 사용 하 여 콘텐츠를 배포](../app-service/deploy-ftp.md)합니다.

__사용 하는 경우:__ 일반적으로 다른 배포 방법이 권장 됩니다. FTP를 사용 하 여 게시할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing)합니다.

### <a name="portal-editing"></a>포털 편집

__용도:__ 함수 앱에 파일을 직접 편집 하면 포털 기반 편집기를 사용 하 여 (기본적으로 클릭 하면 언제 든 지 배포 **저장할**).

__사용 방법:__ Azure portal에서 함수를 편집할 수 있어야 [포털에서 함수를 만든](functions-create-first-azure-function.md)합니다. 다른 배포 메서드를 사용 하 여 읽기 전용 함수를 사용 하면 편집할 수 없도록 합니다 계속 포털, 정보의 단일 원본을 유지 합니다. Azure portal을 사용 하 여 파일을 편집할 수 있는 상태로 반환할 수 있습니다을 수동으로 설정 편집 모드를 다시 `Read/Write` 모든 응용 프로그램 배포와 관련 된 설정을 제거 하 고 (같은 `WEBSITE_RUN_FROM_PACKAGE`).

#### <a name="portal-editing-availability"></a>가용성을 편집 하는 포털

| | Windows Consumption | Windows 프리미엄 (미리 보기) | Windows 전용 | Linux 소비 (미리 보기) | Linux 전용 |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| JavaScript(Node.js) |✔|✔|✔| |✔<sup>1</sup>|
| TypeScript (Node.js) | | | | | |
| C# | | | | | |
| C# 스크립트 |✔|✔|✔| |✔<sup>1</sup>|
| F# | | | | | |
| Java | | | | | |
| Python(미리 보기) | | | | | |
| PowerShell (미리 보기) |✔|✔|✔| | |

<sup>1</sup> 포털 편집 전용 계획을 사용 하 여 Linux에서 함수에 대 한 HTTP 및 타이머 트리거에 대 한 사용 됩니다.

__사용 하는 경우:__ 포털 Azure Functions를 사용 하 여 시작 하는 훌륭한 방법 이지만 도구 클라이언트를 사용 하 여 더 강 할수록 개발 작업에 대 한 것이 좋습니다.

* [VS Code를 사용 하 여 시작](functions-create-first-function-vs-code.md)
* [Azure Functions 핵심 도구를 사용 하 여 시작](functions-run-local.md)
* [Visual Studio를 사용 하 여 시작](functions-create-your-first-function-visual-studio.md)

## <a name="other-relevant-information"></a>기타 관련 정보

### <a name="deployment-slots"></a>배포 슬롯

Azure 함수 앱을 배포할 때 프로덕션에 직접 대신 개별 배포 슬롯에 배포할 수 있습니다. 배포 슬롯에 대 한 자세한 내용은 참조 하세요. [Azure App Service 슬롯 설명서](../app-service/deploy-staging-slots.md)합니다.

#### <a name="deployment-slots-levels-of-support"></a>배포 슬롯 수준의 지원

지원의 두 가지 수준이 있습니다.

* _일반 공급(GA)_ - 완전하게 지원되며 프로덕션용으로 승인되었습니다.
* _미리 보기_ - 아직 지원되지 않지만 향후 GA 상태가 될 예정입니다.

| OS/호스팅 계획 | 지원 수준 |
| --------------- | ------ |
| Windows Consumption | 미리 보기 |
| Windows 프리미엄 (미리 보기) | 미리 보기 |
| Windows 전용 | 일반 공급 |
| Linux 사용 | 지원되지 않음 |
| Linux 전용 | 일반 공급 |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대 한 연속 배포](functions-continuous-deployment.md)
> [Azure DevOps를 사용한 연속 배달](functions-how-to-azure-devops.md)
> [Azure Functions에 대 한 배포를 Zip](deployment-zip-push.md) 
>  [패키지 파일에서 Azure Functions 실행](run-functions-from-deployment-package.md)
> [Azure Functions에서 함수 앱에 대 한 리소스 배포 자동화](functions-infrastructure-as-code.md)
