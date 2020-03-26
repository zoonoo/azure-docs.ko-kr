---
title: Azure Resource Manager 템플릿을 사용하여 Virtual Machine Scale Set 만들기 및 암호화
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Virtual Machine Scale Set를 만들고 암호화하는 방법을 배웁니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "72530941"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 가상 머신 확장 집합 암호화

Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합을 암호화하거나 암호 해독을 수행할 수 있습니다.

## <a name="deploying-templates"></a>템플릿 배포

먼저 시나리오에 적합한 템플릿을 선택합니다.

- [실행 중인 Linux 가상 머신 확장 집합에서 디스크 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [실행 중인 Windows 가상 머신 확장 집합에서 디스크 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Jumpbox를 통해 Linux VM의 가상 머신 확장 집합 배포 및 Linux 가상 머신 확장 집합에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Jumpbox를 통해 Windows VM의 가상 머신 확장 집합 배포 및 Windows 가상 머신 확장 집합에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [실행 중인 Linux 가상 머신 확장 집합에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [실행 중인 Windows 가상 머신 확장 집합에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

그러고 나서 다음 단계를 수행합니다.

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>다음 단계

- [가상 머신 확장 집합을 위한 Azure Disk Encryption](disk-encryption-overview.md)
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](disk-encryption-cli.md)
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](disk-encryption-powershell.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용](disk-encryption-extension-sequencing.md)