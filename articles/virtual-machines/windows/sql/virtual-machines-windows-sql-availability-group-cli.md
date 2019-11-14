---
title: 가용성 그룹 구성 (Azure CLI)
description: Azure CLI를 사용 하 여 Azure에서 Windows 장애 조치 (failover) 클러스터, 가용성 그룹 수신기 및 내부 부하 분산 SQL Server VM 장치를 만들 수 있습니다.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022359"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure CLI를 사용 하 여 Azure VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성
이 문서에서는 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) 를 사용 하 여 Windows 장애 조치 (failover) 클러스터를 배포 하 고, 클러스터에 SQL Server vm을 추가 하 고, Always On 가용성 그룹에 대 한 내부 부하 분산 장치 및 수신기를 만드는 방법을 설명 합니다. Always On 가용성 그룹 배포는 SSMS (SQL Server Management Studio)를 통해 여전히 수동으로 수행 됩니다. 

## <a name="prerequisites"></a>선행 조건
Azure CLI를 사용 하 여 Always On 가용성 그룹의 설치를 자동화 하려면 다음 필수 구성 요소가 있어야 합니다. 
- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- *동일한 가용성 집합 또는* [SQL VM 리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md)된 다른 가용성 영역에서 [SQL Server 2016 이상의 Enterprise Edition을 실행 하는 Azure의 도메인에](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 가입 된 vm 하나 이상  
- [Azure CLI](/cli/azure/install-azure-cli) 
- 사용 가능한 두 개의 IP 주소 (엔터티에서 사용 하지 않음)입니다. 하나는 내부 부하 분산 장치에 대 한 것입니다. 다른는 가용성 그룹과 동일한 서브넷에 있는 가용성 그룹 수신기에 대 한 것입니다. 기존 부하 분산 장치를 사용 하는 경우 가용성 그룹 수신기에 대해 사용 가능한 IP 주소가 하나만 필요 합니다. 

## <a name="permissions"></a>권한
Azure CLI를 사용 하 여 Always On 가용성 그룹을 구성 하려면 다음 계정 권한이 필요 합니다. 

- 도메인에 대 한 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정 예를 들어 도메인 관리자 계정에는 일반적으로 충분 한 사용 권한이 있습니다 (예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server 서비스를 제어 하는 도메인 사용자 계정입니다. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>1 단계: 클라우드 감시로 저장소 계정 만들기
클러스터에는 클라우드 감시 역할을 하는 저장소 계정이 필요 합니다. 기존 저장소 계정을 사용 하거나 새 저장소 계정을 만들 수 있습니다. 기존 저장소 계정을 사용 하려는 경우 다음 섹션으로 건너뜁니다. 

다음 코드 조각에서는 저장소 계정을 만듭니다. 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 오래 된 버전의 Azure CLI를 사용 하는 경우 `az sql: 'vm' is not in the 'az sql' command group` 오류가 표시 될 수 있습니다. [최신 버전의 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 을 다운로드 하 여이 오류를 지났습니다.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>2 단계: Windows 장애 조치 (failover) 클러스터 메타 데이터 정의
Azure CLI [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) 명령 그룹은 가용성 그룹을 호스팅하는 WSFC (Windows Server 장애 조치 (Failover) 클러스터) 서비스의 메타 데이터를 관리 합니다. 클러스터 메타 데이터에는 Active Directory 도메인, 클러스터 계정, 클라우드 감시로 사용할 저장소 계정 및 SQL Server 버전이 포함 됩니다. [Az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) 를 사용 하 여 WSFC에 대 한 메타 데이터를 정의 합니다. 그러면 첫 번째 SQL Server VM 추가 될 때 클러스터가 정의 된 대로 만들어집니다. 

다음 코드 조각에서는 클러스터에 대 한 메타 데이터를 정의 합니다.
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>3 단계: 클러스터에 SQL Server Vm 추가
클러스터에 첫 번째 SQL Server VM을 추가 하면 클러스터가 만들어집니다. [Az sql vm 추가-그룹](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) 명령은 이전에 지정한 이름으로 클러스터를 만들고 SQL Server vm에 클러스터 역할을 설치 하 여 클러스터에 추가 합니다. 이후에 `az sql vm add-to-group` 명령을 사용 하 여 새로 만든 클러스터에 더 많은 SQL Server Vm을 추가 합니다. 

다음 코드 조각은 클러스터를 만들고 여기에 첫 번째 SQL Server VM를 추가 합니다. 

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
이 명령을 사용 하 여 클러스터에 다른 SQL Server Vm을 추가 합니다. SQL Server VM 이름에 대 한 `-n` 매개 변수만 수정 합니다. 

## <a name="step-4-create-the-availability-group"></a>4 단계: 가용성 그룹 만들기
일반적으로 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)또는 [transact-sql](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)을 사용 하 여 가용성 그룹을 수동으로 만듭니다. 

>[!IMPORTANT]
> 이는 다음 섹션의 Azure CLI를 통해 수행 되므로 수신기를 *만들지 마십시오.*  

## <a name="step-5-create-the-internal-load-balancer"></a>5 단계: 내부 부하 분산 장치 만들기

Always On 가용성 그룹 수신기에는 Azure Load Balancer의 내부 인스턴스가 필요 합니다. 내부 부하 분산 장치는 더 빠른 장애 조치 (failover) 및 다시 연결을 허용 하는 가용성 그룹 수신기에 대 한 "부동" IP 주소를 제공 합니다. 가용성 그룹의 SQL Server Vm이 동일한 가용성 집합의 일부인 경우 기본 부하 분산 장치를 사용할 수 있습니다. 그렇지 않으면 표준 부하 분산 장치를 사용 해야 합니다.  

> [!NOTE]
> 내부 부하 분산 장치는 SQL Server VM 인스턴스와 동일한 가상 네트워크에 있어야 합니다. 

다음 코드 조각에서는 내부 부하 분산 장치를 만듭니다.

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 각 SQL Server VM에 대 한 공용 IP 리소스에 표준 부하 분산 장치와 호환 되는 표준 SKU가 있어야 합니다. VM의 공용 IP 리소스의 SKU를 확인 하려면 **리소스 그룹**으로 이동 하 여 원하는 SQL Server VM에 대 한 **공용 ip 주소** 리소스를 선택 하 고 **개요** 창의 **SKU** 아래에서 값을 찾습니다.  

## <a name="step-6-create-the-availability-group-listener"></a>6 단계: 가용성 그룹 수신기 만들기
수동으로 가용성 그룹을 만든 후에는 [az sql vm ag 수신기](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)를 사용 하 여 수신기를 만들 수 있습니다. 

*서브넷 리소스 id* 는 가상 네트워크 리소스의 리소스 id에 추가 된 `/subnets/<subnetname>`의 값입니다. 서브넷 리소스 ID를 확인 하려면:
   1. [Azure Portal](https://portal.azure.com)의 리소스 그룹으로 이동 합니다. 
   1. 가상 네트워크 리소스를 선택 합니다. 
   1. **설정** 창에서 **속성** 을 선택 합니다. 
   1. 가상 네트워크의 리소스 ID를 확인 하 고 그 끝에 `/subnets/<subnetname>`을 추가 하 여 서브넷 리소스 ID를 만듭니다. 예:
      - 가상 네트워크 리소스 ID: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 서브넷 이름은 `default`입니다.
      - 따라서 서브넷 리소스 ID는 다음과 같습니다 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


다음 코드 조각에서는 가용성 그룹 수신기를 만듭니다.

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
Azure에서 호스트 되는 SQL Server Vm에 가용성 그룹을 배포 하는 경우에는 복잡 한 계층이 추가 됩니다. 리소스 공급자와 가상 컴퓨터 그룹은 이제 리소스를 관리 합니다. 따라서 가용성 그룹의 복제본을 추가 하거나 제거 하는 경우 SQL Server Vm에 대 한 정보를 사용 하 여 수신기 메타 데이터를 업데이트 하는 추가 단계가 있습니다. 가용성 그룹의 복제본 수를 수정 하는 경우 [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) 명령을 사용 하 여 SQL Server vm의 메타 데이터로 수신기를 업데이트 해야 합니다. 


### <a name="add-a-replica"></a>복제본 추가

가용성 그룹에 새 복제본을 추가 하려면 다음을 수행 합니다.

1. 클러스터에 SQL Server VM를 추가 합니다.
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. SQL Server Management Studio를 사용 하 여 가용성 그룹 내의 복제본으로 SQL Server 인스턴스를 추가 합니다.
1. 수신기에 SQL Server VM 메타 데이터를 추가 합니다.
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>복제본 제거

가용성 그룹에서 복제본을 제거 하려면 다음을 수행 합니다.

1. SQL Server Management Studio를 사용 하 여 가용성 그룹에서 복제본을 제거 합니다. 
1. 수신기에서 SQL Server VM 메타 데이터를 제거 합니다.
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 클러스터에서 SQL Server VM를 제거 합니다.
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>가용성 그룹 수신기 제거
나중에 Azure CLI로 구성 된 가용성 그룹 수신기를 제거 해야 하는 경우에는 SQL VM 리소스 공급자를 사용 해야 합니다. 수신기는 SQL VM 리소스 공급자를 통해 등록 되기 때문에 SQL Server Management Studio를 통해 삭제 하는 것 만으로는 충분 하지 않습니다. 

가장 좋은 방법은 Azure CLI에서 다음 코드 조각을 사용 하 여 SQL VM 리소스 공급자를 통해 삭제 하는 것입니다. 이렇게 하면 SQL VM 리소스 공급자에서 가용성 그룹 수신기 메타 데이터가 제거 됩니다. 또한 가용성 그룹에서 수신기를 물리적으로 삭제 합니다. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [SQL Server Vm 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Vm에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server Vm에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM에 대한 라이선스 모델 전환](virtual-machines-windows-sql-ahb.md)
* [Always On 가용성 그룹 &#40;SQL Server 개요&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always On 가용성 그룹 &#40;SQL Server에 대 한 서버 인스턴스 구성&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [가용성 그룹 &#40;의 관리 SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [가용성 그룹 &#40;SQL Server 모니터링&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always On 가용성 그룹 &#40;SQL Server에 대 한 transact-sql 문 개요&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 가용성 그룹 &#40;에 대 한 PowerShell cmdlet 개요 SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
