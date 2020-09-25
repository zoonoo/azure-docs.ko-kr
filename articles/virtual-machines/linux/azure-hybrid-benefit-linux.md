---
title: Azure 하이브리드 혜택 및 Linux Vm
description: Azure 하이브리드 혜택를 사용 하면 Azure에서 실행 되는 Linux 가상 컴퓨터에 비용을 절감할 수 있습니다.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: d62eaf96354627e0c1e4e0a31bb16fb3265f66ac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279776"
---
# <a name="preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>미리 보기: Azure 하이브리드 혜택-Linux Virtual Machines에 적용 되는 방법

## <a name="overview"></a>개요

Azure 하이브리드 혜택를 사용 하면 기존 Red Hat 또는 SUSE software 구독을 직접 사용 하 여 온-프레미스 Red Hat Enterprise Linux (RHEL) SUSE Linux Enterprise Server 및 SLES (가상 머신) Vm (가상 머신)을 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 이 혜택을 통해 소프트웨어 요금은 RHEL 또는 SLES 구독에 포함 되므로 VM의 인프라 비용에 대해서만 비용을 지불 하면 됩니다. 이 혜택은 모든 RHEL 및 SLES Marketplace 종 량 제 (PAYG) 이미지에 적용 됩니다.

> [!IMPORTANT]
> Linux Vm에 대 한 Azure 하이브리드 혜택는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="benefit-description"></a>혜택 설명

Azure 하이브리드 혜택를 통해 Azure에서 기존 RHEL 및 SLES PAYG Vm을 변환 하 여 BYOS (사용자 소유의 구독) 요금 청구를 통해 온-프레미스 RHEL 및 SLES 서버를 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 일반적으로 Azure의 PAYG 이미지에서 배포 된 Vm은 소프트웨어 요금 뿐만 아니라 인프라 요금을 부과 합니다. Azure 하이브리드 혜택를 사용 하 여 PAYG Vm을 다시 배포 하지 않고 BYOS 청구 모델로 변환 하 여 가동 중지 위험을 방지할 수 있습니다.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Linux Vm에 대 한 비용 시각화를 Azure 하이브리드 혜택 합니다.":::

RHEL 또는 SLES VM에서 혜택을 사용 하도록 설정 하면 일반적으로 PAYG VM에서 발생 하는 추가 소프트웨어 요금에 대 한 요금이 더 이상 청구 되지 않습니다. 대신 VM은 compute 하드웨어 요금만 포함 하 고 소프트웨어 요금은 포함 하지 않는 BYOS 요금 내보내기가 시작 됩니다.

원하는 경우 혜택을 사용 하도록 설정 된 VM을 PAYG 청구 모델로 변환할 수도 있습니다.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux Vm에 대 한 Azure 하이브리드 혜택 자격의 범위

Azure 하이브리드 혜택은 모든 RHEL 및 SLES Marketplace PAYG 이미지에 사용할 수 있습니다. RHEL 또는 SLES Marketplace BYOS 이미지 또는 사용자 지정 이미지에 대 한 혜택은 아직 제공 되지 않습니다.

Linux Vm에 대 한 혜택을 이미 사용 하 고 있는 경우 예약 된 인스턴스, 전용 호스트 및 SQL 하이브리드 혜택은 Azure 하이브리드 혜택에 적합 하지 않습니다.

## <a name="how-to-get-started"></a>시작하는 방법

Azure 하이브리드 혜택 현재 Linux Vm에 대 한 미리 보기 단계입니다. 미리 보기에 대 한 액세스 권한을 얻은 후 Azure Portal 또는 Azure CLI를 사용 하 여 혜택을 사용 하도록 설정할 수 있습니다.

### <a name="preview"></a>미리 보기

이 단계에서는 [여기](https://aka.ms/ahb-linux-form)에 양식을 입력 하 여 혜택에 대 한 액세스 권한을 얻을 수 있습니다. 양식을 작성 하 고 나면 혜택에 대해 Azure 구독을 사용할 수 있으며 영업일 기준 3 일 이내에 Microsoft의 확인을 받게 됩니다.

### <a name="red-hat-customers"></a>Red Hat 고객

1.    위의 미리 보기 요청 양식을 작성 합니다.
1.    [Red Hat 클라우드 액세스 프로그램](https://aka.ms/rhel-cloud-access) 에 등록
1.    클라우드 액세스를 위해 Azure 구독을 사용 하도록 설정 하 고 혜택을 사용 하려는 Vm이 포함 된 구독을 사용 하도록 설정 합니다.
1.    Azure Portal 또는 Azure CLI를 통해 기존 Vm에 혜택을 적용 합니다.
1.    별도의 업데이트 원본으로 혜택을 받을 수 있도록 Vm 등록

### <a name="suse-customers"></a>SUSE 고객

1.    위의 미리 보기 요청 양식을 작성 합니다.
1.    SUSE 공용 클라우드 프로그램에 등록
1.    Azure Portal 또는 Azure CLI를 통해 기존 Vm에 혜택을 적용 합니다.
1.    별도의 업데이트 원본으로 혜택을 받을 수 있도록 Vm 등록

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Azure Portal 혜택 사용 및 사용 안 함

**구성** 블레이드를 방문 하 고 해당 단계를 수행 하 여 기존 vm에 대 한 혜택을 사용 하도록 설정할 수 있습니다. VM 만들기 환경에서 새 Vm에 대 한 혜택을 사용 하도록 설정할 수 있습니다.

### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Azure CLI 혜택 사용 및 사용 안 함

' Az vm update ' 명령을 사용 하 여 기존 Vm을 업데이트할 수 있습니다. RHEL Vm의 경우--라이선스 형식 매개 변수를 "RHEL_BYOS"로 사용 하 여 명령을 실행 합니다. SLES Vm의 경우--라이선스 형식 매개 변수를 "SLES_BYOS"로 사용 하 여 명령을 실행 합니다.

#### <a name="cli-example-to-enable-the-benefit"></a>혜택을 사용 하도록 설정 하는 CLI 예제:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>이점을 사용 하지 않도록 설정 하는 CLI 예제:
이 혜택을 사용 하지 않도록 설정 하려면 라이선스 유형 값인 "None"을 사용 합니다.
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>많은 수의 Vm에서 혜택을 사용 하도록 설정 하는 CLI 예제
다 수의 Vm에 대 한 혜택을 사용 하도록 설정 하려면 `--ids` Azure CLI에서 매개 변수를 사용 합니다.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

다음 예제에서는 리소스 그룹 수준에서 하나, 구독 수준에서 하나씩 리소스 Id 목록을 가져오는 두 가지 방법을 보여 줍니다.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>VM의 AHB 상태 확인
다음 세 가지 방법으로 VM의 AHB 상태를 확인할 수 있습니다. 포털에서 체크 인 하거나, Azure CLI를 사용 하거나, azure Instance Metadata Service (Azure IMDS)를 사용 합니다.


### <a name="portal"></a>포털

구성 블레이드를 확인 하 고 라이선스 상태를 확인 하 여 VM에 대해 AHB가 사용 하도록 설정 되어 있는지 확인 합니다.

### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`이러한 용도로 명령을 사용할 수 있습니다. 응답에서 licenseType 필드를 찾습니다. LicenseType 필드가 존재 하 고 값이 ' RHEL_BYOS ' 또는 ' SLES_BYOS ' 이면 VM에서 혜택을 사용할 수 있습니다.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

VM 자체 내에서 IMDS 증명 된 메타 데이터를 쿼리하여 VM의 licenseType을 확인할 수 있습니다. LicenseType 값 ' RHEL_BYOS ' 또는 ' SLES_BYOS '는 VM이 사용 하도록 설정 된 것을 의미 합니다. 증명 된 메타 데이터에 대 한 자세한 내용은 여기를 참조 [하세요](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service#attested-data) .

## <a name="compliance"></a>규정 준수

### <a name="red-hat"></a>Red Hat

RHEL Vm에 대 한 Azure 하이브리드 혜택를 사용 하려면 먼저 Red Hat 클라우드 액세스 프로그램에 등록 해야 합니다. 여기서는 Red Hat Cloud Access 사이트를 통해이 작업을 수행할 수 있습니다. VM에서 혜택을 사용 하도록 설정한 후에는 Red Hat Subscription Manager 또는 Red Hat 위성을 사용 하 여 사용자 고유의 업데이트 원본으로 VM을 등록 해야 합니다. 업데이트를 등록 하면 지원 되는 상태로 유지 됩니다.

### <a name="suse"></a>SUSE

SLES Vm에 대 한 Azure 하이브리드 혜택를 사용 하려면 먼저 SUSE 공용 클라우드 프로그램에 등록 해야 합니다. 프로그램에 대 한 자세한 내용은 여기를 참조 하세요. SUSE 구독을 구매한 후에는 SUSE 고객 센터, 구독 관리 도구 서버 또는 SUSE Manager를 사용 하 여 사용자 고유의 업데이트 원본에 해당 구독을 사용 하 여 Vm을 등록 해야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답
*Q: SLES 이미지를 사용 하 여 "RHEL_BYOS" 라이선스 유형을 사용 하거나 그 반대로 사용할 수 있나요?*

A: 아니요. VM에서 실행 되는 배포판 잘못 일치 하는 라이선스 유형을 입력 하려고 하면 청구 메타 데이터가 업데이트 되지 않습니다. 그러나 잘못 된 라이선스 유형을 실수로 입력 한 경우 VM을 올바른 라이선스 유형으로 다시 업데이트 하면 혜택을 계속 사용할 수 있습니다.

*Q: Red Hat Cloud Access에 등록 했지만 여전히 내 RHEL Vm에서 혜택을 사용 하도록 설정할 수 없습니다. 뭐 할까요?*

A: red hat Cloud Access 구독 등록이 Red Hat에서 Azure로 전파 되는 데 약간의 시간이 걸릴 수 있습니다. 한 영업일 후에도 오류가 계속 표시 되 면 Microsoft 지원에 문의 하세요.

## <a name="common-errors"></a>일반 오류
이 섹션에는 일반적인 오류 및 완화 단계에 대 한 목록이 포함 되어 있습니다.

| Error | 완화 방법 |
| ----- | ---------- |
| "구독이 Azure 하이브리드 혜택의 Linux 미리 보기에 등록 되어 있지 않습니다. 단계별 지침은 다음을 참조 하세요. https://aka.ms/ahb-linux | 에서 양식을 작성 하 여 https://aka.ms/ahb-linux-form Azure 하이브리드 혜택 Linux 미리 보기에 등록 합니다.
| "Azure 구독에 대 한 Red Hat Cloud Access를 성공적으로 사용 하도록 설정 하지 않았기 때문에 작업을 완료할 수 없습니다." | RHEL Vm에서 혜택을 사용 하려면 먼저 Red Hat Cloud Access를 사용 하 여 Azure 구독을 등록 해야 합니다. Red Hat Cloud Access에 대 한 Azure 구독을 등록 하는 방법에 대 한 자세한 내용을 보려면이 링크를 방문 하세요.

## <a name="next-steps"></a>다음 단계
* [여기](https://aka.ms/ahb-linux-form)에 양식을 입력 하 여 미리 보기를 통해 시작 하세요.
