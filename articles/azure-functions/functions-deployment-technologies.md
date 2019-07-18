---
title: Azure Functions의 배포 기술 | Microsoft Docs
description: Azure Functions에 코드를 배포할 수 있습니다 하는 다른 방법을 알아봅니다.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594389"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions의 배포 기술

Azure 함수 프로젝트 코드를 Azure에 배포 하는 몇 가지 다른 기술을 사용할 수 있습니다. 이 문서에서는 이러한 기술의 전체 목록, 함수는 버전에 사용할 수 있는 기술을 설명, 각 메서드를 사용 하는 경우 어떻게 처리 되는지를 설명 및 다양 한 시나리오에서 사용 하 여에 대 한 최상의 방법에 대 한 권장 사항을 제공 합니다. . Azure Functions에 배포를 지 원하는 다양 한 도구는 컨텍스트에 따라 적합 한 기술에 조정 됩니다.

## <a name="deployment-technology-availability"></a>배포 기술 가용성

> [!IMPORTANT]
> Azure Functions에는 플랫폼 간 로컬 개발 및 호스팅 Windows 및 Linux에서 지원 합니다. 현재 세 가지 호스팅 계획을 사용할 수 있습니다. [소비](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), 및 [(Azure App Service)를 전용](functions-scale.md#app-service-plan)합니다. 각 계획의 동작이 다릅니다. 일부 배포 기술은 Azure Functions의 각 버전에 대해 사용할 수 있습니다.

| 배포 기술 | Windows Consumption | Windows 프리미엄 (미리 보기) | Windows 전용  | Linux 소비 (미리 보기) | Linux 전용 |
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
<sup>2</sup> 포털 편집 Dedicated 요금제를 사용 하 여 Linux에서 함수에 대 한 HTTP 및 타이머 트리거에 대해서만 사용 됩니다.

## <a name="key-concepts"></a>주요 개념

몇 가지 주요 개념은 Azure Functions에서 배포가 작동 하는 방법을 이해 하는 데 중요 합니다.

### <a name="trigger-syncing"></a>트리거 동기화

사용자 트리거를 변경 하면 함수 인프라 변경 내용을 인식 해야 합니다. 많은 배포 기술에 대 한 자동으로 동기화 합니다. 그러나 경우에 따라 트리거에 수동으로 동기화 해야 합니다. 외부 패키지 URL 내용, 로컬 Git, 클라우드 동기화 또는 FTP를 참조 하 여 업데이트를 배포할 때에 트리거를 수동으로 동기화 해야 합니다. 세 가지 방법 중 하나에서 트리거를 동기화 할 수 있습니다.

* Azure portal에서 함수 앱을 다시 시작
* HTTP POST 요청을 보내지 `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` 를 사용 하 여 합니다 [마스터 키](functions-bindings-http-webhook.md#authorization-keys)합니다.
* HTTP POST 요청을 보낼 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`합니다. 자리 표시자를 함수 앱의 이름과 구독 ID, 리소스 그룹 이름으로 바꿉니다.

## <a name="deployment-technology-details"></a>배포 기술 세부 정보 

다음 배포 방법은 Azure Functions에서 사용할 수 있습니다.

### <a name="external-package-url"></a>외부 패키지 URL

함수 앱을 포함 하는 원격 패키지 (.zip) 파일을 참조 하는 외부 패키지 URL을 사용할 수 있습니다. 제공 된 URL에서 파일 다운로드 되 고 앱에서 실행 [패키지에서 실행](run-functions-from-deployment-package.md) 모드입니다.

>__사용 방법:__ 추가 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정에 있습니다. 이 설정의 값 (실행 하려는 특정 패키지 파일의 위치) URL 이어야 합니다. 설정을 추가할 수 있습니다 하거나 [포털에서](functions-how-to-use-azure-function-app-settings.md#settings) 하거나 [Azure CLI를 사용 하 여](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)입니다. 
>
>사용 하 여 개인 컨테이너를 사용 하 여 Azure Blob storage를 사용 하는 경우는 [공유 액세스 서명 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 패키지 함수 액세스 권한을 부여 합니다. 언제 든 지 응용 프로그램이 다시 시작, 콘텐츠 복사본을 가져와 해당 합니다. 참조 응용 프로그램의 수명 동안 유효 해야 합니다.

>__사용 하는 경우:__ 외부 패키지 URL은 Azure Functions 소비 계획 (미리 보기)에 Linux에서 실행 되는 지원 되는 배포 방법입니다. 함수 앱을 참조 하는 패키지 파일을 업데이트 하는 경우 해야 [트리거를 수동으로 동기화](#trigger-syncing) 하기가 Azure 응용 프로그램 변경 되었습니다.

### <a name="zip-deploy"></a>Zip 배포

Azure 함수 앱이 포함 된.zip 파일을 푸시를 사용 하 여 zip 배포 합니다. 필요에 따라 앱 시작을 설정할 수 있습니다 [패키지에서 실행](run-functions-from-deployment-package.md) 모드입니다.

>__사용 방법:__ 즐겨 찾는 클라이언트 도구를 사용 하 여 배포 합니다. [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)하십시오 [Visual Studio](functions-develop-vs.md#publish-to-azure), 또는 [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)합니다. .Zip 파일을 함수 앱을 수동으로 배포 하려면의 지침을 따릅니다 [.zip 파일이 나 URL에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)합니다.
>
>Zip를 사용 하 여 배포를 배포에서 실행 되도록 앱을 설정할 수 있습니다 [패키지에서 실행](run-functions-from-deployment-package.md) 모드입니다. 패키지의 실행 모드를 설정 하려면 설정 합니다 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정 값을 `1`입니다. Zip 배포를 사용 하는 것이 좋습니다. 응용 프로그램에 대 한 빠른 로드 시간을 생성 하 고 VS Code, Visual Studio 및 Azure CLI에 대 한 기본값.

>__사용 하는 경우:__ Zip 배포는 Dedicated 요금제에서 Linux를 실행 하는 Azure Functions 및 Windows를 실행 하는 Azure Functions에 대 한 권장 되는 배포 기술입니다.

### <a name="docker-container"></a>Docker 컨테이너

함수 앱이 포함 된 Linux 컨테이너 이미지를 배포할 수 있습니다.

>__사용 방법:__ Dedicated 요금제에서 Linux 함수 앱을 만들고에서 실행 하는 컨테이너 이미지를 지정 합니다. 다음 두 가지 방법으로 수행할 수 있습니다.
>
>* Azure portal에서 Azure App Service 계획에서 Linux 함수 앱을 만듭니다. 에 대 한 **게시**를 선택 **Docker 이미지**, 한 다음 컨테이너를 구성 합니다. 이미지 호스팅되는 위치를 입력 합니다.
>* Azure CLI를 사용 하 여 App Service 계획에서 Linux 함수 앱을 만듭니다. 자세한 내용은 [사용자 지정 이미지를 사용 하 여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)합니다.
>
>사용자 지정 컨테이너를 사용 하 여 기존 앱에 배포 [Azure Functions 핵심 도구](functions-run-local.md)를 사용 합니다 [ `func deploy` ](functions-run-local.md#publish) 명령 합니다.

>__사용 하는 경우:__ Docker 컨테이너 옵션을 사용 Linux 환경을 통해 더 많이 제어 해야 하는 경우 함수 앱이 실행 됩니다. 이 배포 메커니즘은 Linux App Service 계획에서 실행 되는 함수에 대해서만 사용할 수 있습니다.

### <a name="web-deploy-msdeploy"></a>웹 배포 (MSDeploy)

웹 배포 패키지 및 Azure에서 Windows를 실행 하 여 함수 앱을 포함 하 여 IIS 서버에 Windows 응용 프로그램을 배포 합니다.

>__사용 방법:__ 사용 하 여 [Azure Functions 용 Visual Studio tools](functions-create-your-first-function-visual-studio.md)합니다. 선택을 취소 합니다 **패키지 파일 (권장)에서 실행할** 확인란 합니다.
>
>또한 다운로드할 수 있습니다 [웹 배포 3.6](https://www.iis.net/downloads/microsoft/web-deploy) 호출 `MSDeploy.exe` 직접.

>__사용 하는 경우:__ 기본 메커니즘은 웹 배포는 지원 하 고 아무 문제가 있지만 [zip 패키지와 함께 배포 실행에서 사용 하도록 설정](#zip-deploy)합니다. 자세한 내용은 참조는 [Visual Studio 개발 가이드](functions-develop-vs.md#publish-to-azure)합니다.

### <a name="source-control"></a>소스 제어

함수 앱에 Git 리포지토리를 연결할 소스 제어를 사용 합니다. 해당 리포지토리의 코드에 대 한 업데이트 배포를 트리거합니다. 자세한 내용은 참조는 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)합니다.

>__사용 방법:__ 원본 제어에서 게시를 설정 하는 Azure Functions 포털에서 배포 센터를 사용 합니다. 자세한 내용은 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요.

>__사용 하는 경우:__ 함수 앱에서 공동 작업을 수행 하는 팀에 대 한 소스 제어를 사용 하는 것이 좋습니다. 소스 제어에는 보다 복잡 한 배포 파이프라인을 사용 하도록 설정 하는 적절 한 배포 옵션입니다.

### <a name="local-git"></a>로컬 Git

Git를 사용 하 여 Azure Functions를 로컬 컴퓨터에서 로컬 Git 푸시 코드에 사용할 수 있습니다.

>__사용 방법:__ 지침을 따릅니다 [Azure App Service에 로컬 Git 배포](../app-service/deploy-local-git.md)합니다.

>__사용 하는 경우:__ 일반적으로 서로 다른 배포 방법을 사용 하는 것이 좋습니다. 로컬 Git에서 게시할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing)합니다.

### <a name="cloud-sync"></a>클라우드 동기화

사용 하 여 Azure Functions에 Dropbox 및 OneDrive에서 콘텐츠 동기화를 클라우드입니다.

>__사용 방법:__ 지침을 따릅니다 [클라우드 폴더에서 콘텐츠 동기화](../app-service/deploy-content-sync.md)합니다.

>__사용 하는 경우:__ 일반적으로 다른 배포 방법의 사용 하는 것이 좋습니다. 클라우드 동기화를 사용 하 여 게시할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing)합니다.

### <a name="ftp"></a>FTP

FTP를 사용 하 여 직접 Azure Functions에 파일을 전송할 수 있습니다.

>__사용 방법:__ 지침을 따릅니다 [FTP/s를 사용 하 여 콘텐츠를 배포](../app-service/deploy-ftp.md)합니다.

>__사용 하는 경우:__ 일반적으로 다른 배포 방법의 사용 하는 것이 좋습니다. FTP를 사용 하 여 게시할 때 해야 [트리거를 수동으로 동기화](#trigger-syncing)합니다.

### <a name="portal-editing"></a>포털 편집

포털 기반 편집기에서 함수 앱 (기본적으로 변경 내용을 저장할 때마다 배포)에 있는 파일을 직접 편집할 수 있습니다.

>__사용 방법:__ Azure portal에서 함수를 편집할 수 있어야 [포털에서 함수를 만든](functions-create-first-azure-function.md)합니다. 정보의 단일 원본을 유지 하려면 다른 배포 메서드를 사용 하 여 읽기 전용으로 함수를 사용 하면 편집할 수 없도록 합니다 계속 포털입니다. Azure portal에서 파일을 편집할 수 있는 상태로 반환할 수 있습니다을 수동으로 설정 편집 모드를 다시 `Read/Write` 모든 응용 프로그램 배포와 관련 된 설정을 제거 하 고 (같은 `WEBSITE_RUN_FROM_PACKAGE`). 

>__사용 하는 경우:__ 포털은 Azure Functions를 사용 하 여 시작 하는 좋은 방법입니다. 더 강 할수록 개발 작업에 대 한 클라이언트 도구를 사용 하는 것이 좋습니다.
>
>* [VS Code를 사용 하 여 시작](functions-create-first-function-vs-code.md)
>* [Azure Functions 핵심 도구를 사용 하 여 시작](functions-run-local.md)
>* [Visual Studio를 사용 하 여 시작](functions-create-your-first-function-visual-studio.md)

다음 표에서 운영 체제 및 언어를 편집 하는 지원 포털:

| | Windows Consumption | Windows 프리미엄 (미리 보기) | Windows 전용 | Linux 소비 (미리 보기) | Linux 전용 |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# 스크립트 |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript(Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python(미리 보기) | | | | | |
| PowerShell (미리 보기) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> 포털 편집을 Dedicated 요금제를 사용 하 여 Linux에서 함수에 대 한 HTTP 및 타이머 트리거에 대해서만 사용 됩니다.

## <a name="deployment-slots"></a>배포 슬롯

Azure 함수 앱을 배포할 때 프로덕션에 직접 배포 하는 대신 개별 배포 슬롯에 배포할 수 있습니다. 배포 슬롯에 대 한 자세한 내용은 참조 하세요. [Azure App Service 슬롯](../app-service/deploy-staging-slots.md)합니다.

### <a name="deployment-slots-levels-of-support"></a>배포 슬롯 수준의 지원

배포 슬롯에 대 한 지원의 두 가지 수준이 있습니다.

* **일반 공급 (GA)** : 완전히 지원 되며 프로덕션 용도로 승인 합니다.
* **미리 보기**: 아직 지원 되지 않지만 나중에 GA 상태에 도달 해야 합니다.

| OS/호스팅 계획 | 지원 수준 |
| --------------- | ------ |
| Windows Consumption | Preview |
| Windows 프리미엄 (미리 보기) | Preview |
| Windows 전용 | 일반 공급 |
| Linux 사용 | 지원되지 않음 |
| Linux 전용 | 일반 공급 |

## <a name="next-steps"></a>다음 단계

함수 앱을 배포 하는 방법에 대 한 자세한 내용을 보려면 다음이 문서를 읽어보세요. 

+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)
+ [Azure DevOps를 사용 하 여 지속적인 업데이트](functions-how-to-azure-devops.md)
+ [Azure Functions에 대 한 zip 배포](deployment-zip-push.md)
+ [패키지 파일에서 Azure Functions 실행](run-functions-from-deployment-package.md)
+ [Azure Functions에서 함수 앱에 대 한 리소스 배포 자동화](functions-infrastructure-as-code.md)
