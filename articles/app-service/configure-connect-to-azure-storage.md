---
title: 사용자 지정 저장소 추가(Windows 컨테이너)
description: Azure 앱 서비스에서 사용자 지정 Windows 컨테이너에 사용자 지정 네트워크 공유를 연결하는 방법에 대해 알아봅니다. 앱 간에 파일을 공유하고, 정적 콘텐츠를 원격으로 관리하고, 로컬로 액세스하는 등
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120675"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>앱 서비스의 Windows 컨테이너에서 Azure 파일 구성

> [!NOTE]
> 이 문서는 사용자 지정 Windows 컨테이너에 적용됩니다. _Linux에서_앱 서비스에 배포하려면 [Azure 저장소의 콘텐츠 제공을](./containers/how-to-serve-content-from-azure-storage.md)참조하십시오.
>

이 가이드에서는 Windows 컨테이너에서 Azure 저장소에 액세스하는 방법을 보여 주며 있습니다. [Azure 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) 및 프리미엄 파일 [공유만](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) 지원됩니다. 이 방법에서 Azure 파일 공유를 사용합니다. 혜택에는 보안된 콘텐츠, 콘텐츠 이식성, 여러 앱에 대한 액세스 및 여러 가지 전송 메서드가 포함됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](/cli/azure/install-azure-cli)(2.0.46 이상)
- [Azure 앱 서비스의 기존 Windows 컨테이너 앱](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure 파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Azure 파일 공유에 파일 업로드](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files는 기본이 아닌 저장소이며 웹 앱에 포함되지 않고 별도로 청구됩니다. 인프라 제한으로 인해 방화벽 구성사용을 지원하지 않습니다.
>

## <a name="limitations"></a>제한 사항

- Windows 컨테이너의 Azure 저장소는 **미리 보기 중이며** **프로덕션 시나리오에서는** **지원되지 않습니다.**
- Windows 컨테이너의 Azure 저장소는 Azure 파일 컨테이너(읽기/쓰기)만 마운트를 지원합니다. **Azure Files containers**
- Windows 컨테이너의 Azure 저장소는 현재 Windows 앱 서비스 계획에서 사용자 고유의 코드 시나리오를 가져오기 위해 **지원되지 않습니다.**
- Windows 컨테이너의 Azure 저장소는 인프라 제한으로 인해 **저장소 방화벽** 구성을 **지원하지 않습니다.**
- Windows 컨테이너의 Azure 저장소를 사용하면 앱당 **최대 5개의** 마운트 지점을 지정할 수 있습니다.
- 앱에 탑재된 Azure 저장소는 앱 서비스 FTP/FTP 끝점을 통해 액세스할 수 없습니다. [Azure 저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/)사용합니다.
- Azure 저장소는 독립적으로 청구되며 웹 앱에 **포함되지 않습니다.** [Azure 저장소 가격책정에](https://azure.microsoft.com/pricing/details/storage)대해 자세히 알아보십시오.

## <a name="link-storage-to-your-web-app-preview"></a>웹앱에 스토리지 연결(미리 보기)

 앱 서비스 앱의 디렉터리에 Azure 파일 공유를 탑재하려면 명령을 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 사용합니다. 저장소 유형은 AzureFiles여야 합니다.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Azure Files 공유에 연결하려는 다른 디렉터리에 대해 이 작업을 수행해야 합니다.

## <a name="verify"></a>확인

Azure Files 공유가 웹 앱에 연결되면 다음 명령을 실행하여 이를 확인할 수 있습니다.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>다음 단계

- [Windows 컨테이너(미리 보기)를 사용하여 ASP.NET 앱을 Azure 앱 서비스로 마이그레이션합니다.](app-service-web-tutorial-windows-containers-custom-fonts.md)
