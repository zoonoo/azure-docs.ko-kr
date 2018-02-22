---
title: "VNET에 Azure SSIS 통합 런타임 조인 | Microsoft Docs"
description: "Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 72b0965e1fda733651baa04997da1242a73320f1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인
다음 시나리오에서 Azure-SSIS IR(통합 런타임)을 Azure VNet(가상 네트워크)에 조인합니다. 

- VNet의 일부인 SQL Server 관리되는 인스턴스(비공개 미리 보기)에서 SSIS 카탈로그 데이터베이스를 호스팅합니다.
- Azure-SSIS 통합 런타임에서 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결하려고 합니다.

 Azure Data Factory 버전 2(미리 보기)를 사용하면 Azure-SSIS 통합 런타임을 클래식 또는 Azure Resource Manager VNet에 조인할 수 있습니다. 

 > [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="access-on-premises-data-stores"></a>온-프레미스 데이터 저장소 액세스
SSIS 패키지가 공용 클라우드 데이터 저장소에만 액세스하는 경우 Azure-SSIS IR을 VNet에 조인할 필요가 없습니다. SSIS 패키지가 온-프레미스 데이터 저장소에 액세스하는 경우 Azure-SSIS IR을 온-프레미스 네트워크에 연결된 VNet에 조인해야 합니다. 

SSIS 카탈로그가 VNet에 없는 Azure SQL Database에서 호스트되는 경우 적절한 포트를 열어야 합니다. 

SSIS 카탈로그가 VNet에 있는 Azure SQL 관리되는 인스턴스(MI)에 호스팅되는 경우 Azure-SSIS IR을 동일한 VNet (또는) Azure SQL 관리되는 인스턴스가 있는 VNet 간 연결이 있는 다른 VNet에 조인할 수 있습니다. VNet은 클래식 VNet일 수도 있고 Azure Resource 관리 VNet일 수 있습니다. Azure-SSIS IR을 SQL MI가 있는 **동일한 VNet**에 조인하려는 경우 Azure-SSIS IR은 SQL MI가 있는 서브넷과 **다른 서브넷**에 있어야 합니다.   

다음 섹션에 자세한 내용이 제공됩니다.

다음은 몇 가지 유의할 사항입니다. 

- 온-프레미스 네트워크에 연결된 기존 VNet이 없는 경우, 먼저 Azure-SSIS 통합 런타임에서 조인할 [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) 또는 [클래식 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)을 만듭니다. 그런 다음 해당 VNet에서 온-프레미스 네트워크로 사이트 간 [VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 연결을 구성합니다.
- Azure-SSIS IR과 같은 위치에 온-프레미스 네트워크에 연결된 기존 Azure Resource Manager 또는 클래식 VNet이 있는 경우 IR을 해당 VNet에 조인할 수 있습니다.
- Azure-SSIS IR과 다른 위치에 온-프레미스 네트워크에 연결된 기존 클래식 VNet이 있는 경우 먼저 Azure-SSIS IR을 조인할 [클래식 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)을 만듭니다. 그런 다음 [클래식-클래 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 연결을 구성합니다. 또는 Azure-SSIS 통합 런타임에서 조인할 [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network)을 만들 수 있습니다. 그런 다음 [클래식-Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 연결을 구성합니다.
- 온-프레미스 네트워크에 연결된 기존 Azure Resource Manager VNet이 Azure-SSIS IR과 다른 위치에 있는 경우, 먼저 Azure-SSIS IR에서 조인할 [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md##create-a-virtual-network)을 만듭니다. 그런 다음 Azure Resource Manager-Azure Resource Manager VNet 연결을 구성합니다. 또는 Azure-SSIS IR에서 조인할 [클래식 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)을 만들 수 있습니다. 그런 다음 [클래식-Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 연결을 구성합니다.

## <a name="domain-name-services-server"></a>도메인 이름 서비스 서버 
Azure-SSIS 통합 런타임에서 조인한 VNet에서 자체 DNS(도메인 이름 서비스) 서버를 사용해야 하는 경우 지침에 따라 [VNet의 Azure-SSIS 통합 런타임 노드에서 Azure 끝점을 확인할 수 있는지 확인하십시오](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>네트워크 보안 그룹
Azure-SSIS 통합 런타임에서 조인한 VNet에 NSG(네트워크 보안 그룹)를 구현해야 하는 경우 다음 포트를 통해 인바운드/아웃바운드 트래픽을 허용합니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 인바운드 원본/아웃바운드 대상 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100(IR을 VNet에 조인할 경우)<br/><br/>29876, 29877(IR을 Azure Resource Manager VNet에 조인할 경우) | 인바운드 | TCP | Azure 서비스는 이러한 포트를 사용하여 VNet의 Azure-SSIS 통합 런타임 노드와 통신합니다. | 인터넷 | 
| 443 | 아웃바운드 | TCP | VNet의 Azure-SSIS 통합 런타임 노드는 이 포트를 사용하여 Azure 서비스(예: Azure Storage, Event Hub 등)에 액세스합니다. | 인터넷 | 
| 1433<br/>11000-11999<br/>14000-14999  | 아웃바운드 | TCP | VNet의 Azure-SSIS 통합 런타임 노드는 이러한 포트를 사용하여 Azure SQL Database 서버가 호스트하는 SSISDB에 액세스합니다(Azure SQL 관리되는 서비스가 호스트하는 SSISDB에는 해당되지 않음). | 인터넷 | 

## <a name="azure-portal-data-factory-ui"></a>Azure Portal(데이터 팩터리 UI)
이 섹션에서는 Azure Portal과 데이터 팩터리 UI를 사용하여 기존 Azure SSIS 런타임을 VNet(클래식 또는 Azure Resource Manager)에 조인하는 방법을 보여 줍니다. Azure SSIS IR을 VNet에 조인하기 전에 먼저 VNet을 적합하게 구성해야 합니다. VNet(클래식 또는 Azure Resource Manager)의 형식에 따라 다음 두 섹션 중 하나를 진행합니다. 그런 다음, 세 번째 섹션을 진행하여 Azure SSIS IR을 VNet에 조인합니다. 

### <a name="use-portal-to-configure-a-classic-vnet"></a>포털을 사용하여 클래식 VNet 구성
먼저 VNet을 구성해야 Azure-SSIS IR을 VNet에 조인할 수 있습니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com)에 로그인합니다.
3. **추가 서비스**를 클릭합니다. **가상 네트워크(클래식)**를 필터링하여 선택합니다.
4. 목록에서 **가상 네트워크**를 필터링하고 선택합니다. 
5. 가상 네트워크(클래식) 페이지에서 **속성**을 선택합니다. 

    ![클래식 VNet 리소스 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. **RESOURCE ID**에 대한 복사 단추를 클릭하여 클래식 네트워크의 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다.
6. 왼쪽 메뉴에서 **서브넷**을 클릭하고 **사용 가능한 주소**의 수가 Azure-SSIS 통합 런타임의 노드보다 큰지 확인합니다.

    ![VNet에서 사용 가능한 주소 수](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. **MicrosoftAzureBatch**를 VNet의 **클래식 Virtual Machine 참여자** 역할에 조인합니다. 
    1. 왼쪽 메뉴에서 액세스 제어(IAM)를 클릭하고 도구 모음에서 **추가**를 클릭합니다.
    
        ![액세스 제어 -> 추가](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. **권한 추가** 페이지에서 **역할**에 **클래식 Virtual Machine 기여자**를 선택합니다. **선택** 텍스트 상자에 **ddbf3205-c6bd-46ae-8127-60eb93363864**를 복사하여 붙여넣고 검색 결과 목록에서 **Microsoft Azure Batch**를 선택합니다. 
    
        ![권한 추가 - 검색](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. 저장을 클릭하여 설정을 저장하고 페이지를 닫습니다.
    
        ![액세스 설정 저장](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 참여자 목록에 **MicrosoftAzureBatch**가 보이는지 확인합니다.
    
        ![AzureBatch 액세스 확인](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Azure 배치 공급자가 VNet이 있는 Azure 구독에 등록되어 있는지 확인하거나 Azure 배치 공급자를 등록합니다. Azure 배치 계정이 구독에 이미 있는 경우 구독이 Azure 배치에 등록됩니다.
    1. Azure Portal의 왼쪽 메뉴에서 **구독**을 클릭합니다. 
    2. **구독**을 선택합니다. 
    3. 왼쪽에서 **리소스 공급자**를 클릭하고 `Microsoft.Batch`가 등록된 공급자인지 확인합니다. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    `Microsoft.Batch`가 목록에 보이지 않으면 등록을 위해 구독에서 [빈 Azure 배치 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>포털을 사용하여 Azure Resource Manager VNet 구성
먼저 VNet을 구성해야 Azure-SSIS IR을 VNet에 조인할 수 있습니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com)에 로그인합니다.
3. **추가 서비스**를 클릭합니다. **가상 네트워크**를 필터링하여 선택합니다.
4. 목록에서 **가상 네트워크**를 필터링하고 선택합니다. 
5. 가상 네트워크 페이지에서 **속성**을 선택합니다. 
6. **리소스 ID**에 대한 복사 단추를 클릭하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다.
7. 왼쪽 메뉴에서 **서브넷**을 클릭하고 **사용 가능한 주소**의 수가 Azure-SSIS 통합 런타임의 노드보다 큰지 확인합니다.
8. Azure 배치 공급자가 VNet이 있는 Azure 구독에 등록되어 있는지 확인하거나 Azure 배치 공급자를 등록합니다. Azure 배치 계정이 구독에 이미 있는 경우 구독이 Azure 배치에 등록됩니다.
    1. Azure Portal의 왼쪽 메뉴에서 **구독**을 클릭합니다. 
    2. **구독**을 선택합니다. 
    3. 왼쪽에서 **리소스 공급자**를 클릭하고 `Microsoft.Batch`가 등록된 공급자인지 확인합니다. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    `Microsoft.Batch`가 목록에 보이지 않으면 등록을 위해 구독에서 [빈 Azure 배치 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다.

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>Azure SSIS IR을 VNet에 조인


1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **데이터 팩터리**를 선택합니다. 메뉴에 **데이터 팩터리**가 표시되지 않으면 **다른 서비스**를 선택하고 **INTELLIGENCE + ANALYTICS** 섹션에서 **데이터 팩터리**를 선택합니다. 
    
    ![데이터 팩터리 목록](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. 목록에서 Azure SSIS 통합 런타임의 데이터 팩터리를 선택합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. **작성자 및 배포** 타일을 선택합니다. 별도의 탭에서 데이터 팩터리 UI(사용자 인터페이스)를 시작합니다. 

    ![데이터 팩터리 홈페이지](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결**을 선택한 다음, **통합 런타임** 탭으로 전환합니다. 

    ![통합 런타임 탭](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Azure SSIS IR이 실행 중이면 통합 런타임 목록에서 Azure SSIS IR에 대한 **동작** 열의 **중지** 단추를 선택합니다. 중지해야 IR을 편집할 수 있습니다. 

    ![IR 중지](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 통합 런타임 목록에서 Azure SSIS IR에 대한 **동작** 열의 **편집** 단추를 선택합니다.

    ![통합 런타임 편집](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. **통합 런타임 설정**의 **일반 설정** 페이지에서 **다음**을 선택합니다. 

    ![IR 설정 - 일반 설정](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. **SQL 설정** 페이지에서 관리자 **암호**를 입력하고 **다음**을 선택합니다.

    ![IR 설정 - SQL 설정](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. **고급 설정** 페이지에서 다음을 수행합니다. 

    1. **Azure-SSIS Integration Runtime이 조인할 VNet을 선택하고 Azure Services가 VNet 권한/설정을 구성하도록 허용**에 대한 확인란을 선택합니다. 
    2. **형식**에는 Vnet이 클래식 VNet인지 또는 Azure Resource Manager VNet인지 지정합니다. 
    3. **VNet 이름**에는 VNet을 선택합니다.
    4. **서브넷 이름**에는 VNet의 서브넷을 선택합니다. 
    5. **업데이트**를 선택합니다. 

        ![IR 설정 - 고급 설정](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. 이제 Azure SSIS IR에 대한 **동작** 열의 **시작** 단추를 사용하여 IR을 시작할 수 있습니다. Azure SSIS IR을 시작하는 데 약 20분이 걸립니다. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>VNet 구성
먼저 VNet을 구성해야 Azure-SSIS IR을 VNet에 조인할 수 있습니다. 다음 스크립트를 추가하여 VNet을 조인할 Azure-SSIS Integration Runtime에 대한 VNet 권한/설정을 자동으로 구성합니다.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Azure-SSIS IR을 만들어 VNet에 조인
Azure-SSIS IR를 만드는 동시에 VNet에 조인할 수 있습니다. Azure-SSIS IR를 만드는 동시에 VNet에 조인하는 전체 스크립트와 지침은 [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md#azure-powershell)를 참조하세요.

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>기존 Azure-SSIS IR을 VNet에 조인
[Azure-SSIS Integration Runtime 만들기](create-azure-ssis-integration-runtime.md) 문서의 스크립트는 동일한 스크립트에서 Azure-SSIS IR을 만들고 VNet에 조인하는 방법을 보여 줍니다. 기존 Azure-SSIS가 있는 경우에는 다음 단계를 수행하여 VNet에 조인합니다. 

1. Azure-SSIS IR을 중지합니다.
2. VNet에 조인하도록 Azure-SSIS IR을 구성합니다. 
3. Azure-SSIS IR을 시작합니다. 

### <a name="define-the-variables"></a>변수를 정의합니다.

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR 중지
VNet에 조인하려면 먼저 Azure-SSIS Integration Runtime을 중지합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR이 조인하기 위한 VNet 설정을 구성합니다.
Azure Batch 리소스 공급자에 등록합니다.

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR 구성
Set-AzureRmDataFactoryV2IntegrationRuntime 명령을 실행하여 VNet에 조인하도록 Azure-SSIS Integration Runtime을 구성합니다. 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR 시작
다음 명령을 실행하여 Azure-SSIS Integration Runtime을 시작합니다. 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
이 명령은 완료하는 데 **20-30분** 정도 걸립니다.

## <a name="next-steps"></a>다음 단계
Azure-SSIS 런타임에 대한 자세한 내용은 다음 항목을 참조하세요. 

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL 관리되는 인스턴스(비공개 미리 보기)를 사용하고 IR을 VNet에 조인하는 지침을 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보와 반환된 정보의 상태 설명을 검색하는 방법을 설명합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
