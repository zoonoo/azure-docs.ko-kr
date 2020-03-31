---
title: 가용성 그룹 구성(Azure CLI)
description: Azure CLI를 사용하여 Azure의 SQL Server VM에서 Windows 장애 조치 클러스터, 가용성 그룹 수신기 및 내부 로드 밸런서를 만듭니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022359"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure CLI를 사용하여 Azure VM에서 SQL Server에 대한 항상 사용 가능 그룹 구성
이 문서에서는 [Azure CLI를](/cli/azure/sql/vm?view=azure-cli-latest/) 사용하여 Windows 장애 조치 클러스터를 배포하고, 클러스터에 SQL Server VM을 추가하고, Always On 가용성 그룹에 대한 내부 로드 밸런서 및 수신기를 만드는 방법에 대해 설명합니다. Always On 가용성 그룹의 배포는 여전히 Sql Server 관리 스튜디오(SSMS)를 통해 수동으로 수행됩니다. 

## <a name="prerequisites"></a>사전 요구 사항
Azure CLI를 사용하여 Always On 가용성 그룹의 설정을 자동화하려면 다음 필수 구성 조건이 있어야 합니다. 
- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- [SQL VM 리소스 공급자에 등록된](virtual-machines-windows-sql-register-with-resource-provider.md) *동일한 가용성 집합 또는 다른 가용성 영역에서* Azure에서 하나 이상의 도메인 에 가입한 [VM이 SQL Server 2016 이상 또는 이후 엔터프라이즈 버전을 실행합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)  
- [Azure CLI](/cli/azure/install-azure-cli). 
- 사용 가능한 두 개의 IP 주소(엔터티에서 사용되지 않음) IP 주소입니다. 하나는 내부 부하 분산기용입니다. 다른 하나는 가용성 그룹과 동일한 서브넷 내의 가용성 그룹 리스너용입니다. 기존 로드 밸런서를 사용하는 경우 가용성 그룹 리스너에 사용할 수 있는 IP 주소가 하나만 있으면 됩니다. 

## <a name="permissions"></a>사용 권한
Azure CLI를 사용하여 항상 사용 가능 그룹을 구성하려면 다음 계정 권한이 필요합니다. 

- 도메인에 컴퓨터 개체 **만들기** 권한이 있는 기존 도메인 사용자 계정입니다. 예를 들어 도메인 관리자 계정에는 일반적으로 충분한 account@domain.com권한이 있습니다(예: ). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.__
- SQL Server 서비스를 제어하는 도메인 사용자 계정입니다. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>1단계: 클라우드 미러링 모니터 서버로 저장소 계정 만들기
클러스터는 클라우드 감시 자 역할을 하는 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 저장소 계정을 만들 수 있습니다. 기존 저장소 계정을 사용하려면 다음 섹션으로 건너뜁니다. 

다음 코드 조각은 저장소 계정을 만듭니다. 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 오래된 버전의 `az sql: 'vm' is not in the 'az sql' command group` Azure CLI를 사용하는 경우 오류가 표시될 수 있습니다. 이 오류를 지나서 얻으려면 [Azure CLI의 최신 버전을](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 다운로드합니다.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>2단계: Windows 장애 조치 클러스터 메타데이터 정의
Azure CLI [az sql vm 그룹](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) 명령 그룹은 가용성 그룹을 호스트하는 WSFC(Windows 서버 장애 조치 클러스터) 서비스의 메타데이터를 관리합니다. 클러스터 메타데이터에는 Active Directory 도메인, 클러스터 계정, 클라우드 미러링 모니터서버로 사용할 저장소 계정 및 SQL Server 버전이 포함됩니다. [az sql vm 그룹 만들기를](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) 사용하여 첫 번째 SQL Server VM이 추가될 때 정의된 대로 클러스터가 만들어지도록 WSFC에 대한 메타데이터를 정의합니다. 

다음 코드 코드 조각은 클러스터에 대한 메타데이터를 정의합니다.
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>3단계: 클러스터에 SQL 서버 VM 추가
클러스터에 첫 번째 SQL Server VM을 추가하면 클러스터가 만들어집니다. [az sql vm 추가 그룹](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) 명령은 이전에 지정한 이름으로 클러스터를 만들고 SQL Server VM에 클러스터 역할을 설치하고 클러스터에 추가합니다. `az sql vm add-to-group` 명령의 후속 사용은 새로 만든 클러스터에 더 많은 SQL Server VM을 추가합니다. 

다음 코드 조각은 클러스터를 만들고 첫 번째 SQL Server VM을 추가합니다. 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
이 명령을 사용하여 클러스터에 다른 SQL Server VM을 추가합니다. SQL Server `-n` VM 이름에 대한 매개 변수만 수정합니다. 

## <a name="step-4-create-the-availability-group"></a>4단계: 가용성 그룹 만들기
[SQL Server 관리 Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)또는 [Transact-SQL을](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)사용하여 평소와 같이 가용성 그룹을 수동으로 만듭니다. 

>[!IMPORTANT]
> 이 작업은 다음 섹션의 Azure CLI를 통해 수행되므로 현재 수신기를 *만들지* 마십시오.  

## <a name="step-5-create-the-internal-load-balancer"></a>5단계: 내부 부하 분산기 작성

Always On 가용성 그룹 수신기에는 Azure 로드 밸런서의 내부 인스턴스가 필요합니다. 내부 로드 밸런서에서는 가용성 그룹 리스너에게 "부동" IP 주소를 제공하여 장애 조치 및 재연결 속도를 높입니다. 가용성 그룹의 SQL Server VM이 동일한 가용성 집합의 일부인 경우 기본 로드 밸런서를 사용할 수 있습니다. 그렇지 않으면 표준 로드 밸러버를 사용해야 합니다.  

> [!NOTE]
> 내부 로드 밸러블러는 SQL Server VM 인스턴스와 동일한 가상 네트워크에 있어야 합니다. 

다음 코드 조각은 내부 로드 밸러버를 만듭니다.

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 각 SQL Server VM의 공용 IP 리소스에는 표준 로드 밸런서와 호환되는 표준 SKU가 있어야 합니다. VM의 공용 IP 리소스의 SKU를 확인하려면 **리소스 그룹으로**이동하여 원하는 SQL Server VM에 대한 공용 IP **주소** 리소스를 선택하고 **개요** 창에서 **SKU** 아래의 값을 찾습니다.  

## <a name="step-6-create-the-availability-group-listener"></a>6단계: 가용성 그룹 리스너 만들기
가용성 그룹을 수동으로 만든 후 [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)를 사용하여 수신기를 만들 수 있습니다. 

*서브넷 리소스 ID는* 가상 `/subnets/<subnetname>` 네트워크 리소스의 리소스 ID에 추가된 값입니다. 서브넷 리소스 ID를 식별하려면 다음을 수행하십시오.
   1. [Azure 포털의](https://portal.azure.com)리소스 그룹으로 이동합니다. 
   1. 가상 네트워크 리소스를 선택합니다. 
   1. **설정** 창에서 **속성을** 선택합니다. 
   1. 가상 네트워크의 리소스 ID를 식별하고 `/subnets/<subnetname>` 그 끝에 부가하여 서브넷 리소스 ID를 만듭니다. 예를 들어:
      - 가상 네트워크 리소스 ID는 다음과 같은`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 서브넷 이름은 다음과 같은 것입니다.`default`
      - 따라서 서브넷 리소스 ID는 다음과 같은 경우입니다.`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


다음 코드 코드 조각은 가용성 그룹 리스너를 만듭니다.

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>가용성 그룹의 복제본 수 수정
Azure에서 호스팅되는 SQL Server VM에 가용성 그룹을 배포할 때 복잡성이 추가됩니다. 이제 리소스 공급자와 가상 시스템 그룹이 리소스를 관리합니다. 따라서 가용성 그룹에서 복제본을 추가하거나 제거할 때 SQL Server VM에 대한 정보로 수신기 메타데이터를 업데이트하는 추가 단계가 있습니다. 가용성 그룹의 복제본 수를 수정하는 경우 [az sql vm 그룹 ag-listener 업데이트](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) 명령을 사용하여 SQL Server VM의 메타데이터로 리스너를 업데이트해야 합니다. 


### <a name="add-a-replica"></a>복제본 추가

가용성 그룹에 새 복제본을 추가하려면 다음을 수행하십시오.

1. 클러스터에 SQL 서버 VM추가:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. SQL Server 관리 스튜디오를 사용하여 SQL Server 인스턴스를 가용성 그룹 내에서 복제본으로 추가합니다.
1. 수신기에 SQL Server VM 메타데이터를 추가합니다.
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>복제본 제거

가용성 그룹에서 복제본을 제거하려면 다음을 수행하십시오.

1. SQL Server 관리 Studio를 사용하여 가용성 그룹에서 복제본을 제거합니다. 
1. 수신기에서 SQL Server VM 메타데이터를 제거합니다.
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 클러스터에서 SQL 서버 VM을 제거합니다.
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>가용성 그룹 리스너 제거
나중에 Azure CLI로 구성된 가용성 그룹 리스너를 제거해야 하는 경우 SQL VM 리소스 공급자를 거쳐야 합니다. 수신기는 SQL VM 리소스 공급자를 통해 등록되므로 SQL Server 관리 스튜디오를 통해 삭제하는 것만으로는 충분하지 않습니다. 

가장 좋은 방법은 Azure CLI에서 다음 코드 조각을 사용하여 SQL VM 리소스 공급자를 통해 삭제하는 것입니다. 이렇게 하면 SQL VM 리소스 공급자에서 가용성 그룹 리스너 메타 데이터가 제거됩니다. 또한 가용성 그룹에서 수신기를 물리적으로 삭제합니다. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [SQL 서버 VM 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL 서버 VM에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL 서버 VM에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM에 대한 라이선스 모델 전환](virtual-machines-windows-sql-ahb.md)
* [SQL Server &#40;항상 사용 가능 그룹에 대한 개요&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [SQL Server &#40;항상 켜기 가용성 그룹에 대한 서버 인스턴스 구성&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [SQL Server&#41;&#40;가용성 그룹 관리](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [가용성 그룹 모니터링&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [SQL Server&#41;&#40;항상 사용 가능 그룹에 대한 Transact-SQL 문 개요](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [SQL Server&#41;&#40;항상 켜기 가용성 그룹에 대한 PowerShell cmdlet 개요](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
