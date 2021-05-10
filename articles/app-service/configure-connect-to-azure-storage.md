---
title: Azure Storage(컨테이너) 추가
description: Azure App Service의 컨테이너화된 앱에서 사용자 지정 네트워크 공유를 연결하는 방법을 알아봅니다. 앱 간에 파일을 공유하고, 정적 콘텐츠를 원격으로 관리하고, 로컬로 액세스하는 등의 작업을 수행합니다.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777620"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>App Service의 컨테이너에서 네트워크 공유로 Azure Storage 액세스(미리 보기)

::: zone pivot="container-windows"

이 가이드에서는 Azure Storage Files를 App Service의 Windows 컨테이너에 네트워크 공유로 연결하는 방법을 보여줍니다. [Azure Files 공유](../storage/files/storage-how-to-use-files-cli.md)와 [프리미엄 파일 공유](../storage/files/storage-how-to-create-file-share.md)만 지원됩니다. 혜택에는 보안된 콘텐츠, 콘텐츠 이식성, 여러 앱에 대한 액세스 및 여러 가지 전송 메서드가 포함됩니다.

> [!NOTE]
>App Service의 Azure Storage는 **미리 보기** 로 제공되며 **프로덕션 시나리오** 에서는 **지원되지 않습니다**.

::: zone-end

::: zone pivot="container-linux"

이 가이드에서는 Linux 컨테이너 App Service에 Azure Storage를 연결하는 방법을 보여줍니다. 보안 콘텐츠, 콘텐츠 이식성, 영구적 스토리지, 여러 앱에 대한 액세스, 여러 전송 방법 등의 이점이 있습니다.

> [!NOTE]
>App Service의 Azure Storage는 App Service on Linux 및 Web App for Containers에 대해 **미리 보기** 로 제공됩니다. **프로덕션 시나리오** 에서는 **지원되지 않습니다**.

::: zone-end

## <a name="prerequisites"></a>필수 요건

::: zone pivot="container-windows"

- [Azure App Service의 기존 Windows 컨테이너 앱](quickstart-custom-container.md)
- [Azure 파일 공유 만들기](../storage/files/storage-how-to-use-files-cli.md)
- [Azure 파일 공유에 파일 업로드](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- 기존 [App Service on Linux 앱](index.yml)
- [Azure Storage 계정](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure 파일 공유 및 디렉터리](../storage/files/storage-how-to-use-files-cli.md)

::: zone-end

> [!NOTE]
> Azure Files는 기본이 아닌 스토리지이며 별도로 청구되고, 웹앱에 포함되지 않습니다. 인프라 제한으로 인해 방화벽 구성 사용을 지원하지 않습니다.
>

## <a name="limitations"></a>제한 사항

::: zone pivot="container-windows"

- 고유한 코드 가져오기 시나리오(컨테이너화되지 않은 Windows 앱)에서는 현재 App Service의 Azure Storage가 **지원되지 않습니다**.
- App Service의 Azure Storage는 인프라 제한으로 인해 **스토리지 방화벽** 구성 사용을 **지원하지 않습니다**.
- App Service가 포함된 Azure Storage를 사용하면 앱당 **최대 5개** 의 탑재 지점을 지정할 수 있습니다.
- 앱에 탑재된 Azure Storage는 App Service FTP/FTPS 엔드포인트를 통해 액세스할 수 없습니다. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용합니다.

::: zone-end

::: zone pivot="container-linux"

- App Service의 Azure Storage는 **Azure Files 컨테이너**(읽기/쓰기) 및 **Azure Blob 컨테이너**(읽기 전용) 탑재를 지원합니다.
- App Service의 Azure Storage를 사용하면 앱당 **최대 5개** 의 탑재 지점을 지정할 수 있습니다.
- 앱에 탑재된 Azure Storage는 App Service FTP/FTPS 엔드포인트를 통해 액세스할 수 없습니다. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용합니다.

::: zone-end

## <a name="link-storage-to-your-app"></a>앱에 스토리지 연결

::: zone pivot="container-windows"

[Azure Storage 계정, 파일 공유 및 디렉터리](#prerequisites)를 만들었으면 이제 Azure Storage를 사용하여 앱을 구성할 수 있습니다.

Azure Files 공유를 App Service 앱의 디렉터리에 탑재하려면 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 명령을 사용합니다. 스토리지 유형은 AzureFiles여야 합니다.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Azure Files 공유에 연결하려는 다른 디렉터리에 대해 이 작업을 수행해야 합니다.

::: zone-end

::: zone pivot="container-linux"

[Azure Storage 계정, 파일 공유 및 디렉터리](#prerequisites)를 만들었으면 이제 Azure Storage를 사용하여 앱을 구성할 수 있습니다.

스토리지 계정을 App Service 앱의 디렉터리에 탑재하려면 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 명령을 사용합니다. 스토리지 형식은 AzureBlob 또는 AzureFiles일 수 있습니다. 이 예제에서는 AzureFiles가 사용되지 않습니다. 탑재 경로 설정은 Azure Storage에 탑재하려는 컨테이너 내의 폴더에 해당합니다. '/'로 설정하면 Azure Storage에 전체 컨테이너가 탑재됩니다.


> [!CAUTION]
> 웹앱에 탑재 경로로 지정된 디렉터리는 비어 있어야 합니다. 이 디렉터리에 저장된 모든 콘텐츠는 외부 마운트가 추가될 때 삭제됩니다. 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱 및 해당 콘텐츠의 백업을 만듭니다.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

스토리지 계정에 연결하려는 다른 디렉터리에 대해 이 작업을 수행해야 합니다.

::: zone-end

## <a name="verify-linked-storage"></a>연결된 스토리지 확인

공유가 앱에 연결되면 다음 명령을 실행하여 이를 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>다음 단계

::: zone pivot="container-windows"

- [사용자 지정 컨테이너를 사용하여 Azure App Service로 사용자 지정 소프트웨어 마이그레이션](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [사용자 지정 컨테이너 구성](configure-custom-container.md?pivots=platform-linux)

::: zone-end