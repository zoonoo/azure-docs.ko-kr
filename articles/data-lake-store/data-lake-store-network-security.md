---
title: Azure Data Lake Storage Gen1의 네트워크 보안 | Microsoft Docs
description: Azure Data Lake Storage Gen1에서 IP 방화벽 및 가상 네트워크 통합이 어떻게 작동하는지 이해
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168196"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Azure Data Lake Storage Gen1에 대한 가상 네트워크 통합 - 미리 보기

Azure Data Lake Storage Gen1에 대한 가상 네트워크 통합을 소개합니다(미리 보기). VNet 통합을 사용하면 계정을 특정 가상 네트워크와 서브넷으로 잠그는 방식으로 Azure Data Lake Storage Gen1에 대한 무단 액세스를 방지할 수 있습니다. 이제 지정한 가상 네트워크 및 서브넷으로부터의 트래픽만 수신하고 나머지 액세스는 차단하도록 ADLS Gen1 계정을 구성할 수 있습니다. 이렇게 하면 ADLS 계정의 외부 위협으로부터 보호할 수 있습니다.

ADLS Gen1에 대한 VNet 통합에서는 가상 네트워크와 Azure Active Directory 서비스 간에 가상 네트워크 서비스 엔드포인트 보안을 사용하여 액세스 토큰에서 추가 보안 클레임을 생성합니다. 그런 다음, 이러한 클레임을 사용하여 ADLS Gen1 계정에 대해 가상 네트워크를 인증하고 액세스를 허용합니다.

> [!NOTE]
> 미리 보기 기술이며 프로덕션 환경에 사용하는 것은 권장되지 않습니다.
>
> 이 기능 사용과 관련한 추가 비용은 없습니다. 계정에는 ADLS Gen1([가격 책정](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) 및 사용하는 모든 Azure 서비스([가격 책정](https://azure.microsoft.com/pricing/#product-picker))에 대한 표준 요금이 청구됩니다.

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>ADLS Gen1에 대한 VNET 통합 시나리오

ADLS Gen1 VNet 통합을 사용하면 Gen1 ADLS 계정에 대한 액세스를 지정된 가상 네트워크 및 서브넷으로 제한할 수 있습니다.  특정 VNet 서브넷으로 잠근 후에는 그 외의 Azure VNet/VM이 계정에 액세스할 수 없습니다.  기능적으로 ADLS Gen1 VNet 통합을 통해 같은 시나리오를 [가상 네트워크 서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)로 사용할 수 있습니다.  아래 섹션에서는 몇 가지 주요 차이점을 자세히 설명합니다. 

![ADLS Gen1 VNet 통합에 대한 시나리오 다이어그램](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> 기존 IP 방화벽 규칙을 VNet 규칙과 함께 사용하여 온-프레미스 네트워크로부터의 액세스도 허용할 수 있습니다. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>ADLS Gen1 VNet 통합에서의 최적 라우팅

VNet 서비스의 주요 이점은 가상 네트워크로부터의 [최적 라우팅](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits)이란 것입니다.  ADLS Gen1 계정에 대해 동일한 경로 최적화를 수행하려면 VNet에서 ADLS Gen1 계정으로 다음 [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)를 사용합니다.

- **ADLS 공용 IP 주소** – 대상 ADLS Gen1 계정에 공용 IP 주소를 사용합니다.  계정의 [DNS 이름을 확인](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)하여 ADLS Gen1 계정의 IP 주소를 식별할 수 있습니다.  각 주소에 대해 별도의 항목을 만듭니다.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>고객 VNet으로부터 데이터 반출

가상 네트워크로부터의 액세스로부터 ADSL 계정을 보호하는 것 외에도 무단 계정에 대한 반출을 없게 하는 것도 중요합니다.

VNet에서 방화벽 솔루션을 사용하여 대상 계정 URL에 따라 아웃바운드 트래픽을 필터링하고 권한 있는 ADLS Gen1 계정에 대해서만 액세스를 허용하는 것이 좋습니다.

사용 가능한 옵션은 다음과 같습니다.
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview): VNet에 대해 [Azure Firewall을 배포 및 구성](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)하고 아웃바운드 ADSL 트래픽을 보호하며 알고 있고 권한 있는 계정 URL로 잠글 수 있습니다.
- [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 방화벽: 관리자가 특정 상용 방화벽 공급업체의 사용만 승인한 경우 Azure Marketplace에서 제공하는 NVA 방화벽 솔루션을 통해 같은 기능을 수행할 수 있습니다.

> [!NOTE]
> 데이터 경로에서 방화벽을 사용하면 데이터 경로에 홉을 추가하게 되며 엔드투엔드 데이터 교환에 성능 문제를 초래할 수 있습니다. 여기에는 가용 처리량 및 연결 대기 시간 등이 해당합니다. 

## <a name="limitations"></a>제한 사항
1.  미리 보기에 추가한 후에는 HDInsight 클러스터를 새로 만들어야 합니다.  ADLS Gen1 VNet 통합 지원이 제공되기 전에 만들어진 클러스터의 경우 이 기능을 지원하려면 다시 만들어야 합니다. 
2.  새 HDInsight 클러스터를 만들 때 VNet 통합을 사용하도록 설정된 ADLS Gen1 계정을 선택하면 프로세스가 실패합니다. ADSL 계정의 **방화벽 및 가상 네트워크** 블레이드를 통해 **모든 네트워크 및 서비스에서 액세스를 허용**할 수 있습니다.  자세한 내용은 [예외](##Exceptions) 섹션을 참조하세요.
3.  ADLS Gen1 VNet 통합 미리 보기는 [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에서 작동하지 않습니다.  
4.  VNet을 사용하도록 설정된 ADLS Gen1 계정의 파일/폴더 데이터는 포털에서 액세스할 수 없습니다.  VNET 안에 있는 VM과 데이터 탐색기 사용 등의 작업으로부터의 액세스도 여기에 포함됩니다.  계정 관리 작업은 계속 작동합니다.  VNet을 사용하도록 설정된 ADLS 계정의 파일/폴더 데이터는 모든 포털 이외 리소스를 통해 액세스할 수 있습니다. 여기에는 SDK 액세스, PowerShell 스크립트, 기타 Azure 서비스(출처가 포털이 아닌 경우)가 해당됩니다. 

## <a name="configuration"></a>구성

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>1단계: AAD 서비스 엔드포인트를 사용하도록 VNET 구성
1.  Azure Portal을 열고 계정에 로그인합니다. 
2.  구독에서 [새 가상 네트워크를 만들거나](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) 기존 가상 네트워크로 이동합니다.  VNET은 현재 ADLS Gen 1 계정과 동일한 지역에 있어야 합니다. 
3.  가상 네트워크 블레이드에서 **서비스 엔드포인트**를 선택합니다. 
4.  **추가**를 클릭하여 새 서비스 엔드포인트를 추가합니다.
![VNet 서비스 엔드포인트 추가](media/data-lake-store-network-security/config-vnet-1.png)
5.  **Microsoft.AzureActiveDirectory**를 엔드포인트의 서비스로 선택합니다.
![Microsoft.AzureActiveDirectory 서비스 엔드포인트 선택](media/data-lake-store-network-security/config-vnet-2.png)
6.  연결을 허용하려면 연결을 허용하려는 서브넷을 선택하고 **추가**를 클릭합니다.
![서브넷 선택](media/data-lake-store-network-security/config-vnet-3.png)
7.  서비스 엔드포인트 추가에 최대 15분이 소요될 수 있습니다. 추가되면 목록에 나타납니다. 목록에 표시되며 모든 세부 정보가 구성된 대로인지 확인합니다. 
![서비스 엔드포인트 추가 성공](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>2단계: ADLS Gen1 계정에 대해 허용된 VNET/서브넷 설정
1.  VNET을 구성한 후 구독에서 [새 Azure Data Lake 저장소 Gen1 계정을 만들거나](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) 기존 Gen1 ADLS 계정으로 이동합니다. ADLS Gen1 계정은 현재 VNET과 동일한 지역에 있어야 합니다. 
2.  **방화벽 및 가상 네트워크**를 선택합니다.

  > [!NOTE]
  > **방화벽 및 가상 네트워크**가 설정에 표시되지 않으면 포털에서 로그오프합니다. 브라우저를 닫습니다. 브라우저 캐시를 지웁니다. 머신을 다시 시작하고 다시 시도합니다.

  ![ADLS 계정에 VNet 규칙 추가](media/data-lake-store-network-security/config-adls-1.png)
3.  **선택한 네트워크**를 선택합니다. 
4.  **기존 가상 네트워크 선택**을 클릭합니다.
  ![기존 가상 네트워크 추가](media/data-lake-store-network-security/config-adls-2.png)
5.  VNet과 서브넷을 선택하여 연결을 허용한 다음, **추가**를 클릭합니다.
  ![VNet 및 서브넷 선택](media/data-lake-store-network-security/config-adls-3.png)
6.  목록에 VNet 및 서브넷이 올바르게 표시되는지 확인하고 **저장**을 클릭합니다.
  ![새 규칙 저장](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > 설정을 저장한 후 적용되는 데는 최대 5분이 소요될 수 있습니다.

7.  [선택 사항] VNet과 서브넷 외에도 특정 IP 주소에서의 연결을 허용하려면 같은 페이지의 **방화벽** 섹션에서 그렇게 할 수 있습니다. 

## <a name="exceptions"></a>예외
**방화벽 및 가상 네트워크** 블레이드의 예외 영역에는 Azure의 서비스 집합과 가상 머신으로부터의 연결을 사용하도록 설정하는 두 확인란이 있습니다.
![방화벽 및 가상 네트워크 예외](media/data-lake-store-network-security/firewall-exceptions.png)
- **모든 Azure 서비스가 이 Data Lake Storage Gen1 계정에 액세스하도록 허용**은 Azure Data Factory, Event Hubs, 모든 Azure VM 등, 모든 Azure 서비스가 ADLS 계정과 통신할 수 있게 허용합니다.

- **Azure Data Lake Analytics가 이 Data Lake Storage Gen1 계정에 액세스하도록 허용**은 Azure Data Lake Analytics 서버의 이 ADSL 계정 연결을 허용합니다. 

이러한 예외는 해제해 두고 VNet 외부에서 해당 서비스에 대한 연결이 필요한 경우에만 사용하는 것이 좋습니다.
