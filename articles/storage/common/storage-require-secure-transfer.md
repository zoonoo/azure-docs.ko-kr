---
title: 안전한 연결을 보장하기 위해 보안 전송 필요
titleSuffix: Azure Storage
description: Azure 저장소에 대한 요청에 대한 보안 전송을 요구하는 방법을 알아봅니다. 저장소 계정에 대한 보안 전송이 필요한 경우 안전하지 않은 연결에서 발생한 모든 요청은 거부됩니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870525"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>안전한 연결을 보장하기 위해 보안 전송 필요

저장소 계정에 대해 **Secure 전송 필수** 속성을 설정해야만 보안 연결에서 요청을 수락하도록 저장소 계정을 구성할 수 있습니다. 보안 전송이 필요한 경우 안전하지 않은 연결에서 발생하는 모든 요청이 거부됩니다. 모든 저장소 계정에 대해 항상 보안 전송을 요구하는 것이 좋습니다.

보안 전송이 필요한 경우 HTTPS를 통해 Azure 저장소 REST API 작업을 호출해야 합니다. HTTP를 통해 이루어진 모든 요청은 거부됩니다.

저장소 계정에 대한 보안 전송이 필요한 경우 암호화 없이 SMB를 통해 Azure File 공유에 연결하지 못합니다. 안전하지 않은 연결의 예로는 SMB 2.1, 암호화 없이 SMB 3.0 또는 Linux SMB 클라이언트의 일부 버전을 통해 만들어진 연결이 있습니다.

기본적으로 저장소 계정을 만들 때 **보안 전송 필수** 속성이 활성화됩니다.

> [!NOTE]
> Azure Storage에서 사용자 지정 도메인 이름에 대해 HTTPS를 지원하지 않으므로 사용자 지정 도메인 이름을 사용할 때 이 옵션이 적용되지 않습니다. 또한 클래식 스토리지 계정은 지원되지 않습니다.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Azure 포털에서 보안 전송 필요

[Azure 포털에서](https://portal.azure.com)저장소 계정을 만들 때 **Secure 전송 필수** 속성을 켤 수 있습니다. 기존 스토리지 계정을 활성화할 수도 있습니다.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>새 스토리지 계정에 대해 보안 전송 필요

1. Azure Portal에서 **스토리지 계정 만들기** 창을 엽니다.
1. **보안 전송이 필요한**경우 사용 중 에서 **를**선택합니다.

   ![스토리지 계정 만들기 블레이드](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>기존 스토리지 계정에 대해 보안 전송 필요

1. Azure Portal에서 기존 스토리지 계정을 선택합니다.
1. 스토리지 계정 메뉴 창의 **설정** 아래에서 **구성**을 선택합니다.
1. **보안 전송이 필요한**경우 사용 중 에서 **를**선택합니다.

   ![스토리지 계정 메뉴 창](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>코드에서 안전한 전송 필요

프로그래밍 방식으로 보안 전송을 요구하려면 저장소 계정에 _supportsHttpsTrafficOnly_ 속성을 설정합니다. 저장소 리소스 공급자 REST API, 클라이언트 라이브러리 또는 도구를 사용하여 이 속성을 설정할 수 있습니다.

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [파이썬 SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>PowerShell으로 안전한 전송 필요

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

`Connect-AzAccount`를 실행하여 Azure와 연결합니다.

 다음 명령줄을 사용하여 설정을 확인합니다.

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

다음 명령줄을 사용하여 설정을 사용하도록 설정합니다.

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Azure CLI를 사용하면 안전한 전송이 필요합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 다음 명령을 사용하여 설정을 확인합니다.

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

다음 명령을 사용하여 설정을 활성화합니다.

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>다음 단계

[Blob 저장소에 대한 보안 권장 사항](../blobs/security-recommendations.md)
