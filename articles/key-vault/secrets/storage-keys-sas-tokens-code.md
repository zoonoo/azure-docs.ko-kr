---
title: 코드에서 공유 액세스 서명 토큰 가져오기 | Azure Key Vault
description: 관리형 스토리지 계정 기능은 Azure Key Vault와 Azure Storage 계정 간의 원활한 통합을 제공합니다. 이 샘플에서는 .NET용 Azure SDK를 사용하여 SAS 토큰을 관리합니다.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0f81ffb5279e10c71f7d7cccfb6b738bc12e5cf4
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086779"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>SAS 정의를 만들고 코드에서 공유 액세스 서명 토큰 가져오기

키 자격 증명 모음에 저장된 SAS(공유 액세스 서명) 토큰을 사용하여 스토리지 계정을 관리할 수 있습니다. 자세한 내용은 [SAS를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../../storage/common/storage-sas-overview.md)를 참조하세요.

> [!NOTE]
> [RBAC(역할 기반 액세스 제어)](../../storage/common/storage-auth-aad.md)를 사용하여 공유 키 권한 부여에 대한 뛰어난 보안 및 사용 편의성을 위해 스토리지 계정을 보호하는 것이 좋습니다.

이 문서에서는 SAS 정의를 만들고 SAS 토큰을 가져오는 .NET 코드 샘플을 제공합니다. Key Vault 관리형 스토리지 계정에 대해 생성된 클라이언트를 포함한 자세한 내용은 [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) 샘플을 참조하세요. SAS 토큰을 만들고 저장하는 방법에 대한 자세한 내용은 [Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리](overview-storage-keys.md) 또는 [Key Vault 및 Azure PowerShell을 사용하여 스토리지 계정 키 관리](overview-storage-keys-powershell.md)를 참조하세요.

## <a name="code-samples"></a>코드 샘플

다음 예제에서는 SAS 템플릿을 만듭니다.

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

이 템플릿을 사용하면 SAS 정의를 만들 수 있습니다. 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

SAS 정의가 만들어지면 `SecretClient`를 사용하여 비밀과 같은 SAS 토큰을 검색할 수 있습니다. 비밀 이름은 스토리지 계정 이름 뒤에 대시를 붙여 시작해야 합니다.

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

공유 액세스 서명 토큰이 곧 만료되는 경우 동일한 비밀을 다시 가져와서 새 토큰을 생성할 수 있습니다.

Key Vault SAS 토큰에서 검색을 사용하여 Azure Storage 서비스에 액세스하는 방법에 대한 가이드는 [계정 SAS를 사용하여 Blob 서비스에 액세스](https://docs.microsoft.com/azure/storage/common/storage-account-sas-create-dotnet#use-an-account-sas-from-a-client)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [SAS를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../../storage/common/storage-sas-overview.md) 방법을 알아보세요.
- [Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리](overview-storage-keys.md) 또는 [Key Vault 및 Azure PowerShell을 사용하여 스토리지 계정 키 관리](overview-storage-keys-powershell.md) 방법을 알아보세요.
- [관리형 스토리지 계정 키 샘플](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)을 확인하세요.
