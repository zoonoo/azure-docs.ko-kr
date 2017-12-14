---
title: "Azure Migrate 평가 계산 | Microsoft Docs"
description: "Azure Migrate 서비스의 평가 계산에 대한 개요를 제공합니다."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 2b274244cc7b7fd0fc3eee22a57a51db77370370
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2017
---
# <a name="assessment-calculations"></a>평가 계산

[Azure Migrate](migrate-overview.md)는 Azure로 마이그레이션하는 온-프레미스 워크로드를 평가합니다. 이 문서에서는 평가를 계산하는 방법에 대한 정보를 제공합니다.



## <a name="overview"></a>개요

Azure Migrate 평가에는 세 가지 단계가 있습니다. 평가는 적합성 분석으로 시작하여 성능 기반 크기 조정을 추정하고, 마지막으로 월별 비용을 예측합니다. 컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 Azure 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 예상 비용이 계산되지 않습니다. 


## <a name="azure-suitability-analysis"></a>Azure 적합성 분석

Azure로 마이그레이션하려는 컴퓨터는 Azure 요구 사항 및 제한 사항을 충족해야 합니다. 예를 들어 온-프레미스 VM 디스크가 4TB보다경 큰 경우 Azure에서 호스팅할 수 없습니다. 다음 표에는 Azure 준수 검사가 요약되어 있습니다. 

**확인** | **세부 정보**
--- | ---
**부팅 유형** | 게스트 OS 디스크의 부팅 유형은 UEFI가 아닌 BIOS여야 합니다.
**코어 수** | 컴퓨터의 코어 수는 Azure VM에서 지원되는 최대 코어 수(32)와 같거나 작아야 합니다.<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 코어 수와 비교합니다. 쾌적 인자가 평가 설정에 지정되어 있는 경우 사용된 코어 수와 쾌적 인자를 곱합니다.<br/><br/> 성능 기록이 없으면 Azure Migrate는 쾌적 인자를 적용하지 않고 할당된 코어 수를 사용합니다.
**메모리** | 컴퓨터 메모리 크기는 Azure VM에 허용되는 최대 메모리 크기(448GB)와 같거나 작아야 합니다. <br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 메모리 크기와 비교합니다. 쾌적 인자가 지정되어 있는 경우 사용된 메모리 크기와 쾌적 인자를 곱합니다.<br/><br/> 기록이 없으면 쾌적 인자를 적용하지 않고 할당된 메모리 크기를 사용합니다.<br/><br/> 
**Windows Server 2003-2008 R2** | 32비트 및 64비트를 지원합니다.<br/><br/> Azure에서 최상의 활동만 지원합니다.
**모든 SP가 포함된 Windows Server 2008 R2** | 64비트를 지원합니다.<br/><br/> Azure에서 완벽하게 지원합니다.
**Windows Server 2012 및 모든 SP** | 64비트를 지원합니다.<br/><br/> Azure에서 완벽하게 지원합니다.
**Windows Server 2012 R2 및 모든 SP** | 64비트를 지원합니다.<br/><br/> Azure에서 완벽하게 지원합니다.
**Windows Server 2016 및 모든 SP** | 64비트를 지원합니다.<br/><br/> Azure에서 완벽하게 지원합니다.
**Windows 클라이언트 7 이상** | 64비트를 지원합니다.<br/><br/> Azure에서 Visual Studio 구독만 지원합니다.
**Linux** | 64비트를 지원합니다.<br/><br/> Azure에서 이러한 [운영 체제](../virtual-machines/linux/endorsed-distros.md)를 완벽하게 지원합니다.
**저장소 디스크** | 할당된 디스크 크기는 4TB(4,096GB) 이하여야 합니다.<br/><br/> 컴퓨터에 연결되는 디스크의 수는 OS 디스크를 포함하여 65개 이하여야 합니다. 
**네트워킹** | 컴퓨터에는 32개 이하의 NIC가 연결되어야 합니다.


## <a name="performance-based-sizing"></a>성능 기반 크기 조정

컴퓨터가 Azure에 적합한 것으로 표시되면 Azure Migrate는 다음 조건을 사용하여 Azure의 VM 크기에 매핑합니다.

- **저장소 검사**: Azure Migrate에서 컴퓨터에 연결된 모든 디스크를 Azure의 디스크에 매핑하려고 합니다. Azure Migrate에서 IOPS(초당 I/O 횟수)와 쾌적 인자를 곱합니다. 또한 각 디스크의 처리량(MBps 단위)과 쾌적 인자를 곱합니다. 이렇게 하면 효과적인 디스크 IOPS 및 처리량을 제공합니다. 이에 따라 Azure Migrate는 해당 디스크를 Azure의 표준 또는 프리미엄 디스크에 매핑합니다.
    - 서비스에서 필요한 IOPS 및 처리량을 갖춘 디스크를 찾지 못하면 컴퓨터가 Azure에 적합하지 않은 것으로 표시됩니다.
    - 적합한 디스크 집합을 찾으면 Azure Migrate는 저장소 중복 방법 및 평가 설정에 지정된 위치를 지원하는 디스크를 선택합니다.
    - 적합한 디스크가 여러 개인 경우 비용이 가장 낮은 디스크를 선택합니다.
- **저장소 디스크 처리량**: 디스크 및 VM당 Azure 제한에 대해 [자세히 알아보세요](../azure-subscription-service-limits.md#storage-limits).
- **디스크 유형**: Azure Migrate는 관리 디스크만 지원합니다.
- **네트워크 검사**: Azure Migrate는 온-프레미스 컴퓨터의 NIC 수를 지원할 수 있는 Azure VM을 찾으려고 합니다.
    - 이렇게 하려면 모든 NIC에서 컴퓨터(네트워크 외부)의 초당 전송 데이터(MBps)를 집계하여 집계된 숫자에 쾌적 인자를 적용합니다. 이 숫자는 필요한 네트워크 성능을 지원할 수 있는 Azure VM을 찾는 데 사용됩니다.
    - Azure Migrate는 VM의 네트워크 설정을 적용하고 해당 VM을 데이터 센터 외부의 네트워크로 간주합니다.
    - 네트워크 성능 데이터를 사용할 수 없는 경우 NIC 수만 VM 크기 조정으로 간주됩니다.
- **계산 검사**: 저장소 및 네트워크 요구 사항이 계산되면 Azure Migrate는 계산 요구 사항에 대해 다음과 같이 고려합니다.
    - 성능 데이터를 VM에 사용할 수 있으면 사용된 코어 수 및 메모리 크기를 확인하고 쾌적 인자를 적용합니다. 이 숫자에 따라 Azure에서 적합한 VM 크기를 찾습니다.
    - 적합한 크기가 없으면 컴퓨터가 Azure에 적합하지 않은 것으로 표시됩니다.
    - 적합한 크기가 있으면 Azure Migrate는 저장소 및 네트워킹 계산을 적용합니다. 그런 다음 최종 VM 크기 권장 사항에 대해 위치 및 가격 책정 계층 설정을 적용합니다.


## <a name="monthly-cost-estimation"></a>월별 비용 예측

크기 조정 권장 사항이 완료되면 Azure Migrate는 마이그레이션 후 계산 및 저장소 비용을 계산합니다.

- **계산 비용**: Azure Migrate는 권장되는 Azure VM 크기를 사용하여 청구 API를 통해 VM에 대한 월별 비용을 계산합니다. 계산에는 운영 체제, 소프트웨어 보증, 위치 및 통화 설정이 고려됩니다. 모든 컴퓨터에서 비용을 집계하여 월별 총 계산 비용을 계산합니다.
- **저장소 비용**: 컴퓨터에 연결된 모든 디스크의 월별 비용을 집계하여 해당 컴퓨터의 월별 저장소 비용을 계산합니다. Azure Migrate는 모든 컴퓨터의 저장소 비용을 집계하여 월별 총 저장소 비용을 계산합니다. 현재 계산에서는 평가 설정에 지정된 제안을 고려하지 않습니다.

비용은 평가 설정에 지정된 통화로 표시됩니다. 


## <a name="next-steps"></a>다음 단계

[온-프레미스 VMware VM에 대한 평가 설정](tutorial-assessment-vmware.md)
