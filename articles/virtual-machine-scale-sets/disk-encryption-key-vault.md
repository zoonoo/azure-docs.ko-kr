---
title: Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는에서 사용할 키 자격 증명 모음을 만들고 구성 하는 단계를 제공 Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279011"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성

Azure Disk Encryption는 Azure Key Vault를 사용 하 여 디스크 암호화 키와 암호를 제어 하 고 관리 합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md) 및 [키 자격 증명 모음 보안](../key-vault/key-vault-secure-your-key-vault.md)을 참조하세요.

Azure Disk Encryption 사용할 키 자격 증명 모음을 만들고 구성 하는 단계는 다음 세 단계로 구성 됩니다.

1. 필요한 경우 리소스 그룹 만들기
2. 키 자격 증명 모음 만들기 
3. 키 자격 증명 모음 고급 액세스 정책 설정

이러한 단계는 다음 퀵 스타트에 설명 되어 있습니다.

KEK (키 암호화 키)를 생성 하거나 가져올 수도 있습니다.

## <a name="install-tools-and-connect-to-azure"></a>도구를 설치 하 고 Azure에 연결

이 문서의 단계는 [Azure CLI](/cli/azure/), [Azure PowerShell Az module](/powershell/azure/overview)또는 [Azure Portal](https://portal.azure.com)를 사용 하 여 완료할 수 있습니다.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell를 사용 하기 전에 먼저 Azure 구독에 연결 해야 합니다. 이렇게 하려면 Azure CLI를 [사용](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)하 여 로그인 하거나, [Azure Powershell을 사용](/powershell/azure/authenticate-azureps?view=azps-2.5.0)하 여 로그인 하거나, 메시지가 표시 되 면 Azure Portal에 자격 증명을 제공 합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](disk-encryption-cli.md)
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](disk-encryption-powershell.md)
