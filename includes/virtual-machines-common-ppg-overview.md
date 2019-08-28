---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850328"
---
Vm을 단일 지역에 배치 하면 인스턴스 간의 물리적인 거리가 줄어듭니다. 단일 가용성 영역에 배치 하는 경우에도 물리적으로 긴밀 하 게 통합 됩니다. 그러나 Azure 공간이 증가 함에 따라 단일 가용성 영역은 여러 물리적 데이터 센터에 걸쳐 있을 수 있으며이로 인해 응용 프로그램에 영향을 주는 네트워크 대기 시간이 발생할 수 있습니다. 

가능한 한 가까운 시간 내에 Vm을 가져오기 위해 가장 낮은 대기 시간을 달성 하려면 근접 배치 그룹 내에 배포 해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까운 위치에 있는지 확인 하는 데 사용 되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구 하는 작업에 유용 합니다.


- 독립 실행형 Vm 간의 짧은 대기 시간.
- 단일 가용성 집합 또는 가상 머신 확장 집합의 Vm 간에 짧은 대기 시간 
- 독립 실행형 Vm, 여러 가용성 집합의 Vm 또는 여러 확장 집합 간의 짧은 대기 시간 단일 배치 그룹에 여러 계산 리소스를 포함 하 여 다중 계층 응용 프로그램을 함께 만들 수 있습니다. 
- 서로 다른 하드웨어 유형을 사용 하는 여러 응용 프로그램 계층 간의 짧은 대기 시간 예를 들어 가용성 집합에서 M 시리즈를 사용 하 여 백 엔드를 실행 하 고 단일 근접 배치 그룹의 확장 집합에서 D 시리즈 인스턴스의 프런트 엔드를 실행 합니다.

> [!IMPORTANT]
> 근접 배치 그룹은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 이러한 지역에서는 미리 보기 중에 근접 배치 그룹을 사용할 수 없습니다. **일본 동부**, **오스트레일리아 동부** 및 **인도 중부**.


## <a name="best-practices"></a>모범 사례 
- 대기 시간이 가장 짧은 경우 근접 배치 그룹을 가속 네트워킹과 함께 사용 합니다. 자세한 내용은 [가속화 된 네트워킹을 사용 하 여 Linux 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [가속화 된 네트워킹을 사용 하 여 Windows 가상 머신](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)만들기를 참조 하세요.
- 단일 resource manager 템플릿에서 모든 VM 크기를 배포 합니다. 필요한 모든 VM Sku 및 크기를 지원 하지 않는 하드웨어를 사용 하지 않도록 하려면 모든 응용 프로그램 계층을 단일 템플릿에 포함 하 여 모든 응용 프로그램을 동시에 배포 해야 합니다.
- PowerShell, CLI 또는 SDK를 사용 하 여 배포를 스크립팅 하는 경우 할당 오류가 `OverconstrainedAllocationRequest`발생할 수 있습니다. 이 경우 모든 기존 Vm을 중지/할당 취소 하 고 실패 한 VM SKU/크기로 시작 하도록 배포 스크립트의 시퀀스를 변경 해야 합니다. 
- Vm을 삭제 한 기존 배치 그룹을 다시 사용 하는 경우 Vm을 추가 하기 전에 삭제가 완전히 완료 될 때까지 기다립니다.
- 대기 시간이 첫 번째 우선 순위의 경우 근접 배치 그룹에 Vm을 배치 하 고 전체 솔루션을 가용성 영역에 배치 합니다. 그러나 복원 력이 가장 중요 한 경우에는 여러 가용성 영역에 인스턴스를 분산 시킵니다 (단일 근접 배치 그룹은 영역을 확장할 수 없음).