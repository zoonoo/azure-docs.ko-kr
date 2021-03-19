---
title: 네트워크 라우팅 기본 설정 구성
titleSuffix: Azure Storage
description: Azure storage 계정에 대 한 네트워크 라우팅 기본 설정을 구성 하 여 네트워크 트래픽을 인터넷을 통해 클라이언트에서 계정으로 라우팅하는 방법을 지정 합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589857"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Azure Storage에 대 한 네트워크 라우팅 기본 설정 구성

이 문서에서는 저장소 계정에 대 한 네트워크 라우팅 기본 설정 및 경로 특정 끝점을 구성 하는 방법을 설명 합니다. 

네트워크 라우팅 기본 설정은 네트워크 트래픽을 인터넷을 통해 클라이언트에서 계정으로 라우팅하는 방법을 지정 합니다. 경로 관련 끝점은 저장소 계정에 대해 만들 Azure Storage 있는 새 끝점입니다. 이러한 끝점은 기본 라우팅 기본 설정을 변경 하지 않고 원하는 경로를 통해 트래픽을 라우팅합니다. 자세히 알아보려면 [Azure Storage의 네트워크 라우팅 기본 설정](network-routing-preference.md)을 참조 하세요.

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>기본 공용 끝점에 대 한 라우팅 기본 설정 구성

기본적으로 저장소 계정의 공용 끝점에 대 한 라우팅 기본 설정은 Microsoft 글로벌 네트워크로 설정 됩니다. 스토리지 계정의 퍼블릭 엔드포인트에 대한 기본 라우팅 기본 설정으로 Microsoft 글로벌 네트워크 또는 인터넷 라우팅을 선택할 수 있습니다. 이러한 두 라우팅 유형 간의 차이점에 대 한 자세한 내용은 [Azure Storage의 네트워크 라우팅 기본 설정](network-routing-preference.md)을 참조 하세요. 

### <a name="portal"></a>[포털](#tab/azure-portal)

라우팅 기본 설정을 인터넷 라우팅으로 변경 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털에서 저장소 계정으로 이동 합니다.

3. **설정** 에서 **네트워킹** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![네트워킹 메뉴 옵션](./media/configure-network-routing-preference/networking-option.png)

4.  **방화벽 및 가상 네트워크** 탭의 **네트워크 라우팅** 에서 **라우팅 기본** 설정 설정을 **인터넷 라우팅** 으로 변경 합니다.

5.  **저장** 을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![인터넷 라우팅 옵션](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. `Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

   ```powershell
   Connect-AzAccount
   ```

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 라우팅 기본 설정을 인터넷 라우팅으로 변경 하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 사용 하 고 `--routing-choice` 매개 변수를로 설정 `InternetRouting` 합니다.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   `<resource-group-name>`자리 표시자 값을 저장소 계정이 포함 된 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>`자리 표시자 값을 저장소 계정의 이름으로 바꿉니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure 구독에 로그인합니다.

   - Azure Cloud Shell를 시작 하려면 [Azure Portal](https://portal.azure.com)에 로그인 합니다.

   - CLI의 로컬 설치에 로그인 하려면 [az login](/cli/azure/reference-index#az-login) 명령을 실행 합니다.

     ```azurecli
     az login
     ```
2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 라우팅 기본 설정을 인터넷 라우팅으로 변경 하려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 사용 하 고 `--routing-choice` 매개 변수를로 설정 `InternetRouting` 합니다.

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

---

## <a name="configure-a-route-specific-endpoint"></a>경로 특정 끝점 구성

경로 특정 끝점을 구성할 수도 있습니다. 예를 들어 기본 끝점에 대 한 라우팅 기본 설정을 *인터넷 라우팅* 으로 설정한 다음, 인터넷의 클라이언트와 저장소 계정 간의 트래픽을 Microsoft 글로벌 네트워크를 통해 라우팅할 수 있도록 하는 경로 특정 끝점을 게시할 수 있습니다.

이 기본 설정은 경로 특정 끝점에만 영향을 줍니다. 이 기본 설정에는 기본 라우팅 기본 설정에 영향을 주지 않습니다.  

### <a name="portal"></a>[포털](#tab/azure-portal)

1.  포털에서 저장소 계정으로 이동 합니다.

2.  **설정** 에서 **네트워킹** 을 선택 합니다.

3.  **방화벽 및 가상 네트워크** 탭의 **경로 관련 끝점 게시** 에서 경로 관련 끝점의 라우팅 기본 설정을 선택 하 고 **저장** 을 클릭 합니다.

    다음 그림은 선택 된 **Microsoft 네트워크 라우팅** 옵션을 보여 줍니다.

    > [!div class="mx-imgBorder"]
    > ![Microsoft 네트워크 라우팅 옵션](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 경로 특정 끝점을 구성 하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 사용 합니다. 

   - Microsoft 네트워크 라우팅 기본 설정을 사용 하는 경로 특정 끝점을 만들려면 `-PublishMicrosoftEndpoint` 매개 변수를로 설정 `true` 합니다. 

   - 인터넷 라우팅 기본 설정을 사용 하는 경로 특정 끝점을 만들려면 `-PublishInternetEndpointTo` 매개 변수를로 설정 `true` 합니다.  

   다음 예제에서는 Microsoft 네트워크 라우팅 기본 설정을 사용 하는 경로 특정 끝점을 만듭니다.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   `<resource-group-name>`자리 표시자 값을 저장소 계정이 포함 된 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>`자리 표시자 값을 저장소 계정의 이름으로 바꿉니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 경로 특정 끝점을 구성 하려면 [az storage account update](/azure/storage/account#az-storage-account-update) 명령을 사용 합니다. 

   - Microsoft 네트워크 라우팅 기본 설정을 사용 하는 경로 특정 끝점을 만들려면 `--publish-microsoft-endpoints` 매개 변수를로 설정 `true` 합니다. 

   - 인터넷 라우팅 기본 설정을 사용 하는 경로 특정 끝점을 만들려면 `--publish-internet-endpoints` 매개 변수를로 설정 `true` 합니다.  

   다음 예제에서는 Microsoft 네트워크 라우팅 기본 설정을 사용 하는 경로 특정 끝점을 만듭니다.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   `<storage-account-name>`자리 표시자 값을 저장소 계정의 이름으로 바꿉니다.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>경로 관련 끝점의 끝점 이름 찾기

경로 특정 끝점을 구성한 경우 저장소 계정의 속성에서 끝점을 찾을 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1.  **설정** 에서 **속성** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![속성 메뉴 옵션](./media/configure-network-routing-preference/properties.png)

2.  라우팅 기본 설정을 지 원하는 각 서비스에 대해 **Microsoft 네트워크 라우팅** 끝점이 표시 됩니다. 이 이미지는 blob 및 파일 서비스에 대 한 끝점을 보여 줍니다.

    > [!div class="mx-imgBorder"]
    > ![경로 관련 끝점에 대 한 Microsoft 네트워크 라우팅 옵션](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 끝점을 콘솔에 인쇄 하려면 `PrimaryEndpoints` 저장소 계정 개체의 속성을 사용 합니다.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   `<resource-group-name>`자리 표시자 값을 저장소 계정이 포함 된 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>`자리 표시자 값을 저장소 계정의 이름으로 바꿉니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 콘솔에 끝점을 인쇄 하려면 저장소 계정 개체의 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 속성을 사용 합니다.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   `<resource-group-name>`자리 표시자 값을 저장소 계정이 포함 된 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>`자리 표시자 값을 저장소 계정의 이름으로 바꿉니다.

---

## <a name="see-also"></a>참고 항목

- [네트워크 라우팅 기본 설정](network-routing-preference.md)
- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
