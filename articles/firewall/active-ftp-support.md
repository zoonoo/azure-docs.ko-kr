---
title: Azure 방화벽 활성 FTP 지원
description: 기본적으로 활성 FTP는 Azure 방화벽에서 사용 하지 않도록 설정 됩니다. PowerShell, CLI 및 ARM 템플릿을 사용 하 여이 기능을 사용 하도록 설정할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: aa543b32efcd1897eb0ee7d8b477742705095ad9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430737"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure 방화벽 활성 FTP 지원

FTP 서버는 활성 FTP를 사용 하 여 지정 된 FTP 클라이언트 데이터 포트에 대 한 데이터 연결을 시작 합니다. 클라이언트 쪽 네트워크의 방화벽은 일반적으로 내부 클라이언트 포트에 대 한 외부 연결 요청을 차단 합니다. 자세한 내용은 [활성 ftp 및 수동 ftp, 결정적 설명](https://slacksite.com/other/ftp.html)을 참조 하세요.

기본적으로 ftp 명령을 사용 하 여 FTP 바운스 공격 으로부터 보호 하기 위해 Azure 방화벽에서 활성 FTP 지원은 사용 하지 않도록 설정 됩니다 `PORT` . 그러나 Azure PowerShell, Azure CLI 또는 Azure ARM 템플릿을 사용 하 여 배포할 때 활성 FTP를 사용 하도록 설정할 수 있습니다.


## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell를 사용 하 여 배포 하려면 `AllowActiveFTP` 매개 변수를 사용 합니다. 자세한 내용은 [액티브 FTP를 사용 하 여 방화벽 만들기](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-)를 참조 하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용 하 여 배포 하려면 `--allow-active-ftp` 매개 변수를 사용 합니다. 자세한 내용은 [az network firewall create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters)를 참조 하세요. 

## <a name="azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿

ARM 템플릿을 사용 하 여 배포 하려면 필드를 사용 합니다 `AdditionalProperties` .

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
자세한 내용은 [Microsoft 네트워크 방화벽](/azure/templates/microsoft.network/azurefirewalls)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure 방화벽을 배포 하는 방법에 대 한 자세한 내용은 [Azure PowerShell를 사용 하 여 Azure 방화벽 배포 및 구성](deploy-ps.md)을 참조 하세요.