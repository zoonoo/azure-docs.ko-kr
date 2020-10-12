---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75769672"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>REST API를 사용하여 ZIP 파일 배포 

[배포 서비스 REST API](https://github.com/projectkudu/kudu/wiki/REST-API)를 사용하여 .zip 파일을 Azure의 앱에 배포할 수 있습니다. 배포하려면 POST 요청을 https://<app_name>.scm.azurewebsites.net/api/zipdeploy에 보냅니다. POST 요청은 메시지 본문에 .zip 파일을 포함해야 합니다. 앱에 대한 배포 자격 증명은 HTTP 기본 인증을 사용하여 요청으로 제공됩니다. 자세한 내용은 [.zip 푸시 배포 참조](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)를 참조하세요. 

HTTP BASIC 인증의 경우 App Service 배포 자격 증명이 필요합니다. 배포 자격 증명을 설정하는 방법을 알아보려면 [사용자 수준 자격 증명 설정 및 다시 설정](../articles/app-service/deploy-configure-credentials.md#userscope)을 참조하세요.

### <a name="with-curl"></a>cURL 사용

다음 예제에서는 cURL 도구를 사용하여 .zip 파일을 배포합니다. `<deployment_user>`, `<zip_file_path>` 및 `<app_name>` 자리 표시자를 바꿉니다. cURL에서 프롬프트가 표시되면 암호를 입력합니다.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

이 요청은 업로드된 .zip 파일에서 푸시 배포를 트리거합니다. 다음 cURL 예제에 표시된 대로 `https://<app_name>.scm.azurewebsites.net/api/deployments` 엔드포인트를 사용하여 현재 및 과거 배포를 검토할 수 있습니다. 다시 `<app_name>`을 앱의 이름으로 바꾸고 `<deployment_user>`를 배포 자격 증명의 사용자 이름으로 바꿉니다.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>PowerShell 사용

다음 예제에서는 [AzWebapp](/powershell/module/az.websites/publish-azwebapp) 를 사용 하 여 .zip 파일을 업로드 합니다. `<group-name>`, `<app-name>` 및 `<zip-file-path>` 자리 표시자를 바꿉니다.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

이 요청은 업로드된 .zip 파일에서 푸시 배포를 트리거합니다. 

현재 및 과거 배포를 검토하려면 다음 명령을 실행합니다. 다시,, `<deployment-user>` `<deployment-password>` 및 `<app-name>` 자리 표시자를 바꿉니다.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
