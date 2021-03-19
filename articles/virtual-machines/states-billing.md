---
title: Azure Virtual Machines의 상태 및 청구
description: VM에서 입력할 수 있는 다양 한 상태에 대 한 개요 및 사용자에 게 요금이 청구 될 때.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596300"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Azure Virtual Machines의 상태 및 청구

Azure VM(Virtual Machines)은 *프로비전* 및 *전원* 상태로 분류될 수 있는 여러 상태를 거칩니다. 이 아티클은 이러한 상태를 설명하고, 고객에게 인스턴스 사용량에 요금이 청구된 경우 특히 강조 표시하려고 합니다. 

## <a name="get-states-using-instance-view"></a>인스턴스 뷰를 사용 하 여 상태 가져오기

인스턴스 보기 API는 VM 실행 상태 정보를 제공합니다. 자세한 내용은 [Virtual Machines - 인스턴스 보기](/rest/api/compute/virtualmachines/instanceview) API 설명서를 참조하세요.

Azure 리소스 탐색기는 VM 실행 상태를 보기 위한 간단한 UI를 제공합니다. [리소스 탐색기](https://resources.azure.com/)

프로비전 상태는 VM 속성 및 인스턴스 보기에 표시됩니다. 전원 상태는 VM의 인스턴스 보기에서 사용할 수 있습니다.

구독에 있는 모든 VM의 전원 상태를 검색하려면 매개 변수 **statusOnly** 가 *true* 로 설정된 [Virtual Machines - 모든 API 목록](/rest/api/compute/virtualmachines/listall)을 사용합니다.

> [!NOTE]
> Virtual Machines-매개 변수 **Statusonly** 가 true로 설정 된 [모든 API 나열](/rest/api/compute/virtualmachines/listall) 은 구독의 모든 vm에 대 한 전원 상태를 검색 합니다. 그러나 드문 경우 이지만 검색 프로세스의 일시적인 문제로 인해 전원 상태를 사용할 수 없는 경우도 있습니다. 이러한 경우 동일한 API를 사용 하 여 다시 시도 하거나 [Azure Resource Health](../service-health/resource-health-overview.md) 또는 [Azure 리소스 그래프](..//governance/resource-graph/overview.md) 를 사용 하 여 vm의 전원 상태를 확인 하는 것이 좋습니다.
 
## <a name="power-states-and-billing"></a>전원 상태 및 청구

전원 상태는 VM의 마지막 알려진 상태를 나타냅니다.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="이미지는 VM이 이동할 수 있는 전원 상태의 다이어그램을 보여 줍니다. ":::

다음 표에서는 각 인스턴스 상태를 설명하고 인스턴스 사용량에 대해 요금이 청구되는지 여부를 나타냅니다.

| 전원 상태 | 설명 | 결제 |  
|---|---|---|
| 시작 중| 가상 머신이 켜져 있습니다. |청구 되지 않음 * | 
| 실행 중 | 가상 컴퓨터가 완전히 작동 합니다. 표준 작업 상태입니다. | 청구됨 | 
| 중지 중 | 이는 실행 중과 중지 된 사이의 전환 상태입니다. | 청구됨| 
|중지됨 | 가상 머신이 게스트 OS 내에서 또는 전원 꺼짐 Api를 사용 하 여 종료 되었습니다. 이 상태에서 가상 머신은 여전히 기본 하드웨어를 임대 합니다. 이 상태를 *중지 됨 (할당 됨)* 이 라고도 합니다. | 청구됨 | 
| 할당 취소 중 | 실행 중과 할당 취소 간의 전환 상태입니다. | 청구 되지 않음 * | 
| 할당 취소됨 | 가상 컴퓨터에서 기본 하드웨어에 대 한 임대를 해제 하 고 완전히 꺼져 있습니다. 이 상태를 *중지 됨 (할당 취소 됨)* 이 라고도 합니다. | 청구 되지 않음 * | 

일부 Azure 리소스 (예: [디스크](https://azure.microsoft.com/pricing/details/managed-disks) 및 [네트워킹](https://azure.microsoft.com/pricing/details/bandwidth/) )는 계속 해 서 요금을 부과 합니다. &#42;


## <a name="provisioning-states"></a>프로비전 상태

프로비전 상태는 VM에서 사용자가 시작한 제어 평면 작업의 상태입니다. 이러한 상태는 VM의 전원 상태와 별개입니다.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="이미지 VM이 이동할 수 있는 프로 비전 상태를 보여 줍니다.":::

| 프로비저닝 상태 | 설명 | 전원 상태 | 결제 | 
|---|---|---|---|
| 생성 | 가상 머신을 만듭니다. | 시작 중 | 청구 되지 않음 * | 
| 업데이트 | 기존 가상 컴퓨터에 대 한 모델을 업데이트 합니다. 시작 및 다시 시작과 같은 가상 컴퓨터에 대 한 일부 모델이 아닌 변경 내용은 업데이트 상태에 속합니다. | 실행 중 | 청구됨 | 
| DELETE | 가상 컴퓨터를 삭제 합니다. | 할당 취소 중 | 청구 되지 않음 * |
| 할당 취소 | 기본 호스트에서 가상 컴퓨터가 완전히 중지 되 고 제거 됩니다. 가상 컴퓨터의 할당을 취소 하는 것은 업데이트로 간주 되며, 업데이트와 유사한 프로 비전 상태를 표시 합니다. | 할당 취소 중 | 청구 되지 않음 * | 

일부 Azure 리소스 (예: [디스크](https://azure.microsoft.com/pricing/details/managed-disks) 및 [네트워킹](https://azure.microsoft.com/pricing/details/bandwidth/) )는 계속 해 서 요금을 부과 합니다. &#42;

## <a name="os-provisioning-states"></a>OS 프로 비전 상태
Os 프로 비전 상태는 OS 이미지를 사용 하 여 만든 가상 컴퓨터에만 적용 됩니다. 특수 이미지는 이러한 상태를 표시 하지 않습니다. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="이미지 VM이 이동할 수 있는 OS 프로 비전 상태를 보여 줍니다.":::

| OS 프로 비전 상태 | 설명 | 전원 상태 | 결제 | 
|---|---|---|---|
| OSProvisioningInProgress | VM이 실행 중이 고 게스트 OS 설치가 진행 중입니다. | 실행 중 | 청구됨 | 
| OSProvisioningComplete | 수명이 짧은 상태입니다. 가상 컴퓨터는이 상태에서 **성공** 으로 신속 하 게 전환 됩니다. 확장을 아직 설치 하는 경우 완료 될 때까지 계속 해 서이 상태가 표시 됩니다. | 실행 중 | 청구됨 | 
| 성공 | 사용자가 시작한 작업이 완료 되었습니다. | 실행 중 | 청구됨 | 
| 실패 | 실패 한 작업을 나타냅니다. 자세한 내용 및 가능한 해결 방법은 오류 코드를 참조 하십시오. | 실행 중  | 청구됨 | 


## <a name="next-steps"></a>다음 단계
- [Azure Cost Management 및 청구 설명서](../cost-management-billing/index.yml) 검토
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 배포를 계획 합니다.
- VM 모니터링에 대 한 자세한 내용은 [Azure에서 virtual Machines 모니터링](../azure-monitor/vm/monitor-vm-azure.md)을 참조 하세요.