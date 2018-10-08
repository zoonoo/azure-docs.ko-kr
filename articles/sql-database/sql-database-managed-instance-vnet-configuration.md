---
title: Azure SQL Database Managed Instance VNet 구성 | Microsoft Docs
description: 이 항목에서는 Azure SQL Database Managed Instance를 사용하는 VNet(가상 네트워크)의 구성 옵션에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 9d3f867dad40017e8e97ec4f5e370533b018263c
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181176"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 VNet 구성

Azure SQL Database Managed Instance는 Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에 배포해야 합니다. 이 배포를 사용하면 다음과 같은 시나리오를 수행할 수 있습니다. 
- 온-프레미스 네트워크에서 직접 Managed Instance에 연결 
- 연결된 서버 또는 다른 온-프레미스 데이터 저장소에 Managed Instance 연결 
- Azure 리소스에 Managed Instance 연결  

## <a name="plan"></a>계획

다음과 같은 질문에 대한 답변을 사용하여 가상 네트워크에서 Managed Instance를 배포하는 방법을 계획합니다. 
- 관리되는 단일 또는 여러 인스턴스를 배포할 계획인가요? 

  Managed Instance 수는 Managed Instance에 할당할 서브넷의 최소 크기를 결정합니다. 자세한 내용은 [Managed Instance의 서브넷 크기 결정](#determine-the-size-of-subnet-for-managed-instances)을 참조하세요. 
- Managed Instance를 기존 가상 네트워크에 배포해야 하거나 새 네트워크를 만들려고 합니까? 

   기존 가상 네트워크를 사용하려는 경우 Managed Instance를 수용하도록 해당 네트워크 구성을 수정해야 합니다. 자세한 내용은 [Managed Instance의 기존 가상 네트워크 수정](#modify-an-existing-virtual-network-for-managed-instances)을 참조하세요. 

   새 가상 네트워크를 만들려는 경우 [Managed Instance의 새 가상 네트워크 만들기](#create-a-new-virtual-network-for-a-managed-instance)를 참조하세요.

## <a name="requirements"></a>요구 사항

Managed Instance를 만들려면 다음 요구 사항을 준수하는 가상 네트워크 내에 전용 서브넷(Managed Instance 서브넷)을 만듭니다.
- **전용 서브넷**: Managed Instance 서브넷은 연결된 다른 클라우드 서비스를 포함하지 않아야 하고, 게이트웨이 서브넷이 아니어야 합니다. Managed Instance는 Managed Instance 이외의 리소스가 포함된 서브넷에 만들 수 없으며, 나중에 다른 리소스를 이 서브넷에 추가할 수 없습니다.
- **호환 가능한 NSG(네트워크 보안 그룹)**: Managed Instance 서브넷과 연결되는 NSG에는 다음 표에 나와 있는 규칙(필수 인바운드 및 아웃바운드 보안 규칙)이 다른 규칙 앞에 있어야 합니다. NSG를 사용하여 1433 포트에서 트래픽을 필터링함으로써 Managed Instance 데이터 엔드포인트에 대한 액세스를 완전히 제어할 수 있습니다. 
- **호환 가능한 UDR(사용자 정의 경로) 테이블**: Managed Instance 서브넷에는 **0.0.0.0/0 다음 홉 인터넷**이 필수 UDR로 할당된 사용자 경로 테이블이 있어야 합니다. 또한 온-프레미스 사설 IP 범위가 있는 트래픽을 가상 네트워크 게이트웨이 또는 NVA(가상 네트워크 어플라이언스)를 통해 대상으로 라우팅하는 UDR을 추가할 수 있습니다. 
- **선택적 사용자 지정 DNS**: 사용자 지정 DNS가 가상 네트워크에 지정되면 Azure의 재귀 확인자 IP 주소(예: 168.63.129.16)를 목록에 추가해야 합니다. 자세한 내용은 참조 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요. 사용자 지정 DNS 서버에서 다음 도메인 및 해당 하위 도메인의 호스트 이름을 확인할 수 있어야 합니다. *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* 및 *microsoftonline-p.com* 
- **서비스 엔드포인트 없음**: Managed Instance 서브넷에는 연결된 서비스 엔드포인트가 없어야 합니다. 가상 네트워크를 만들 때 서비스 엔드포인트 옵션을 사용하지 않도록 설정해야 합니다.
- **충분한 IP 주소**: Managed Instance 서브넷에는 16개 이상의 IP 주소가 있어야 합니다(권장되는 최솟값은 32개 IP 주소). 자세한 내용은 [Managed Instance의 서브넷 크기 결정](#determine-the-size-of-subnet-for-managed-instances)을 참조하세요.

> [!IMPORTANT]
> 대상 서브넷이 이러한 모든 요구 사항과 호환되지 않는 경우 새 Managed Instance를 배포할 수 없습니다. Managed Instance가 만들어지면 네트워크 구성에 대한 호환되지 않는 변경을 방지하기 위해 *네트워크 의도 정책*이 서브넷에 적용됩니다. 마지막 인스턴스가 서브넷에서 제거되면 *네트워크 의도 정책*도 제거됩니다

### <a name="mandatory-inbound-security-rules"></a>필수 인바운드 보안 규칙 

| 이름       |포트                        |프로토콜|원본           |대상|작업|
|------------|----------------------------|--------|-----------------|-----------|------|
|관리  |9000, 9003, 1438, 1440, 1452|TCP     |모두              |모두        |허용 |
|mi_subnet   |모두                         |모두     |MI SUBNET        |모두        |허용 |
|health_probe|모두                         |모두     |AzureLoadBalancer|모두        |허용 |

### <a name="mandatory-outbound-security-rules"></a>필수 아웃바운드 보안 규칙 

| 이름       |포트          |프로토콜|원본           |대상|작업|
|------------|--------------|--------|-----------------|-----------|------|
|관리  |80, 443, 12000|TCP     |모두              |모두        |허용 |
|mi_subnet   |모두           |모두     |모두              |MI SUBNET  |허용 |

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Managed Instance의 서브넷 크기 결정

Managed Instance를 만들 때 Azure에서는 프로비전하는 동안 선택한 계층에 따라 가상 머신의 수를 할당합니다. 이러한 가상 머신이 서브넷에 연결되어 있기 때문에 IP 주소가 필요합니다. 일반 작업 및 서비스 유지 관리 중에 고가용성을 보장하기 위해 Azure에서는 가상 머신을 추가로 할당할 수 있습니다. 결과적으로 서브넷의 필수 IP 주소 수는 해당 서브넷의 Managed Instance 수보다 많습니다. 

기본적으로 Managed Instance는 서브넷에서 최소 16개의 IP 주소가 필요하고 최대 256개의 IP 주소를 사용할 수 있습니다. 결과적으로 서브넷 IP 범위를 정의할 때 /28에서 /24 서브넷 마스크를 사용할 수 있습니다. 

> [!IMPORTANT]
> IP 주소가 16개인 서브넷 크기는 추가적인 Managed Instance 스케일 아웃에 대한 가능성이 제한된 최저 수준입니다. 접두사 /27 이하가 지정된 서브넷을 선택하는 것이 좋습니다. 

서브넷 내에서 관리되는 여러 인스턴스를 배포하고 서브넷 크기를 최적화해야 하는 경우 다음과 같은 매개 변수를 사용하여 계산을 만듭니다. 

- Azure는 고유한 요구 사항으로 인해 서브넷에서 5개의 IP 주소를 사용합니다. 
- 범용 인스턴스마다 두 개의 주소가 필요합니다. 
- 각 중요 비즈니스용 인스턴스에는 4개의 주소가 필요합니다.

**예**: 세 개의 범용 및 두 개의 중요 비즈니스용 Managed Instance를 포함하도록 계획합니다. 즉, 5 + 3 * 2 + 2 * 4 = 19개 IP 주소가 필요합니다. IP 범위가 2의 거듭제곱으로 정의되므로 32개(2^5)의 IP 주소라는 IP 범위가 필요합니다. 따라서 /27 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다. 

> [!IMPORTANT]
> 위에 표시된 계산은 추가적인 개선의 등장으로 사용하지 않게 됩니다. 

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>Managed Instance에 대한 새 가상 네트워크 만들기

가상 네트워크를 만들고 구성하는 가장 쉬운 방법은 Azure Resource Manager 배포 템플릿을 사용하는 것입니다.

1. Azure 포털에 로그인합니다.

2. **Azure에 배포** 단추를 사용하여 Azure 클라우드에 가상 네트워크를 배포합니다.

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  이 단추를 사용하면 네트워크 환경을 구성하는 데 사용할 수 있는 양식이 열리는데, 여기서 Managed Instance를 배포할 수 있습니다.

  > [!Note]
  > 이 Azure Resource Manager 템플릿은 두 개의 서브넷이 있는 가상 네트워크를 배포합니다. **ManagedInstances**라는 서브넷은 Managed Instance를 위해 예약되며, 경로 테이블이 미리 구성되어 있지만 **Default**라는 다른 서브넷은 Managed Instance(예: Azure Virtual Machines)에 액세스해야 하는 다른 리소스에 사용됩니다. **Default** 서브넷이 필요하지 않으면 제거할 수 있습니다.

3. 네트워크 환경을 구성합니다. 다음 양식에서 네트워크 환경의 매개 변수를 구성할 수 있습니다.

![Azure 네트워크 구성](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

VNet 및 서브넷의 이름을 변경하고, 네트워킹 리소스에 연결된 IP 범위를 조정할 수 있습니다. "구매" 단추를 누르면 이 양식이 생성되어 환경을 구성합니다. 두 개의 서브넷 필요 없는 경우에는 기본 서브넷을 삭제할 수 있습니다. 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Managed Instance의 기존 가상 네트워크를 수정합니다. 

이 섹션의 질문 및 답변은 기존 가상 네트워크에 Managed Instance를 추가하는 방법을 보여줍니다. 

**기존 가상 네트워크에 클래식 또는 리소스 관리자 배포 모델을 사용하나요?** 

리소스 관리자 가상 네트워크에서 Managed Instance만을 만들 수 있습니다. 

**관리되는 SQL 인스턴스의 새 서브넷을 만들거나 기존 서브넷을 사용하시겠습니까?**

새로 만들려고 하는 경우: 

- [Managed Instance에 대한 서브넷 크기 확인](#determine-the-size-of-subnet-for-managed-instances) 섹션에 있는 지침에 따라 서브넷 크기를 계산합니다.
- [가상 네트워크 서브넷 추가, 변경 또는 삭제](../virtual-network/virtual-network-manage-subnet.md)의 단계를 수행합니다. 
- 다음 홉 인터넷과 같이 단일 항목 **0.0.0.0/0**을 포함하는 경로 테이블을 만들고 Managed Instance의 서브넷과 연결합니다.  

기존 서브넷 내에서 Managed Instance를 만들려는 경우 서브넷을 준비하는 데 다음 PowerShell 스크립트가 권장됩니다.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
서브넷 준비는 간단한 세 단계로 수행됩니다.

- 유효성 검사 - 선택한 가상 네트워크와 서브넷은 Managed Instance 네트워킹 요구 사항에 대해 유효성을 검사합니다.
- 확인 - 사용자에게 Managed Instance 배포를 위해 서브넷을 준비해야 할 일련의 변경 사항이 나타나고 동의를 묻는 메시지가 표시됩니다.
- 준비 - 가상 네트워크 및 서브넷이 제대로 구성됩니다.

**사용자 지정 DNS 서버를 구성했나요?** 

그러한 경우 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- VNet을 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
