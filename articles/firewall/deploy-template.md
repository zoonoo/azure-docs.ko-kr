---
title: 템플릿을 사용하여 Azure Firewall 배포
description: 템플릿을 사용하여 Azure Firewall 배포
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: 86fdbbacf3e8064afe0aaaaebea1d6ef6c25f9d4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865835"
---
# <a name="deploy-azure-firewall-using-a-template"></a>템플릿을 사용하여 Azure Firewall 배포

[AzureFirewall 샌드박스 설정 만들기 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)은 방화벽을 사용하여 테스트 네트워크 환경을 만듭니다. 네트워크에는 다음 세 개의 서브넷이 있는 하나의 VNet(가상 네트워크)이 포함됩니다. *AzureFirewallSubnet*, *ServersSubnet* 및 *JumpboxSubnet*. *ServersSubnet* 및 *JumpboxSubnet* 서브넷에는 각각 하나의 2개 코어 Windows Server 가상 머신이 있습니다.

방화벽은 *AzureFirewallSubnet* 서브넷에 있으며, *www.microsoft.com*에 대한 액세스를 허용하는 단일 규칙이 포함된 애플리케이션 규칙 컬렉션이 포함됩니다.

사용자 정의 경로는 방화벽 규칙이 적용된 방화벽을 통해 *ServersSubnet* 서브넷에서의 네트워크 트래픽을 가리킵니다.

Azure Firewall에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조하세요.

## <a name="use-the-template-to-deploy-azure-firewall"></a>템플릿을 사용하여 Azure Firewall 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

**템플릿을 사용하여 Azure Firewall을 설치 및 배포하려면**

1. [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)에서 템플릿에 액세스합니다.
   
1. 소개 부분을 읽어보고 배포할 준비가 되면 **Azure에 배포**를 선택합니다.
   
1. 필요한 경우 Azure Portal에 로그인합니다. 

1. 포털의 **AzureFirewall의 샌드박스 설정 만들기** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력하고 **확인**을 선택합니다. 
   - **가상 네트워크 이름**: 새 VNet의 이름을 입력합니다. 
   - **관리자 사용자 이름**: 관리자 사용자 계정에 대한 사용자 이름을 입력합니다.
   - **관리자 암호**: 관리자 암호를 입력합니다. 
   
1. 사용 약관을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.
   
1. **구매**를 선택합니다.
   
   리소스를 만드는 데 몇 분이 걸립니다. 
   
1. 방화벽을 통해 만들어진 리소스를 검색합니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) PowerShell 명령을 실행하여 리소스 그룹, 방화벽 및 모든 관련 리소스를 제거할 수 있습니다. *MyResourceGroup*이라는 리소스 그룹을 제거하려면 다음을 실행합니다. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyResourceGroup
```
방화벽 모니터링 자습서를 계속 진행하려면 아직 리소스 그룹과 방화벽을 제거하지 마세요. 

## <a name="next-steps"></a>다음 단계

다음으로, Azure Firewall 로그를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
