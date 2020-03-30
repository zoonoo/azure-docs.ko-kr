---
title: Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 Azure 디스크 암호화에 사용할 키 자격 증명 모음을 만들고 구성하는 단계를 제공합니다.
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245241"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성

Azure 디스크 암호화는 Azure Key Vault를 사용하여 디스크 암호화 키 및 비밀을 제어하고 관리합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/key-vault-get-started.md) 및 [키 자격 증명 모음 보안](../../key-vault/key-vault-secure-your-key-vault.md)을 참조하세요. 

> [!WARNING]
> - 이전에 Azure AD와 함께 Azure 디스크 암호화를 사용하여 VM을 암호화한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)를 사용한 Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault-aad.md) 참조하십시오.

Azure 디스크 암호화에 사용할 키 자격 증명 모음을 만들고 구성하려면 다음 세 단계가 포함됩니다.

1. 필요한 경우 리소스 그룹을 만듭니다.
2. 키 자격 증명 모음 만들기. 
3. 키 볼트 고급 액세스 정책 설정

이러한 단계는 다음 빠른 시작에 나와 있습니다.

- [Azure CLI를 사용하여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)

원하는 경우 KEK(키 암호화 키)를 생성하거나 가져올 수도 있습니다.

> [!Note]
> 이 문서의 단계는 [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 및 Azure 디스크 암호화 필수 구성 조건 [PowerShell 스크립트에서](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)자동화됩니다.

## <a name="install-tools-and-connect-to-azure"></a>도구를 설치하고 Azure에 연결

이 문서의 단계는 Azure [CLI, Azure](/cli/azure/) [PowerShell Az 모듈](/powershell/azure/overview)또는 Azure [포털을](https://portal.azure.com)통해 완료할 수 있습니다.

브라우저를 통해 포털에 액세스할 수 있지만 Azure CLI 및 Azure PowerShell은 로컬 설치가 필요합니다. 자세한 내용은 [Windows용 Azure 디스크 암호화: 도구 설치를](disk-encryption-windows.md#install-tools-and-connect-to-azure) 참조하십시오.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell을 사용하기 전에 먼저 Azure 구독에 연결해야 합니다. 이렇게 하려면 [Azure CLI로 로그인,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [Azure Powershell으로 로그인](/powershell/azure/authenticate-azureps?view=azps-2.5.0)또는 메시지가 표시되면 Azure 포털에 자격 증명을 제공합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>다음 단계

- [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 디스크 암호화 필수 구성 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Windows VM에서 Azure 디스크 암호화 시나리오](disk-encryption-windows.md) 알아보기
- [Azure 디스크 암호화 문제 해결](disk-encryption-troubleshooting.md) 방법 알아보기
- Azure [디스크 암호화 샘플 스크립트](disk-encryption-sample-scripts.md) 읽기
