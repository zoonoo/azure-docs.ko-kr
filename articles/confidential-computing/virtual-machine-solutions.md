---
title: Virtual machines의 Azure 기밀 컴퓨팅 솔루션
description: Virtual machines의 Azure 기밀 컴퓨팅 솔루션에 대해 알아봅니다.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: e574ac33e5f7da814c4bd813fc1c083c7cb4c2c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187888"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure virtual machines의 솔루션

이 문서에서는 intel SGX ( [Software Guard Extension](https://software.intel.com/sgx) )로 지원 되는 intel 프로세서를 실행 하는 Azure 기밀 컴퓨팅 vm (가상 머신) 배포에 대 한 정보를 다룹니다. 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure 기밀 컴퓨팅 VM 크기

Azure 기밀 컴퓨팅 가상 머신은 클라우드에서 처리 되는 동안 데이터 및 코드의 기밀성 및 무결성을 보호 하도록 설계 되었습니다. 

[DCsv2 시리즈](../virtual-machines/dcv2-series.md) Vm은 최신의 최근 기밀 컴퓨팅 크기 제품군입니다. 이러한 Vm은 더 많은 범위의 배포 기능을 지원 하며, Enclave 페이지 캐시 (EPC)를 2 개 포함 하며 DC 시리즈 Vm과 비교 하 여 더 많은 크기를 선택 합니다. [DC 시리즈](../virtual-machines/sizes-previous-gen.md#preview-dc-series) vm은 현재 미리 보기 상태 이며 일반 공급에 포함 되지 않으며 사용 되지 않습니다.

[빠른 시작 자습서](quick-create-marketplace.md)를 수행 하 여 Microsoft 상업적 marketplace를 통해 DCSV2 시리즈 VM 배포를 시작 합니다.

### <a name="current-available-sizes-and-regions"></a>현재 사용 가능한 크기 및 지역

사용 가능한 지역 및 가용성 영역에서 일반적으로 사용 가능한 모든 기밀 계산 VM 크기 목록을 가져오려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

4 월 2020부터 이러한 Sku는 다음 지역 및 가용성 영역에서 사용할 수 있습니다.

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

위의 크기에 대 한 자세한 보기를 보려면 다음 명령을 실행 합니다.

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>배포 고려 사항

빠른 시작 자습서에 따라 10 분 이내에 DCsv2 시리즈 가상 머신을 배포 하세요. 

- **Azure 구독** – 기밀 컴퓨팅 VM 인스턴스를 배포 하려면 종 량 제 구독 또는 기타 구매 옵션을 고려 하세요. [Azure 무료 계정을](https://azure.microsoft.com/free/)사용 하는 경우 적절 한 azure 계산 코어 양에 대 한 할당량이 없습니다.

- **가격 책정 및 지역 가용성** - [가상 머신 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에서 DCsv2 시리즈 vm에 대 한 가격 책정을 찾습니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 에서 Azure 지역의 가용성을 확인하세요.


- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 또한 구독은 DCsv2 시리즈를 포함 하 여 특정 VM 크기 제품군에 배포할 수 있는 코어 수를 제한할 수 있습니다. 할당량 증가를 요청 하려면 무료로 [온라인 고객 지원 요청을 여세요](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) . 기본 제한은 구독 범주에 따라 달라질 수 있습니다.

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **크기 조정** – 특수 한 하드웨어로 인해 동일한 크기 제품군 내에서 기밀 컴퓨팅 인스턴스만 크기를 조정할 수 있습니다. 예를 들어 한 DCsv2 시리즈 크기에서 다른 DCsv2 시리즈 VM의 크기를 변경할 수 있습니다. 비밀 없는 컴퓨팅 크기에서 기밀 컴퓨팅 크기로 크기를 조정 하는 것은 지원 되지 않습니다.  

- **이미지** – 기밀 계산 인스턴스에 intel SGX (Software Guard Extension) 지원을 제공 하기 위해 모든 배포를 2 세대 이미지에서 실행 해야 합니다. Azure 기밀 컴퓨팅은 Ubuntu 18.04 Gen 2, Ubuntu 16.04 Gen 2 및 Windows Server 2016 Gen 2에서 실행 되는 워크 로드를 지원 합니다. 지원 되거나 지원 되지 않는 시나리오에 대 한 자세한 내용은 [Azure의 2 세대 vm에 대 한 지원](../virtual-machines/linux/generation-2.md) 을 참조 하세요. 

- **저장소** – Azure 기밀 컴퓨팅 가상 머신 데이터 디스크 및 삭제 된 OS 디스크는 NVMe 디스크에 있습니다. 인스턴스는 프리미엄 SSD 및 표준 SSD 디스크만 지원 하 고 울트라 SSD 또는 표준 HDD는 지원 하지 않습니다. 가상 컴퓨터 크기 **DC8_v2** Premium storage를 지원 하지 않습니다. 

- **디스크 암호화** – 기밀 계산 인스턴스는 지금은 디스크 암호화를 지원 하지 않습니다. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항

Azure에서 가상 컴퓨터를 사용 하는 경우 가동 중지 시간을 방지 하기 위해 고가용성 및 재해 복구 솔루션을 구현 해야 합니다. 

Azure 기밀 컴퓨팅은 현재 가용성 영역을 통해 영역 중복성을 지원 하지 않습니다. 기밀 컴퓨팅에 대 한 고가용성 및 중복성을 위해 [가용성 집합](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)을 사용 합니다. 하드웨어 제한으로 인해 기밀 컴퓨팅 인스턴스의 가용성 집합에는 최대 10 개의 업데이트 도메인만 사용할 수 있습니다. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 통해 배포 

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 구독에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 배포 이후 액세스 제어, 잠금 및 태그와 같은 관리 기능을 사용하여 리소스를 보호하고 구성합니다.

Azure Resource Manager 템플릿에 대한 자세한 내용은 [템플릿 배포 개요](../azure-resource-manager/templates/overview.md)를 참조하세요.

ARM 템플릿에서 DCsv2 시리즈 VM을 배포 하려면 [가상 머신 리소스](../virtual-machines/windows/template-description.md)를 활용 합니다. **Vmsize** 및 **imageReference**에 대 한 올바른 속성을 지정 해야 합니다.

### <a name="vm-size"></a>VM 크기

가상 컴퓨터 리소스의 ARM 템플릿에서 다음 크기 중 하나를 지정 합니다. 이 문자열은 **속성**에서 **vmsize** 로 배치 됩니다.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 이미지

**속성**아래에서 **storageprofile**의 이미지를 참조 해야 합니다. **ImageReference**에 대해 다음 이미지 *중 하나만* 사용 합니다.

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>다음 단계 

이 문서에서는 기밀 컴퓨팅 가상 머신을 만들 때 필요한 자격 및 구성에 대해 알아보았습니다. 이제 Azure Marketplace로 이동 하 여 DCsv2 시리즈 VM을 배포할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Marketplace에 DCsv2 시리즈 가상 머신 배포](quick-create-marketplace.md)