---
title: 클라우드 폴더의 콘텐츠 동기화
description: OneDrive 또는 Dropbox를 포함 하 여 클라우드 폴더에서 콘텐츠 동기화를 통해 Azure App Service에 앱을 배포 하는 방법에 대해 알아봅니다.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051241"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>클라우드 폴더에서 Azure App Service로 콘텐츠 동기화
이 문서에서는 Dropbox 및 OneDrive에서 [Azure App Service](./overview.md)로 사용자 콘텐츠를 동기화하는 방법을 보여 줍니다. 

콘텐츠 동기화 방법을 사용 하는 경우 지정 된 클라우드 폴더에서 앱 코드 및 콘텐츠를 사용 하 여 배포 준비 상태에 있는지 확인 한 다음 단추를 클릭 하 여 App Service 동기화 합니다. 

API의 기본적인 차이 때문에 **비즈니스용 OneDrive** 는 지원되지 않습니다.

> [!NOTE]
> 이전 배포 환경인 Azure Portal의 **개발 센터 (클래식)** 페이지는 2021 년 3 월부터 더 이상 사용 되지 않습니다. 이 변경 내용은 앱의 기존 배포 설정에 영향을 주지 않으며, **배포 센터** 페이지에서 계속 해 서 앱 배포를 관리할 수 있습니다.

## <a name="enable-content-sync-deployment"></a>콘텐츠 동기화 배포를 사용하도록 설정

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱에 대 한 관리 페이지로 이동 합니다.

1. 왼쪽 메뉴에서 **배포 센터**  >  **설정** 을 클릭 합니다. 

1. **원본** 에서 **OneDrive** 또는 **Dropbox** 를 선택 합니다.

1. 권한 **부여를 클릭 하** 고 권한 부여 프롬프트를 따릅니다. 

    ![Azure Portal의 배포 센터에서 OneDrive 또는 Dropbox에 권한을 부여 하는 방법을 보여 줍니다.](media/app-service-deploy-content-sync/choose-source.png)

    Azure 계정에 대해 OneDrive 또는 Dropbox를 한 번만 인증 하면 됩니다. 앱에 대해 다른 OneDrive 또는 Dropbox 계정을 승인 하려면 **계정 변경** 을 클릭 합니다.

1. **폴더** 에서 동기화 할 폴더를 선택 합니다. 이 폴더는 OneDrive 또는 Dropbox에서 다음과 같은 지정된 콘텐츠 경로 아래에 생성됩니다. 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. **저장** 을 클릭합니다.

## <a name="synchronize-content"></a>콘텐츠 동기화

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱에 대 한 관리 페이지로 이동 합니다.

1. 왼쪽 메뉴에서 **배포 센터**  >  **재배포/동기화** 를 클릭 합니다. 

    ![App Service를 사용 하 여 클라우드 폴더를 동기화 하는 방법을 보여 줍니다.](media/app-service-deploy-content-sync/synchronize.png)
   
1. **확인** 을 클릭 하 여 동기화를 확인 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

다음 명령을 실행 하 고 및를 바꿔서 동기화를 시작 합니다 \<group-name> \<app-name> .

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

다음 명령을 실행 하 고 및를 바꿔서 동기화를 시작 합니다 \<group-name> \<app-name> .

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>콘텐츠 동기화 배포를 사용하지 않도록 설정

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱에 대 한 관리 페이지로 이동 합니다.

1. 왼쪽 메뉴에서 **배포 센터**  >  **설정**  >  **연결 끊기** 를 클릭 합니다. 

    ![Azure Portal에서 App Service 앱과 클라우드 폴더 동기화를 분리 하는 방법을 보여 줍니다.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 Git 리포지토리에서 배포](deploy-local-git.md)