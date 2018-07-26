---
title: 템플릿을 사용하여 Azure Firewall 배포
description: 템플릿을 사용하여 Azure Firewall 배포
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992513"
---
# <a name="deploy-azure-firewall-using-a-template"></a>템플릿을 사용하여 Azure Firewall 배포

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure Firewall 문서의 예제에서는 이미 Azure Firewall 공개 미리 보기를 사용하도록 설정했다고 가정합니다. 자세한 내용은 [Azure Firewall 공개 미리 보기 사용하도록 설정](public-preview.md)을 참조합니다.

이 템플릿은 방화벽 및 테스트 네트워크 환경을 만듭니다. 네트워크에는 *AzureFirewallSubnet*, *ServersSubnet* 및 *JumpboxSubnet*이라는 세 개의 서브넷이 포함된 VNet이 하나 있습니다. ServersSubnet 및 JumpboxSubnet 각각에는 2코어 Windows Server 하나씩 있습니다.

방화벽은 AzureFirewallSubnet에 있으며, www.microsoft.com에 대한 액세스를 허용하는 단일 규칙이 포함된 응용 프로그램 규칙 컬렉션으로 구성됩니다.

방화벽 규칙이 적용된 방화벽을 통해 ServersSubnet에서 네트워크 트래픽을 가리키는 사용자 정의 경로가 만들어집니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="template-location"></a>템플릿 위치

템플릿의 위치는 다음과 같습니다.

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

소개 부분을 읽어보고 배포할 준비가 되면 **Azure에 배포**를 클릭하십시오.

## <a name="clean-up-resources"></a>리소스 정리

먼저 방화벽으로 만든 리소스를 탐색한 다음, 더 이상 필요하지 않으면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, 방화벽 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>다음 단계

다음으로, Azure Firewall 로그를 모니터링할 수 있습니다.

- [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)

