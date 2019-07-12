---
title: Azure Migrate 평가 계산 | Microsoft Docs
description: Azure Migrate 서비스의 평가 계산에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805976"
---
# <a name="assessment-calculations"></a>평가 계산

Azure로 마이그레이션하기 위한 온-프레미스 워크 로드를 평가 하는 azure Migrate Server 평가 합니다. 이 문서에서는 평가를 계산하는 방법에 대한 정보를 제공합니다.


[Azure Migrate](migrate-services-overview.md) 검색, 평가 및 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 인스턴스에서 Azure로 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션 뿐만 아니라 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 대 한 Azure Migrate 도구를 제공합니다.

## <a name="overview"></a>개요

Azure Migrate Server 평가 평가 세 가지 단계가 있습니다. 평가는 적합성 분석으로 시작되며 크기 조정, 월별 비용 예측이 차례로 진행됩니다. 컴퓨터는 이전 단계를 통과하는 경우에만 이후 단계로 이동합니다. 예를 들어 컴퓨터가 Azure 적합성 검사에 실패하면 Azure에 적합하지 않은 것으로 표시되고 크기 조정과 비용 예측이 수행되지 않습니다.


## <a name="whats-in-an-assessment"></a>평가의 내용

**Property** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 위치입니다.<br/><br/> Azure Migrate는 현재 이러한 대상 영역을 지원합니다. 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 인도 중부, 미국 중부, 중국 동부, 중국 북부, 동아시아, 미국 동부, 미국 동부 2, 독일 중부, 독일 북동부, 일본 동부, 일본 서 부, 한국 중부, 한국 남부, 북부 미국 중부, 북유럽, 미국 중남부, 동남 아시아, 인도 남부, 영국 남부, 영국 서 부, 미국 정부 애리조나, 미국 정부 텍사스, 미국 버지니아 주 정부, 미국 중서부, 유럽 서 부, 인도 서 부, 미국 서 부 및 미국 서 부 2입니다.<br/> 기본적으로 대상 지역은 미국 서부 2로 설정되어 있습니다.
**저장소 유형** | 표준 HHD 디스크/표준 SSD 디스크/Premium입니다.<br/><br/> 평가에서 자동으로 저장소 형식을 지정할 때 디스크 권장 사항 (IOPS 및 처리량) 디스크의 성능 데이터를 기반으로 합니다.<br/><br/> 프리미엄/표준 저장소 유형을 지정 하면 저장소 형식 내에서 디스크 SKU 평가 권장 선택 합니다.<br/><br/> 99.9% 단일 인스턴스 VM SLA를 달성 하려는 경우 프리미엄 managed disks로 저장소 형식을 설정할 수 있습니다. 다음 평가의 모든 디스크는 프리미엄 managed disks로 권장 됩니다. <br/> 마이그레이션을 평가하는 경우 Azure Migrate는 관리 디스크만 지원합니다.<br/> 
**Reserved Instances (RI)** | Azure에서 인스턴스를 예약 하는 경우이 속성을 지정 합니다. 평가에서 비용된 예측에 고려 RI 할인 걸립니다. 예약된 인스턴스는 현재 Azure Migrate의 종 량 제 제안에 대해서만 지원 합니다.
**크기 조정 기준** | 적합 한 크기로 Vm을 사용 합니다. 성능 기반 크기 조정 수 또는-온-프레미스, 성능 기록을 고려 하지 않고 있습니다.
**성능 기록** | VM 성능 평가 대해 고려할 기간입니다. 이 속성은 성능 기반 크기 조정 경우에 적용 됩니다.
**백분위 수 사용률** | 적정 Vm에 사용 되는 성능 샘플의 백분위 수 값입니다. 이 속성은 성능 기반 크기 조정 경우에 적용 됩니다.
**VM 시리즈** | 크기 예측에 사용되는 VM 시리즈입니다. 예를 들어 Azure에서 A 시리즈 VM으로 마이그레이션하지 않으려는 프로덕션 환경이 있는 경우 목록 또는 시리즈에서 A 시리즈를 제외할 수 있습니다. 크기 조정은 선택한 시리즈만을 기반으로 합니다.
**쾌적 인자** | Azure Migrate Server 평가 평가 하는 동안 버퍼 (쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다.
**제안** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. Azure Migrate는 그에 따라 비용을 예측합니다.
**통화** | 청구 통화입니다. 
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다.<br/> 기본 설정은 0%입니다.
**VM 작동 시간** | Azure에서 VM을 연중 무휴로 실행하지는 않으려는 경우 VM을 실행할 기간(월별 일 수/일별 시간 수)을 지정하면 해당 기간에 따라 예상 비용이 계산됩니다.<br/> 기본값은 월당 31일이며 일당 24시간입니다.
**Azure 하이브리드 혜택** | 여부를 지정 하면 software assurance가 있는 적합 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)합니다. [예]로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. 



## <a name="azure-suitability-analysis"></a>Azure 적합성 분석

일부 컴퓨터는 Azure에서 실행 하는 데 적합 합니다. 각 온-프레미스 컴퓨터 마이그레이션에 대 한 평가 하 고 다음 적합성 범주 중 하나에 컴퓨터를 분류 하는 azure Migrate Server 평가:
- **Azure 준비 완료** - 컴퓨터를 변경하지 않고 현재 상태대로 Azure로 마이그레이션할 수 있습니다. Azure를 완벽하게 지원하는 상태로 Azure에서 부팅됩니다.
- **조건부 Azure 준비 완료** - 컴퓨터가 Azure에서 부팅되지만 Azure를 완벽하게 지원하지 않을 수도 있습니다. 예를 들어 이전 버전의 Windows Server OS가 설치된 컴퓨터는 Azure에서 지원되지 않습니다. 이러한 컴퓨터를 Azure로 마이그레이션할 때는 주의해야 하며, 마이그레이션 전에 평가에 제안된 수정 지침에 따라 준비 상태 문제를 해결해야 합니다.
- **Azure를 사용할 준비 안 됨** - 컴퓨터가 Azure에서 부팅되지 않습니다. 예를 들어 온-프레미스 컴퓨터에 크기가 4TB를 초과하는 디스크가 연결되어 있을 경우 Azure에서 호스트할 수 없습니다. Azure로 마이그레이션하기 전에 평가에 제안된 수정 지침에 따라 준비 상태 문제를 해결해야 합니다. Azure를 사용할 준비 안 됨으로 표시된 컴퓨터에 대해서는 정적 크기 조정 및 비용 예측이 수행되지 않습니다.
- **준비 상태 알 수 없음** - vCenter Server에서 사용 가능한 데이터가 부족하여 Azure Migrate가 컴퓨터의 준비 상태를 찾을 수 없습니다.



### <a name="machine-properties"></a>컴퓨터 속성

Azure Migrate는 Azure에서 실행할 수 있는지 여부를 식별 하는 온-프레미스 VM의 다음 속성을 검토 합니다.

**Property** | **세부 정보** | **Azure 준비 상태**
--- | --- | ---
**부팅 유형** | Azure는 부팅 유형이 BIOS인 VM을 지원하지만 UEFI인 VM은 지원하지 않습니다. | 부팅 유형이 UEFI인 경우 조건부 준비 완료입니다.
**코어 수** | 컴퓨터의 코어 수는 Azure VM에 대해 지원되는 최대 코어 수(128개 코어) 이하여야 합니다.<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 코어 수와 비교합니다. 쾌적 인자가 평가 설정에 지정되어 있는 경우 사용된 코어 수와 쾌적 인자를 곱합니다.<br/><br/> 성능 기록이 없으면 Azure Migrate는 쾌적 인자를 적용하지 않고 할당된 코어 수를 사용합니다. | 제한보다 작거나 같은 경우 준비 완료입니다.
**메모리** | 컴퓨터 메모리 크기는 Azure VM에 허용되는 최대 메모리 크기(Azure M 시리즈 Standard_M128m&nbsp;<sup>2</sup>에서 3892GB)와 같거나 작아야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> 성능 기록을 사용할 수 있으면 Azure Migrate는 사용된 메모리 크기와 비교합니다. 쾌적 인자가 지정되어 있는 경우 사용된 메모리 크기와 쾌적 인자를 곱합니다.<br/><br/> 기록이 없으면 쾌적 인자를 적용하지 않고 할당된 메모리 크기를 사용합니다.<br/><br/> | 제한 내에 있는 경우 준비 완료입니다.
**저장소 디스크** | 할당된 디스크 크기는 4TB(4,096GB) 이하여야 합니다.<br/><br/> 컴퓨터에 연결되는 디스크의 수는 OS 디스크를 포함하여 65개 이하여야 합니다. | 제한 내에 있는 경우 준비 완료입니다.
**네트워킹** | 컴퓨터에는 32개 이하의 NIC가 연결되어야 합니다. | 제한 내에 있는 경우 준비 완료입니다.

### <a name="guest-operating-system"></a>게스트 운영 체제
VM 속성을 함께 Azure Migrate Server 평가 Azure에서 실행할 수 있는지 여부를 식별 하 고 컴퓨터의 게스트 운영 체제에 살펴봅니다.

> [!NOTE]
> Azure Migrate Server 평가 분석을 위해 vCenter Server에서에서 VM에 대해 지정 된 운영 체제를 사용 합니다. Azure Migrate Server 평가 VM 검색에 대 한 어플라이언스 기반 이므로 vCenter Server에에서 지정 된 것과 동일한 VM에서 실행 중인 운영 체제 인지 여부를 확인할 수 없습니다.

다음 논리는 운영 체제에 따라 Azure 준비 상태를 식별 하려면 Azure Migrate Server 평가에서 사용 됩니다.

**운영 체제** | **세부 정보** | **Azure 준비 상태**
--- | --- | ---
Windows Server 2016 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2012 R2 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2012 및 모든 SP | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2008 R2 및 모든 SP | Azure에서 완벽하게 지원합니다.| Azure 준비 완료
Windows Server 2008(32비트 및 64비트) | Azure에서 완벽하게 지원합니다. | Azure 준비 완료
Windows Server 2003, 2003 R2 | 이러한 운영 체제는 지원 날짜가 종료되었으며, Azure에서 지원을 받기 위해 [CSA(사용자 지정 지원 계약)](https://aka.ms/WSosstatement)가 필요합니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | 이러한 운영 체제는 지원 날짜가 종료되었으며, Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Windows 클라이언트 7, 8, 10 | Azure는 [Visual Studio 구독만 지원합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | 조건부 Azure 준비 완료
Windows 10 Pro Desktop | Azure는 [다중 테넌트 호스팅 권한을 지원합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | 조건부 Azure 준비 완료
Windows Vista, XP Professional | 이러한 운영 체제는 지원 날짜가 종료되었으며, Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
Linux | Azure는 이러한 [Linux 운영 체제](../virtual-machines/linux/endorsed-distros.md)를 보증합니다. 다른 Linux 운영 체제도 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 OS를 보증된 버전으로 업그레이드하는 것이 좋습니다. | 버전이 보증되면 Azure 준비 완료입니다.<br/><br/>버전이 보증되지 않으면 조건부 준비 완료입니다.
기타 운영 체제<br/><br/> 예: Oracle Solaris, Apple Mac OS, FreeBSD 등 | Azure는 이러한 운영 체제를 보증하지 않습니다. Azure에서 컴퓨터가 부팅되더라도 Azure가 OS 지원을 제공하지 않습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 지원되는 OS를 설치하는 것이 좋습니다.  
vCenter Server에서 **기타**로 지정된 OS | 이 경우 Azure Migrate가 OS를 식별할 수 없습니다. | 준비 상태 알 수 없음입니다. VM 내부에서 실행 중인 OS가 Azure에서 지원되는지 확인합니다.
32비트 운영 체제 | Azure에서 컴퓨터가 부팅되더라도 Azure가 전체 지원을 제공하지 않을 수 있습니다. | 조건부 Azure 준비 완료의 경우 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 32비트 OS에서 64비트 OS로 업그레이드하는 것이 좋습니다.

## <a name="sizing"></a>크기 조정

컴퓨터가 Azure 준비 완료로 표시되면 Azure Migrate는 VM 및 해당 디스크의 크기를 Azure에 맞게 조정합니다.

- 평가에서는 성능 기반 크기 조정 하는 경우 Azure Migrate는 Azure에서 VM 크기 및 디스크 유형을 식별 하는 컴퓨터의 성능 기록을 고려 합니다. 이 메서드는 과도 하 게 온-프레미스 VM을 할당 하지만 사용률이 낮습니다 하려는 경우 적정 크기로 비용 절감을 위해 Azure에서 VM에 특히 유용 합니다.
- 인 경우 Azure Migrate Server 평가 사용률 데이터를 고려 하지 않고 온-프레미스 설정에 따라 Vm 크기, 있는 그대로 사용 하 여 온-프레미스 평가 합니다. 디스크 크기 조정,이 경우에 저장소 유형에 따라 (표준 디스크 또는 프리미엄 디스크)는 평가 속성에 지정 합니다.

### <a name="performance-based-sizing"></a>성능 기반 크기 조정

성능 기반 크기 조정에 대 한 Azure Migrate 뒤에 네트워크 어댑터, VM에 연결 된 디스크를 사용 하 여 시작한 다음 맵 Azure VM을 기반으로 온-프레미스 VM의 계산 요구 합니다.

- **저장소**: Azure Migrate는 머신에 연결된 모든 디스크를 Azure의 디스크에 매핑하려고 합니다.
    - 유효 디스크 IOPS(초당 I/O) 및 처리량(MBps)을 얻기 위해 Azure Migrate는 디스크 IOPS와 처리량에 쾌적 인자를 곱합니다. Azure Migrate는 유효 IOPS 및 처리량 값에 따라 디스크를 Azure의 표준 또는 프리미엄 디스크에 매핑할지를 식별합니다.
    - Azure Migrate가 필요한 IOPS 및 처리량을 가진 디스크를 찾지 못하면 컴퓨터가 Azure에 적합하지 않은 것으로 표시됩니다. 디스크 및 VM당 Azure 제한에 대해 [자세히 알아보세요](../azure-subscription-service-limits.md#storage-limits).
    - 적합한 디스크 집합을 찾으면 Azure Migrate는 저장소 중복 방법 및 평가 설정에 지정된 위치를 지원하는 디스크를 선택합니다.
    - 적합한 디스크가 여러 개인 경우 비용이 가장 낮은 디스크를 선택합니다.
    - 디스크에 대 한 성능 데이터를 사용할 수 없는 경우 모든 디스크가 Azure의 표준 디스크에 매핑됩니다.

- **네트워크**: Azure Migrate는 온-프레미스 머신에 연결된 네트워크 어댑터 수와 이러한 네트워크 어댑터에 필요한 성능을 지원할 수 있는 Azure VM을 찾으려고 합니다.
    - 온-프레미스 VM의 유효 네트워크 성능을 얻기 위해 Azure Migrate는 모든 네트워크 어댑터를 통해 컴퓨터에서 초당 전송(네트워크 출력)되는 데이터(MBps)를 집계하고 쾌적 인자를 적용합니다. 이 숫자는 필요한 네트워크 성능을 지원할 수 있는 Azure VM을 찾는 데 사용됩니다.
    - 네트워크 성능과 더불어, Azure VM이 필요한 네트워크 어댑터 수를 지원할 수 있는지 여부도 고려합니다.
    - 네트워크 성능 데이터를 사용할 수 없는 경우 네트워크 어댑터 개수만 VM 크기 조정에 고려됩니다.

- **컴퓨팅**: 스토리지 및 네트워크 요구 사항이 계산된 후 Azure Migrate는 CPU 및 메모리 요구 사항을 고려하여 Azure의 적정 VM 크기를 찾습니다.
    - Azure Migrate는 사용한 코어 및 메모리를 확인하고 쾌적 인자를 적용하여 유효 코어 및 메모리를 얻습니다. 이 숫자에 따라 Azure에서 적합한 VM 크기를 찾습니다.
    - 적합한 크기가 없으면 컴퓨터가 Azure에 적합하지 않은 것으로 표시됩니다.
    - 적합한 크기가 있으면 Azure Migrate는 저장소 및 네트워킹 계산을 적용합니다. 그런 다음 최종 VM 크기 권장 사항에 대해 위치 및 가격 책정 계층 설정을 적용합니다.
    - 적합한 Azure VM 크기가 여러 개인 경우 비용이 가장 낮은 크기가 권장됩니다.

### <a name="as-on-premises-sizing"></a>온-프레미스로 크기 조정

온-프레미스 크기 조정으로 사용 하는 경우 서버 평가 크기 온-프레미스에 따라 Azure VM SKU를 할당 합니다. 디스크 크기 조정과 유사하게 평가 속성에 지정된 저장소 유형을 확인하고(표준/프리미엄) 그에 따라 디스크 유형을 권장합니다. 기본 저장소 형식에는 프리미엄 디스크입니다.

## <a name="confidence-ratings"></a>신뢰 등급
Azure Migrate의 각 성능 기반 평가 신뢰도 등급에서 까지의 하나 (최소) 5 시작 (가장 높음)에 연결 됩니다.
- 신뢰 등급은 평가 계산에 필요한 데이터 요소의 가용성에 따라 평가에 할당됩니다.
- 평가의 신뢰 등급은 Azure Migrate에서 제공하는 권장 크기의 신뢰성을 추정하는 데 도움이 됩니다.
- 신뢰 등급은 온-프레미스 평가 적용 되지 않습니다.
- 성능 기반 크기 조정에 대 한 Azure Migrate Server 평가 해야합니다.
    - CPU 사용률 데이터 및 VM 메모리입니다.
    - 또한 VM에 연결된 각 디스크에는 디스크 IOPS 및 처리량 데이터가 필요합니다.
    - 마찬가지로 신뢰 등급이 VM에 연결 된 각 네트워크 어댑터에 대해 성능 기반 크기 조정을 수행 하려면 네트워크 I/O를 해야 합니다.
    - 위의 사용률 숫자 중 하나를 사용할 수 없는 vCenter Server에서에서 권장 하는 크기 수 신뢰할 수 없습니다. 

사용 가능한 데이터 요소의 백분율에 따라 아래와 같이 평가의 신뢰 등급이 제공됩니다.

   **데이터 요소 가용성** | **신뢰 등급**
   --- | ---
   0%-20% | 별 1개
   21%-40% | 별 2개
   41%-60% | 별 3개
   61%-80% | 별 4개
   81%-100% | 별 5개

### <a name="low-confidence-ratings"></a>낮은 신뢰 등급

평가 낮은 신뢰 등급을 가져올 수는 이유는 이유 중 몇 가지:

- 만들려는 평가 기간에 대 한 환경을 프로 파일링 하지 있습니다. 예를 들어 1 일로 설정 하는 성능 기간을 사용 하 여 평가 만든 경우 적어도 하루에 수집 하 여 모든 데이터 요소에 대 한 검색을 시작한 후에 대 한 대기 해야 합니다.
- 평가 계산 기간 동안 일부 Vm이 종료 되었습니다. 모든 Vm이 꺼지면 기간 중 일부를 Azure Migrate Server 평가 기간에 대 한 성능 데이터를 수집할 수 없습니다.
- 평가 계산 기간 일부 Vm은 만들었습니다. 예를 들어 마지막 1 달의 성능 기록에 대 한 평가 만들 수 있지만 일부 Vm이 불과 일주일 전에 환경 생성 된 경우 새 Vm의 성능 기록이 없습니다 있습니다 전체 기간에 대 한 합니다.

  > [!NOTE]
  > 평가의 신뢰 등급이 별 다섯 아래 인 경우 환경을 프로 파일링 하려면 어플라이언스에 대 한 하루 이상 기다려야 다음 평가 다시 계산 하는 것이 좋습니다. 그렇지 않으면 성능 기반 크기 조정을 불안정할 수, 있으며 온-프레미스 크기 조정으로 사용 하 여 평가 전환 하는 것이 좋습니다.
  
## <a name="monthly-cost-estimation"></a>월별 비용 예측

크기 조정 권장 사항 완료 되 면 Azure Migrate에 대 한 계산 및 저장소 비용은 마이그레이션 후 계산 됩니다.

- **컴퓨팅 비용**: Azure Migrate는 권장되는 Azure VM 크기를 사용하여 청구 API를 통해 VM에 대한 월별 비용을 계산합니다.
    - 계산에는 운영 체제, 소프트웨어 보증, 예약 인스턴스, VM 작동 시간, 위치 및 통화 설정이 고려됩니다.
    - 모든 머신에서 비용을 집계하여 월별 총 계산 비용을 계산합니다.
- **스토리지 비용**: 컴퓨터에 대 한 월별 저장소 비용을 컴퓨터에 연결 된 모든 디스크의 월별 비용을 집계 하 여 계산 됩니다.
    - Azure Migrate 서버 평가 모든 컴퓨터의 저장소 비용을 집계 하 여 월별 총 저장소 비용을 계산 합니다.
    - 현재 계산에서는 평가 설정에 지정된 제안을 고려하지 않습니다.

비용은 평가 설정에 지정된 통화로 표시됩니다.


## <a name="next-steps"></a>다음 단계

에 대 한 평가 만들 [VMware Vm](tutorial-assess-vmware.md) 하거나 [Hyper-v Vm](tutorial-assess-hyper-v.md)합니다.
