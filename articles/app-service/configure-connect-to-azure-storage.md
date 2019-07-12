---
title: Azure Files를 사용 하 여 저장소 구성
description: 구성 하 고 App Service에서 Windows 컨테이너에서 Azure Files에 연결 하는 방법.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789047"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>App Service에서 Windows 컨테이너에서 Azure 파일을 구성 합니다.

> [!NOTE]
> 이 문서에서는 사용자 지정 Windows 컨테이너에 적용 됩니다. App Service에 배포 하려면 _Linux_를 참조 하십시오 [Azure Storage에서 콘텐츠 제공](./containers/how-to-serve-content-from-azure-storage.md)합니다.
>

이 가이드에서는 Windows 컨테이너에서 Azure Storage에 액세스 하는 방법을 보여 줍니다. 만 [Azure 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) 하 고 [Premium 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) 지원 됩니다. 이 방법에서 Azure 파일 공유를 사용합니다. 혜택에는 보안된 콘텐츠, 콘텐츠 이식성, 여러 앱에 대한 액세스 및 여러 가지 전송 메서드가 포함됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure CLI](/cli/azure/install-azure-cli)(2.0.46 이상)
- [Azure App Service에서 기존 Windows 컨테이너 앱](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure 파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Azure 파일 공유에 파일 업로드](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 파일 저장소가 기본이 아닌 별도로 청구를 웹 앱에 포함 되지 않은 합니다. 인프라 제한으로 인해 방화벽 구성을 사용 하 여 지원 하지 않습니다.
>

## <a name="link-storage-to-your-web-app-preview"></a>웹앱에 스토리지 연결(미리 보기)

 App Service 앱의 디렉터리에 Azure 파일 공유를 탑재 하려면 사용 합니다 [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 명령입니다. 저장소 유형 AzureFiles 이어야 합니다.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Azure Files에 연결 하려는 다른 디렉터리의 모든 공유에 대 한이 작업을 수행 해야 합니다.

## <a name="verify"></a>Verify

Azure Files 공유를 웹 앱에 연결 되 면 다음 명령을 실행 하 여이 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>다음 단계

- [Windows 컨테이너 (미리 보기)를 사용 하 여 Azure App Service에 ASP.NET 앱 마이그레이션](app-service-web-tutorial-windows-containers-custom-fonts.md)합니다.