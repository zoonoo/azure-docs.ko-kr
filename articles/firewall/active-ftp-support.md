---
title: Azure Firewall 활성 FTP 지원
description: 기본적으로 활성 FTP는 Azure Firewall에서 사용하지 않도록 설정되어 있습니다. PowerShell, CLI 및 ARM 템플릿을 사용하여 이 기능을 사용하도록 설정할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: adbc2a9eb6cd3b054df84911604143ddb711ad20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499138"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall 활성 FTP 지원

FTP 서버는 활성 FTP를 사용하여 지정된 FTP 클라이언트 데이터 포트에 대한 데이터 연결을 시작합니다. 클라이언트 측 네트워크의 방화벽은 일반적으로 내부 클라이언트 포트에 대한 외부 연결 요청을 차단합니다. 자세한 내용은 [Active FTP vs. Passive FTP, a Definitive Explanation](https://slacksite.com/other/ftp.html)을 참조하세요.

기본적으로 FTP `PORT` 명령을 사용하는 FTP 바운스 공격으로부터 보호하기 위해 Azure Firewall에서 활성 FTP 지원을 사용하지 않도록 설정합니다. 그러나 Azure PowerShell, Azure CLI 또는 Azure ARM 템플릿을 사용하여 배포할 때 활성 FTP를 사용하도록 설정할 수 있습니다.


## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 배포하려면 `AllowActiveFTP` 매개 변수를 사용합니다. 자세한 내용은 [활성 FTP를 사용하여 방화벽 만들기](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)를 참조하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 배포하려면 `--allow-active-ftp` 매개 변수를 사용합니다. 자세한 내용은 [az network firewall create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters)를 참조하세요. 

## <a name="azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿

ARM 템플릿을 사용하여 배포하려면 `AdditionalProperties` 필드를 사용합니다.

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
자세한 내용은 [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Firewall을 배포하는 방법을 알아보려면 [Azure PowerShell을 사용하여 Azure Firewall 배포 및 구성](deploy-ps.md)을 참조하세요.