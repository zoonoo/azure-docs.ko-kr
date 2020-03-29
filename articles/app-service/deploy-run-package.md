---
title: ZIP 패키지에서 앱 실행
description: 원자성으로 앱의 ZIP 패키지를 배포합니다. ZIP 배포 프로세스 중에 앱 동작의 예측 가능성과 안정성을 개선합니다.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920725"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>ZIP 패키지에서 직접 Azure 앱 서비스에서 앱 실행

[Azure 앱 서비스에서](overview.md)배포 ZIP 패키지 파일에서 직접 앱을 실행할 수 있습니다. 이 문서에서는 앱에서 이 기능을 사용하도록 설정하는 방법을 보여 줍니다.

App Service의 다른 모든 배포 방법에는 공통점이 있습니다: 파일이 *앱의 D:\home\site\wwwroot(또는* Linux 앱용 */home/site/wwwroot)에* 배포됩니다. 런타임에 앱에서 동일한 디렉터리를 사용하므로 파일 잠금 충돌로 인해 배포가 실패하고 일부 파일이 아직 업데이트되지 않았기 때문에 앱이 예기치 않게 동작할 수 있습니다.

반대로 패키지에서 직접 실행하면 패키지의 파일이 *wwwroot* 디렉터리로 복사되지 않습니다. 대신 ZIP 패키지 자체는 읽기 전용 *wwwroot* 디렉토리로 직접 마운트됩니다. 패키지에서 직접 실행하려면 다음과 같은 몇 가지 이점이 있습니다.

- 배포와 런타임 간의 파일 잠금 충돌을 제거합니다.
- 항상 전체 배포된 앱만 실행되도록 합니다.
- (다시 시작으로) 프로덕션 앱에 배포될 수 있습니다.
- Azure Resource Manager 배포의 성능을 향상시킵니다.
- 특히 대규모 npm 패키지 트리가 있는 JavaScript 함수의 경우 콜드 부팅 시간을 줄일 수 있습니다.

> [!NOTE]
> 현재 는 ZIP 패키지 파일만 지원됩니다.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>패키지에서 실행 활성화

`WEBSITE_RUN_FROM_PACKAGE` 앱 설정을 사용하면 패키지에서 실행할 수 있습니다. 설정하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`패키지를 로컬에서 앱으로 실행할 수 있습니다. [원격 패키지에서 실행할](#run-from-external-url-instead)수도 있습니다.

## <a name="run-the-package"></a>패키지 실행

앱 서비스에서 패키지를 실행하는 가장 쉬운 방법은 Azure CLI [az 웹앱 배포 소스 구성-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 명령을 사용하는 것입니다. 예를 들어:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

`WEBSITE_RUN_FROM_PACKAGE` 앱 설정이 설정되어 있으므로 이 명령은 패키지 콘텐츠를 *앱의 D:\home\site\wwwroot* 디렉토리로 추출하지 않습니다. 대신 ZIP 파일을 *D:\home\data\SitePackages에*있는 것처럼 업로드하고 런타임에 로드할 ZIP 패키지의 이름이 포함된 동일한 디렉터리에서 *packagename.txt를* 만듭니다. ZIP 패키지를 다른 방식으로 업로드하는 경우(예: [FTP)](deploy-ftp.md) *D:\home\data\SitePackages* 디렉토리와 *packagename.txt* 파일을 수동으로 만들어야 합니다.

또한 명령은 앱을 다시 시작합니다. 앱 `WEBSITE_RUN_FROM_PACKAGE` 서비스는 설정되어 있기 때문에 업로드된 패키지를 읽기 전용 *wwwroot* 디렉터리로 마운트하고 마운트된 디렉터리에서 직접 앱을 실행합니다.

## <a name="run-from-external-url-instead"></a>대신 외부 URL에서 실행

Azure Blob 저장소와 같은 외부 URL에서 패키지를 실행할 수도 있습니다. [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 Blob 스토리지 계정에 패키지 파일을 업로드할 수 있습니다. [SAS(공유 액세스 서명)가](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 있는 개인 저장소 컨테이너를 사용하여 앱 서비스 런타임이 패키지에 안전하게 액세스할 수 있도록 해야 합니다. 

파일을 Blob 저장소에 업로드하고 파일에 대한 SAS URL이 `WEBSITE_RUN_FROM_PACKAGE` 있으면 앱 설정을 URL로 설정합니다. 다음 예제는 Azure CLI를 사용하여 수행합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Blob 저장소에 이름이 같은 업데이트된 패키지를 게시하는 경우 업데이트된 패키지가 앱 서비스에 로드되도록 앱을 다시 시작해야 합니다.

## <a name="troubleshooting"></a>문제 해결

- 패키지에서 직접 실행하면 읽기 전용이 됩니다. `wwwroot` 이 디렉터리에 파일을 작성하려고 하면 앱에 오류가 표시됩니다.
- TAR 및 GZIP 형식은 지원되지 않습니다.
- 이 기능은 로컬 [캐시와](overview-local-cache.md)호환되지 않습니다.
- 콜드 스타트 성능을 향상시면 로컬 Zip`WEBSITE_RUN_FROM_PACKAGE`옵션(=1)을 사용합니다.

## <a name="more-resources"></a>추가 리소스

- [Azure 앱 서비스에 대한 지속적인 배포](deploy-continuous-deployment.md)
- [ZIP 또는 WAR 파일로 코드 배포](deploy-zip.md)
