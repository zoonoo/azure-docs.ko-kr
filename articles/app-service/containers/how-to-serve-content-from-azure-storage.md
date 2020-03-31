---
title: Azure 저장소에서 Linux 컨테이너로 콘텐츠 제공
description: Azure 앱 서비스에서 Linux 컨테이너에 사용자 지정 네트워크 공유를 연결하는 방법에 대해 알아봅니다. 앱 간에 파일을 공유하고, 정적 콘텐츠를 원격으로 관리하고, 로컬로 액세스하는 등
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297920"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Linux의 App Service에 있는 Azure Storage의 콘텐츠 제공

> [!NOTE]
> 이 문서는 Linux 컨테이너에 적용됩니다. 사용자 지정 Windows 컨테이너에 배포하려면 [앱 서비스의 Windows 컨테이너에서 Azure 파일 구성](../configure-connect-to-azure-storage.md)을 참조하십시오. Linux의 앱 서비스에서 Azure 저장소는 **미리 보기** 기능입니다. 이 기능은 **프로덕션 시나리오에서 지원되지 않습니다.**
>

이 가이드에서는 Linux에서 앱 서비스에 Azure 저장소를 연결하는 방법을 보여 주며 있습니다. 보안 콘텐츠, 콘텐츠 이식성, 영구 저장소, 여러 앱에 대한 액세스 및 여러 전송 방법이 이점을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](/cli/azure/install-azure-cli)(2.0.46 이상)
- [리눅스 앱의](https://docs.microsoft.com/azure/app-service/containers/)기존 앱 서비스 .
- [Azure 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure 파일 공유 및 디렉터리](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>앱 서비스를 통해 Azure 저장소의 제한 사항

- 앱 서비스가 있는 Azure 저장소는 **Linux에서** 앱 서비스에 대한 미리 보기 및 컨테이너용 웹 앱입니다. **프로덕션 시나리오에**대 한 **지원 되지 않습니다.**
- 앱 서비스를 갖춘 Azure 저장소는 Azure 파일 컨테이너(읽기/쓰기) 및 **Azure Blob 컨테이너(읽기** 전용)를 지원합니다. **Azure Files containers**
- 앱 서비스가 있는 Azure 저장소는 인프라 제한으로 인해 **저장소 방화벽** 구성사용을 **지원하지 않습니다.**
- 앱 서비스를 갖춘 Azure 저장소를 사용하면 앱당 **최대 5개의** 마운트 지점을 지정할 수 있습니다.
- 앱에 탑재된 Azure 저장소는 앱 서비스 FTP/FTP 끝점을 통해 액세스할 수 없습니다. [Azure 저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/)사용합니다.
- Azure 저장소는 웹 앱에 **포함되지 않으며** 별도로 청구됩니다. [Azure 저장소 가격책정에](https://azure.microsoft.com/pricing/details/storage)대해 자세히 알아보십시오.

> [!WARNING]
> Azure Blob 저장소를 사용하는 앱 서비스 구성은 2020년 2월에만 읽기가 됩니다. [자세히 알아보기](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Azure 저장소로 앱 구성

[Azure Storage 계정, 파일 공유 및 디렉터리를](#prerequisites)만든 후에는 Azure Storage를 통해 앱을 구성할 수 있습니다.

앱 서비스 앱의 디렉터리에 저장소 계정을 탑재하려면 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 명령을 사용합니다. 스토리지 형식은 AzureBlob 또는 AzureFiles일 수 있습니다. AzureFiles는 이 예제에서 사용됩니다.


> [!CAUTION]
> 웹 앱의 탑재 경로로 지정된 디렉터리가 비어 있어야 합니다. 이 디렉터리에 저장된 모든 콘텐츠는 외부 마운트가 추가되면 삭제됩니다. 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱 및 해당 콘텐츠의 백업을 만듭니다.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

스토리지 계정에 연결하려는 다른 디렉터리에 대해 이 작업을 수행해야 합니다.

## <a name="verify-azure-storage-link-to-the-web-app"></a>웹 앱에 대한 Azure 저장소 링크 확인

스토리지 컨테이너가 웹앱에 연결되면 다음 명령을 실행하여 이를 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>도커 컴포지에 Azure 저장소 사용

Azure 저장소는 사용자 지정 ID를 사용하여 다중 컨테이너 앱과 함께 탑재할 수 있습니다. 사용자 지정 ID 이름을 보려면 [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)을 실행합니다.

*docker-compose.yml* 파일에서 `volumes` 옵션을 에 `custom-id`매핑합니다. 예를 들어:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>다음 단계

- [Azure 앱 서비스에서 웹 앱을 구성합니다.](../configure-common.md)

