---
title: Azure 하이브리드 혜택 및 Linux Vm
description: Azure에서 실행 되는 Linux 가상 머신에 대 한 비용을 절감 하는 데 도움이 되 Azure 하이브리드 혜택 방법을 알아보세요.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 73747222b9131fa85ae6ac01c9dedd5b0bbe1d63
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543411"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Linux 가상 머신에 Azure 하이브리드 혜택 적용 되는 방법

Azure 하이브리드 혜택는 클라우드에서 Red Hat Enterprise Linux (RHEL) 및 SUSE Linux Enterprise Server (Vm) Vm (가상 머신)을 실행 하는 비용을 크게 줄일 수 있는 라이선스 혜택입니다. 이 혜택을 통해 RHEL 또는 SLES 구독이 소프트웨어 요금을 포함 하기 때문에 VM의 인프라 비용에 대해서만 비용을 지불 합니다. 이 혜택은 모든 RHEL 및 SLES Marketplace PAYG (종 량 제) 이미지에 사용할 수 있습니다.

Linux Vm에 대 한 Azure 하이브리드 혜택는 이제 공개적으로 사용할 수 있습니다.

## <a name="benefit-description"></a>혜택 설명

Azure 하이브리드 혜택를 통해 Azure에서 기존 RHEL 및 SLES PAYG Vm을 변환 하 여 BYOS (사용자 소유의 구독) 요금 청구를 통해 온-프레미스 RHEL 및 SLES 서버를 Azure로 마이그레이션할 수 있습니다. 일반적으로 Azure의 PAYG 이미지에서 배포 된 Vm은 인프라 요금 및 소프트웨어 요금을 모두 청구 합니다. Azure 하이브리드 혜택를 사용 하 여 PAYG Vm을 다시 배포 하지 않고 BYOS 청구 모델로 변환할 수 있으므로 가동 중지 위험을 피할 수 있습니다.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Linux Vm에 대 한 비용 시각화를 Azure 하이브리드 혜택 합니다.":::

RHEL 또는 SLES VM에서 혜택을 사용 하도록 설정한 후에는 일반적으로 PAYG VM에서 발생 하는 추가 소프트웨어 요금에 대 한 요금이 더 이상 청구 되지 않습니다. 대신 VM은 계산 하드웨어 요금 및 소프트웨어 요금은 포함 하지 않는 BYOS 요금 발생 시작 합니다.

혜택을 사용 하도록 설정 된 VM을 PAYG 청구 모델로 변환 하도록 선택할 수도 있습니다.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux Vm에 대 한 Azure 하이브리드 혜택 자격의 범위

Azure 하이브리드 혜택은 Azure Marketplace의 모든 RHEL 및 SLES PAYG 이미지에 사용할 수 있습니다. RHEL 또는 SLES BYOS 이미지 또는 Azure Marketplace의 사용자 지정 이미지에 대 한 혜택은 아직 제공 되지 않습니다.

Linux Vm에서 혜택을 이미 사용 하 고 있는 경우 Azure 전용 호스트 인스턴스 및 SQL 하이브리드 혜택을 Azure 하이브리드 혜택 수 없습니다.

## <a name="get-started"></a>시작하기

### <a name="red-hat-customers"></a>Red Hat 고객

RHEL에 대 한 Azure 하이브리드 혜택는 다음 조건을 모두 충족 하는 Red Hat 고객에 게 제공 됩니다.

- Azure에서 사용할 수 있는 활성 또는 사용 하지 않는 RHEL 구독 보유
- [Red Hat 클라우드 액세스](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 프로그램을 사용 하 여 Azure에서 사용 하기 위해 이러한 구독을 하나 이상 사용 하도록 설정 했습니다.

> [!IMPORTANT]
> [클라우드 액세스](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 프로그램에서 올바른 구독을 사용 하도록 설정 했는지 확인 합니다.

Red Hat의 혜택 사용을 시작 하려면 다음을 수행 합니다.

1. [Red Hat Cloud Access customer 인터페이스](https://access.redhat.com/management/cloud)를 사용 하 여 Azure에서 사용 하기에 적합 한 RHEL 구독을 하나 이상 사용 하도록 설정 합니다.

   Red Hat Cloud Access 사용 프로세스 중에 제공 하는 Azure 구독은 Azure 하이브리드 혜택 기능을 사용 하도록 허용 됩니다.
1. 기존 RHEL PAYG Vm 및 Azure Marketplace PAYG 이미지에서 배포 하는 모든 새 RHEL Vm에 Azure 하이브리드 혜택을 적용 합니다. Azure Portal 또는 Azure CLI를 사용 하 여 혜택을 사용 하도록 설정할 수 있습니다.
1. RHEL Vm에 대 한 업데이트 원본을 구성 하는 데 권장 되는 [다음 단계](https://access.redhat.com/articles/5419341) 와 RHEL 구독 준수 지침을 따릅니다.


### <a name="suse-customers"></a>SUSE 고객

SUSE에 대 한 혜택 사용을 시작 하려면 다음을 수행 합니다.

1. SUSE 공용 클라우드 프로그램에 등록 합니다.
1. Azure Portal 또는 Azure CLI를 통해 새로 만들었거나 기존 Vm에 혜택을 적용 합니다.
1. 별도의 업데이트 원본으로 혜택을 받는 Vm을 등록 합니다.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Azure Portal 혜택 사용 및 사용 안 함

왼쪽의 **구성** 옵션을 방문 하 고 해당 단계를 수행 하 여 기존 vm에 대 한 혜택을 사용 하도록 설정할 수 있습니다. VM 만들기 환경에서 새 Vm에 대 한 혜택을 사용 하도록 설정할 수 있습니다.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>예를 Azure Portal 하 여 기존 VM에 대 한 혜택을 사용 하도록 설정 합니다.
1. [Microsoft Azure portal](https://portal.azure.com/) 방문
1. 포털에서 ' 가상 머신 만들기 ' 페이지로 이동 합니다.
 ![VM을 만드는 동안 AHB](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. AHB 변환을 사용 하도록 설정 하 고 클라우드 액세스 라이선스를 사용 하려면이 확인란을 클릭 합니다.
 ![VM을 만드는 동안 AHB 확인란](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. 다음 명령 집합에 따라 가상 머신 만들기
1. **구성** 블레이드를 확인 하면 사용 옵션이 표시 됩니다. 
![만든 후 AHB 구성 블레이드](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>VM을 만드는 동안 혜택을 사용 하도록 설정 하는 예제를 Azure Portal 합니다.
1. [Microsoft Azure portal](https://portal.azure.com/) 방문
1. 변환을 적용 하려는 가상 컴퓨터 페이지를 엽니다.
1. 왼쪽의 **구성** 옵션으로 이동 합니다. 라이선스 섹션이 표시 됩니다. AHB 변환을 사용 하도록 설정 하려면 ' 예 ' 라디오 단추를 선택 하 고 확인 확인란을 선택 합니다.
![만든 후 AHB 구성 블레이드](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> RHEL 또는 SLES PAYG Marketplace 이미지의 **사용자 지정 스냅숏** 또는 **SIG (공유 이미지)** 를 만든 경우 Azure 하이브리드 혜택를 사용 하도록 설정 하는 데 Azure CLI만 사용할 수 있습니다. 이는 알려진 제한 사항입니다. 현재 Azure Portal 에서도이 기능을 제공할 타임 라인이 없습니다.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Azure CLI 혜택 사용 및 사용 안 함

명령을 사용 하 여 `az vm update` 기존 vm을 업데이트할 수 있습니다. RHEL Vm의 경우 매개 변수를 사용 하 여 명령을 실행 합니다 `--license-type` `RHEL_BYOS` . SLES Vm의 경우 매개 변수를 사용 하 여 명령을 실행 합니다 `--license-type` `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>혜택을 사용 하도록 설정 하는 CLI 예제
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>혜택을 사용 하지 않도록 설정 하는 CLI 예제
이 혜택을 사용 하지 않도록 설정 하려면 다음 값을 사용 합니다 `--license-type` `None` .

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>많은 수의 Vm에서 혜택을 사용 하도록 설정 하는 CLI 예제
많은 수의 Vm에 대 한 혜택을 사용 하도록 설정 하려면 `--ids` Azure CLI에서 매개 변수를 사용할 수 있습니다.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

다음 예제에서는 리소스 그룹 수준에서 하나, 구독 수준에서 리소스 Id 목록을 가져오는 두 가지 방법을 보여 줍니다.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>VM 만들기 시간에 Azure 하이브리드 혜택 적용
기존 종 량 제 Vm에 Azure 하이브리드 혜택를 적용 하는 것 외에 VM을 만들 때이를 호출할 수 있습니다. 이렇게 하면 다음과 같은 이점이 증가량 됩니다.
- 동일한 이미지와 프로세스를 사용 하 여 PAYG 및 BYOS Vm을 둘 다 프로 비전 할 수 있습니다.
- 이후 라이선스 모드를 변경 하거나, BYOS 전용 이미지에서 사용할 수 없는 경우 또는 사용자 고유의 VM을 가져올 수 있습니다.
- VM은 기본적으로 RHUI (Red Hat Update Infrastructure)에 연결 되어 최신 상태를 유지 하 고 안전 하 게 유지 됩니다. 언제 든 지 배포 후 업데이트 된 메커니즘을 변경할 수 있습니다.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>VM의 Azure 하이브리드 혜택 상태를 확인 합니다.
Azure CLI를 사용 하거나 Azure Instance Metadata Service를 사용 하 여 VM의 Azure 하이브리드 혜택 상태를 볼 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

이를 위해 명령을 사용할 수 있습니다 `az vm get-instance-view` . `licenseType`응답에서 필드를 찾습니다. `licenseType`필드가 존재 하 고 값이 또는 이면 `RHEL_BYOS` `SLES_BYOS` VM에서 혜택을 사용할 수 있습니다.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

VM 자체 내에서 Azure Instance Metadata Service의 증명 된 메타 데이터를 쿼리하여 VM의 값을 확인할 수 있습니다 `licenseType` . `licenseType`또는 값은 `RHEL_BYOS` `SLES_BYOS` VM이 사용 하도록 설정 된 것을 의미 합니다. [증명 된 메타 데이터에 대해 자세히 알아보세요](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>규정 준수

### <a name="red-hat"></a>Red Hat

RHEL에 대 한 Azure 하이브리드 혜택를 사용 하는 고객은 Azure Marketplace [RHEL 제품과 관련 된 표준 약관 및](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) [개인 정보 취급 방침](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) 에 동의 합니다.

RHEL에 대 한 Azure 하이브리드 혜택를 사용 하는 고객에 게는 해당 Vm에 소프트웨어 업데이트 및 패치를 제공 하기 위한 세 가지 옵션이 있습니다.

- [Red Hat 업데이트 인프라](../workloads/redhat/redhat-rhui.md) (기본 옵션)
- Red Hat 위성 서버
- Red Hat 구독 관리자

RHUI 옵션을 선택 하는 고객은 RHUI를 해당 Vm에 연결 하지 않고 Azure 하이브리드 혜택 해당 Vm에 대 한 기본 업데이트 원본으로 계속 사용할 수 있습니다. RHUI 옵션을 선택 하는 고객은 RHEL 구독 준수를 보장 해야 합니다.

Red Hat 위성 서버 또는 Red Hat Subscription Manager 중 하나를 선택 하는 고객은 RHUI 구성을 제거한 후 클라우드 액세스 사용 RHEL 구독을 해당 Azure 하이브리드 혜택 RHEL Vm에 연결 해야 합니다.  

Red Hat 구독 준수, 소프트웨어 업데이트 및 Azure 하이브리드 혜택 RHEL Vm에 대 한 원본에 대 한 자세한 내용은 [Azure 하이브리드 혜택에서 RHEL 구독 사용에 대 한 Red hat 문서](https://access.redhat.com/articles/5419341)를 참조 하세요.

### <a name="suse"></a>SUSE

SLES Vm에 대 한 Azure 하이브리드 혜택를 사용 하 고 SLES PAYG에서 BYOS로 이동 하거나 SLES BYOS에서 PAYG로 이동 하는 방법에 대 한 자세한 내용은 [SUSE Linux Enterprise and Azure 하이브리드 혜택](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)를 참조 하세요. 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>예약 된 인스턴스의 Azure 하이브리드 혜택 미리 보기 상태입니다.

Azure Reservations (Azure Reserved Virtual Machine Instances)를 통해 여러 제품에 대 한 1 년 또는 3 년 요금제로 커밋하여 비용을 절감할 수 있습니다. [여기에서 예약 된 인스턴스에](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)대해 자세히 알아볼 수 있습니다. Azure 하이브리드 혜택은 [예약 된 RIs (가상 컴퓨터 인스턴스)](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation)에 대 한 미리 보기에서 사용할 수 있습니다. 즉, RI를 사용 하 여 할인 된 요금으로 계산 비용을 구매한 경우 RHEL 및 SUSE에 대 한 라이선스 비용에 AHB 혜택을 적용할 수 있습니다. RI 인스턴스에 대해 AHB 혜택을 적용 하는 단계는 일반 VM에 대 한 것과 정확히 동일 하 게 유지 됩니다.
![AHB for RIs](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Azure Marketplace에서 RHEL 또는 SUSE PAYG software에 대 한 예약을 이미 구매한 경우 Azure 하이브리드 혜택를 사용 하기 전에 예약 재직 기간이 완료 될 때까지 기다려 주세요.


## <a name="frequently-asked-questions"></a>질문과 대답
*Q: SLES 이미지를 사용 하 여 라이선스 유형을 사용 `RHEL_BYOS` 하거나 그 반대로 사용할 수 있나요?*

A: 아니요, 할 수 없습니다. VM에서 실행 되는 배포와 부정확 하 게 일치 하는 라이선스 유형을 입력 하려고 하면 청구 메타 데이터가 업데이트 되지 않습니다. 그러나 잘못 된 라이선스 유형을 실수로 입력 한 경우 VM을 올바른 라이선스 유형으로 다시 업데이트 하면 혜택을 계속 사용할 수 있습니다.

*Q: Red Hat Cloud Access에 등록 했지만 여전히 RHEL Vm에 대 한 혜택을 사용할 수 없습니다. 제가 뭘 해야 하나요?*

A: red hat Cloud Access 구독 등록이 Red Hat에서 Azure로 전파 되는 데 약간의 시간이 걸릴 수 있습니다. 한 영업일 후에도 오류가 계속 표시 되 면 Microsoft 지원에 문의 하세요.

*Q: RHEL BYOS "골든 이미지"를 사용 하 여 VM을 배포 했습니다. 이러한 이미지에 대 한 청구를 BYOS에서 PAYG로 변환할 수 있나요?*

A: 아니요, 할 수 없습니다. Azure 하이브리드 혜택는 종 량 제 이미지 에서만 변환을 지원 합니다.

*Q: 온-프레미스 (Azure Migrate, Azure Site Recovery 또는 기타)에서 Azure로 자체 RHEL 이미지를 업로드 했습니다. 이러한 이미지에 대 한 청구를 BYOS에서 PAYG로 변환할 수 있나요?*

A: 아니요, 할 수 없습니다. Azure 하이브리드 혜택 기능은 현재 Azure Marketplace에서 RHEL 및 SLES 이미지에만 사용할 수 있습니다. 

*Q: 온-프레미스 (Azure Migrate, Azure Site Recovery 또는 기타)에서 Azure로 자체 RHEL 이미지를 업로드 했습니다. Azure 하이브리드 혜택 혜택을 얻기 위해 어떤 작업을 수행 해야 하나요?*

A: 아니요, 그렇지 않습니다. 업로드 하는 RHEL 이미지는 이미 BYOS로 간주 되며, Azure 인프라 비용에 대해서만 요금이 부과 됩니다. 온-프레미스 환경에 대 한 것 처럼 RHEL 구독 비용을 담당 하 고 있습니다. 

*Q: Azure Marketplace RHEL 및 SLES SAP 이미지에서 배포 된 Vm에 Azure 하이브리드 혜택를 사용할 수 있나요?*

A: 예, 가능합니다. `RHEL_BYOS`RHEL vm에 대 한 라이선스 유형과 `SLES_BYOS` Azure Marketplace RHEL 및 SLES SAP 이미지에서 배포 된 vm의 변환에 사용할 수 있습니다.

*Q: RHEL 및 SLES에 대 한 가상 머신 확장 집합에서 Azure 하이브리드 혜택를 사용할 수 있나요?*

A: 예, RHEL 및 SLES에 대 한 가상 머신 확장 집합에 대 한 Azure 하이브리드 혜택 미리 보기 상태입니다. [이 혜택 및 사용 방법에 대해 자세히 알아볼](https://docs.microsoft.com/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux-vmss)수 있습니다. 

*Q: RHEL 및 SLES에 대 한 예약 된 인스턴스에서 Azure 하이브리드 혜택를 사용할 수 있나요?*

A: 예, Azure 하이브리드 혜택 RHEL 및 SLES의 예약 인스턴스는 미리 보기 상태입니다. [이 혜택 및 사용 방법에 대해 자세히 알아볼](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)수 있습니다.

*Q: RHEL 이미지에 대해 SQL Server 배포 된 가상 머신에서 Azure 하이브리드 혜택를 사용할 수 있나요?*

A: 아니요, 할 수 없습니다. 이러한 가상 컴퓨터를 지 원하는 계획은 없습니다.

*Q: 내 RHEL 가상 데이터 센터 구독에서 Azure 하이브리드 혜택를 사용할 수 있나요?*

A: 아니요, 할 수 없습니다. VDC는 AHB을 포함 하 여 Azure에서 지원 되지 않습니다.  
 

## <a name="common-problems"></a>일반적인 문제
이 섹션에는 발생할 수 있는 일반적인 문제와 완화 단계를 나열 합니다.

| Error | 완화 방법 |
| ----- | ---------- |
| "Azure 구독에 대 한 Red Hat Cloud Access를 성공적으로 사용 하도록 설정 하지 않았기 때문에 작업을 완료할 수 없습니다." | RHEL Vm에 대 한 혜택을 사용 하려면 먼저 [Red Hat Cloud Access를 사용 하 여 Azure 구독을 등록](https://access.redhat.com/management/cloud)해야 합니다.

## <a name="next-steps"></a>다음 단계
* [을 사용 하 여 Vm을 만들고 업데이트 하 고 Azure 하이브리드 혜택에 대 한 라이선스 유형 (RHEL_BYOS, SLES_BYOS)을 추가 하는 방법에 대해 알아봅니다 Azure CLI](/cli/azure/vm)
