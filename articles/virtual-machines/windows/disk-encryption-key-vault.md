---
title: Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는에서 사용할 키 자격 증명 모음을 만들고 구성 하는 단계를 제공 Azure Disk Encryption
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f1317989434cca9375c612c8af0525323c6dda3c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82081662"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성

Azure Disk Encryption는 Azure Key Vault를 사용 하 여 디스크 암호화 키와 암호를 제어 하 고 관리 합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/key-vault-get-started.md) 및 [키 자격 증명 모음 보안](../../key-vault/general/secure-your-key-vault.md)을 참조하세요. 

> [!WARNING]
> - 이전에 Azure AD에서 Azure Disk Encryption 사용 하 여 VM을 암호화 한 경우 계속이 옵션을 사용 하 여 VM을 암호화 해야 합니다. 자세한 내용은 [AZURE AD를 사용 하 여 Azure Disk Encryption에 대 한 키 자격 증명 모음 만들기 및 구성 (이전 릴리스)](disk-encryption-key-vault-aad.md) 을 참조 하세요.

Azure Disk Encryption 사용할 키 자격 증명 모음을 만들고 구성 하는 단계는 다음 세 단계로 구성 됩니다.

1. 필요한 경우 리소스 그룹 만들기
2. 키 자격 증명 모음 만들기 
3. 키 자격 증명 모음 고급 액세스 정책 설정

이러한 단계는 다음 퀵 스타트에 설명 되어 있습니다.

- [Azure CLI를 사용하여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)

KEK (키 암호화 키)를 생성 하거나 가져올 수도 있습니다.

> [!Note]
> 이 문서의 단계는 [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 및 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)에서 자동화 됩니다.

## <a name="install-tools-and-connect-to-azure"></a>도구를 설치 하 고 Azure에 연결

이 문서의 단계는 [Azure CLI](/cli/azure/), [Azure PowerShell Az module](/powershell/azure/overview)또는 [Azure Portal](https://portal.azure.com)를 사용 하 여 완료할 수 있습니다.

브라우저를 통해 포털에 액세스할 수 있지만 Azure CLI 하 고 로컬 설치가 필요 Azure PowerShell. 자세한 내용은 [Windows Azure Disk Encryption: 설치 도구를](disk-encryption-windows.md#install-tools-and-connect-to-azure) 참조 하세요.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell를 사용 하기 전에 먼저 Azure 구독에 연결 해야 합니다. 이렇게 하려면 Azure CLI를 [사용](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)하 여 로그인 하거나, [Azure Powershell을 사용](/powershell/azure/authenticate-azureps?view=azps-2.5.0)하 여 로그인 하거나, 메시지가 표시 되 면 Azure Portal에 자격 증명을 제공 합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [필수 조건 PowerShell 스크립트 Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Windows vm에서 Azure Disk Encryption 시나리오](disk-encryption-windows.md) 알아보기
- [Azure Disk Encryption 문제를 해결](disk-encryption-troubleshooting.md) 하는 방법 알아보기
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md) 읽기
