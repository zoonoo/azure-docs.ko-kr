<properties 
   pageTitle="PowerShell을 사용하여 클래식 모드에서 NSG를 만드는 방법 | Microsoft Azure"
   description="PowerShell을 사용하여 클래식 모드에서 NSG를 만들고 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/29/2015"
   ms.author="telmos" />

# PowerShell에서 NSG(클래식)를 만드는 방법

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 NSG를 만들](virtual-networks-create-nsg-arm-ps.md) 수도 있습니다.

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

아래 샘플 PowerShell 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [VNet을 만들어](virtual-networks-create-vnet-classic-netcfg-ps) 테스트 환경을 구축합니다.

## 프런트 엔드 서브넷에 대한 NSG를 만드는 방법
위의 시나리오에 따라 *NSG-FrontEnd*라는 NSG를 만들려면 다음 단계를 따르세요.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.

3. **NSG-FrontEnd**라는 네트워크 보안 그룹을 만듭니다.

		New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
		    -Label "Front end subnet NSG"

	예상 출력:

		Name         Location   Label               
		----         --------   -----               
		NSG-FrontEnd West US 	Front end subnet NSG


4. 인터넷에서 포트 3389에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name rdp-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
		    -SourceAddressPrefix Internet  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '3389' 

	예상 출력:

		Name     : NSG-FrontEnd
		Location : Central US
		Label    : Front end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           rdp-rule             100       Allow    INTERNET        *             *                3389           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *

4. 인터넷에서 포트 80에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name web-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
		    -SourceAddressPrefix Internet  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '80' 

	예상 출력:
		

		Name     : NSG-FrontEnd
		Location : Central US
		Label    : Front end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           rdp-rule             100       Allow    INTERNET        *             *                3389           TCP     
		           web-rule             200       Allow    INTERNET        *             *                80             TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *   

## 백 엔드 서브넷에 대한 NSG를 만드는 방법
3. **NSG-BackEnd**라는 네트워크 보안 그룹을 만듭니다.

		New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
		    -Label "Back end subnet NSG"

	예상 출력:

		Name        Location   Label              
		----        --------   -----              
		NSG-BackEnd West US    Back end subnet NSG


4. 프런트 엔드 서브넷에서 포트 1433(SQL Server에서 사용되는 기본 포트)에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

		Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
		| Set-AzureNetworkSecurityRule -Name rdp-rule `
		    -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
		    -DestinationAddressPrefix '*' -DestinationPortRange '1433' 

	예상 출력:

		Name     : NSG-BackEnd
		Location : Central US
		Label    : Back end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           fe-rule              100       Allow    192.168.1.0/24  *             *                1433           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *      

4. 서브넷에서 인터넷에 액세스할 수 없도록 차단하는 보안 규칙을 만듭니다.

		Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
		| Set-AzureNetworkSecurityRule -Name block-internet `
		    -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
		    -SourceAddressPrefix '*'  -SourcePortRange '*' `
		    -DestinationAddressPrefix Internet -DestinationPortRange '*' 

	예상 출력:

		Name     : NSG-BackEnd
		Location : Central US
		Label    : Back end subnet NSG
		Rules    : 
		           
		              Type: Inbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           fe-rule              100       Allow    192.168.1.0/24  *             *                1433           TCP     
		           ALLOW VNET INBOUND   65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW AZURE LOAD     65001     Allow    AZURE_LOADBALAN *             *                *              *       
		           BALANCER INBOUND                        CER                                                                   
		           DENY ALL INBOUND     65500     Deny     *               *             *                *              *       
		           
		           
		              Type: Outbound
		           
		           Name                 Priority  Action   Source Address  Source Port   Destination      Destination    Protocol
		                                                   Prefix          Range         Address Prefix   Port Range             
		           ----                 --------  ------   --------------- ------------- ---------------- -------------- --------
		           block-internet       200       Deny     *               *             INTERNET         *              *       
		           ALLOW VNET OUTBOUND  65000     Allow    VIRTUAL_NETWORK *             VIRTUAL_NETWORK  *              *       
		           ALLOW INTERNET       65001     Allow    *               *             INTERNET         *              *       
		           OUTBOUND                                                                                                      
		           DENY ALL OUTBOUND    65500     Deny     *               *             *                *              *   

<!---HONumber=Nov15_HO3-->