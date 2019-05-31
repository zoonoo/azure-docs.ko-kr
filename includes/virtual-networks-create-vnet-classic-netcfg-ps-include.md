---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116904"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>PowerShell에서 네트워크 구성 파일을 사용하여 가상 네트워크를 만드는 방법
Azure에서는 xml 파일을 사용하여 구독에 사용할 수 있는 모든 가상 네트워크를 정의합니다. 이 파일을 다운로드하고, 편집하여 기존 가상 네트워크를 수정 또는 삭제하고 새 가상 네트워크를 만들 수 있습니다. 이 자습서에서는 네트워크 구성(또는 netcfg) 파일이라고 하는 이 파일을 다운로드하고 편집하여 새 가상 네트워크를 만드는 방법을 알아봅니다. 네트워크 구성 파일에 대한 자세한 내용은 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.

PowerShell 사용하여 netcfg 파일을 포함한 가상 네트워크를 만들려면 다음 단계를 완료하세요.

1. Azure PowerShell을 사용한 적이 없는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 문서에 나오는 단계를 완료한 다음 Azure에 로그인하고 구독을 선택합니다.
2. 다음 명령을 실행하여 네트워크 구성 파일을 컴퓨터의 디렉터리에 다운로드하기 위해 Azure PowerShell 콘솔에서 **Get-AzureVnetConfig** cmdlet을 사용합니다. 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   예상 출력:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. 모든 XML 또는 텍스트 편집기 응용 프로그램을 사용 하 여 2 단계에서 저장 한 파일을 열고 검색할 합니다  **\<VirtualNetworkSites >** 요소입니다. 이미 만들어진 네트워크가 있으면 각 네트워크는 자체 표시 됩니다  **\<VirtualNetworkSite >** 요소입니다.
4. 이 시나리오에 설명 된 가상 네트워크를 만들려면 다음 XML을 바로 아래 추가 합니다  **\<VirtualNetworkSites >** 요소:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. 네트워크 구성 파일을 저장합니다.
6. Azure PowerShell 콘솔에서 **Set-AzureVnetConfig** cmdlet을 사용하고 다음 명령을 실행하여 네트워크 구성 파일을 업로드합니다. 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   반환되는 출력:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   반환된 출력에서 **OperationStatus**가 *성공*하지 않으면 오류에 대한 xml 파일을 확인하고 6단계를 완료합니다.

7. Azure PowerShell 콘솔에서 **Get-AzureVnetSite** cmdlet을 사용하고 다음 명령을 실행하여 새 네트워크가 추가되었는지 확인합니다. 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   반환된 (약식) 출력은 다음과 같은 텍스트를 포함합니다.
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
