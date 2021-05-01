---
title: Azure Site Recovery Deployment Planner 버전 기록
description: 릴리스 날짜와 함께 알려진 다른 Site Recovery Deployment Planner 버전 수정 사항 및 알려진 제한 사항.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133806"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery Deployment Planner 버전 기록

이 문서에서는 모든 버전의 Azure Site Recovery Deployment Planner에 대한 기록과 각 버전의 알려진 제한 사항 및 해당 릴리스 날짜를 제공합니다.

## <a name="version-252"></a>버전 2.52

**릴리스 날짜: 2020년 6월 4일**

**수정 사항:**

- vCenter 7.0에 대한 지원이 추가됨
- 다음 운영 체제에 대한 지원이 추가됨:

    - SUSE Linux Enterprise 15(모든 부 버전 포함)
    - Red Hat Enterprise Linux 8(모든 부 버전 포함)


## <a name="version-251"></a>버전 2.51

**릴리스 날짜: 2019년 8월 22일**

**수정 사항:**

- Deployment Planner 버전 2.5의 비용 권장 사항 문제를 수정했습니다.

## <a name="version-25"></a>버전 2.5

**릴리스 날짜: 2019년 7월 29일**

**수정 사항:**

- VMware 가상 머신과 물리적 컴퓨터의 경우에는 관리 디스크에 대한 복제에 따라 권장 사항이 업데이트됩니다.
- Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 이상에 대한 지원 추가됨

## <a name="version-24"></a>버전 2.4

**릴리스 날짜: 2019년 4월 17일**

**수정 사항:**

- 특히 지역화 기반 오류를 처리할 때 운영 체제 호환성이 향상되었습니다.
- 호환성 검사 목록에 최대 20Mbps의 데이터 변경률(변동)이 포함된 VM을 추가했습니다.
- 오류 메시지가 향상되었습니다.
- vCenter 6.7에 대한 지원이 추가되었습니다.
- Windows Server 2019 및 Red Hat Enterprise Linux(RHEL) 워크스테이션에 대한 지원이 추가되었습니다.



## <a name="version-23"></a>버전 2.3

**릴리스 날짜: 2018년 12월 3일**

**수정 사항:**

- 제공된 대상 위치 및 구독을 사용하여 Deployment Planner에서 보고서를 생성할 수 없도록 하는 문제를 해결했습니다.

## <a name="version-22"></a>버전 2.2 

**릴리스 날짜: 2018년 4월 25일**

**수정 사항:**

- GetVMList 작업:
  - 지정된 폴더가 존재하지 않는 경우 GetVMList가 실패하는 문제가 해결되었습니다. 이제 기본 디렉터리를 만들거나 출력 매개 변수에서 지정된 디렉터리를 만듭니다.
  - GetVMList에 대한 자세한 실패 원인을 추가했습니다.
- Deployment Planner 보고서의 호환되는 VM 시트에 M 유형 정보를 열로 추가 했습니다.
- Hyper-V와 Azure 간 재해 복구:
  - 프로파일링에서 공유 VHD 및 통과 디스크가 있는 VM이 제외되었습니다. Startprofiling 작업은 콘솔에서 제외된 VM 목록을 표시합니다.
  - 호환되지 않는 VM 목록에 64개 이상의 디스크를 포함하는 VM을 추가했습니다.
  - IR(초기 복제) 및 DR(델타 복제) 압축 요인이 업데이트되었습니다.
  - SMB 스토리지에 대한 제한된 지원이 추가되었습니다.

## <a name="version-21"></a>버전 2.1

**릴리스 날짜: 2018년 1월 3일**

**수정 사항:**

- Excel 보고서가 업데이트되었습니다.
- GetThroughput 작업에서 버그가 수정되었습니다.
- 보고서를 프로파일링하거나 생성하는 VM 수를 제한하는 옵션이 추가되었습니다. 기본 제한은 VM 1,000개입니다.
- VMware와 Azure 간 재해 복구:
  - 호환되지 않는 테이블로 이동하는 Windows Server 2016 VM 문제를 해결했습니다. 
  - EFI(Extensible Firmware Interface) Windows VM에 대한 호환성 메시지를 업데이트했습니다.
- VMware에서 Azure로 및 Hyper-V에서 Azure로 VM당 VM 데이터 변동 제한을 업데이트했습니다. 
- VM 목록 파일 구문 분석의 신뢰성을 개선했습니다.

## <a name="version-201"></a>버전 2.0.1

**릴리스 날짜: 2017년 12월 7일**

**수정 사항:**

- 네트워크 대역폭을 최적화하기 위한 권장 사항이 추가되었습니다.

## <a name="version-20"></a>버전 2.0

**릴리스 날짜: 2017년 11월 28일**

**수정 사항:**

- Hyper-V와 Azure 간 재해를 복구하기 위한 지원이 추가되었습니다.
- 비용 계산기가 추가되었습니다.
- VM이 보호에 대해 호환되는지 또는 호환되지 않는지 확인하기 위해 VMware와 Azure 간 재해 복구에 대한 OS 버전 확인이 추가되었습니다. 이 도구는 해당 VM의 vCenter 서버에서 반환하는 OS 버전 문자열을 사용합니다. 이는 VMware에서 VM을 만드는 동안 사용자가 선택한 게스트 운영 체제 버전입니다.

**알려진 제한 사항:**

- Hyper-V와 Azure 간 재해 복구는 `,`, `"`, `[`, `]` 및 ``` ` ``` 등의 문자를 포함하는 이름의 VM은 지원되지 않습니다. 프로파일링되는 경우 보고서 생성에 실패하거나 잘못된 결과가 나옵니다.
- VMware와 Azure 간 재해 복구는 쉼표를 포함하는 이름의 VM은 지원되지 않습니다. 프로파일링되는 경우 보고서 생성에 실패하거나 잘못된 결과가 나옵니다.

## <a name="version-131"></a>버전 1.3.1

**릴리스 날짜: 2017년 7월 19일** 

**수정 사항:**

- 보고서 생성 시 대용량 디스크(1TB 초과) 지원이 추가되었습니다. 이제 Deployment Planner를 사용하여 1TB보다 큰 크기(최대 4095GB)의 디스크가 있는 가상 머신 복제를 계획할 수 있습니다.
[Azure Site Recovery에서 대용량 디스크 지원(영문)](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)에 대해 자세히 알아보세요.

## <a name="version-13"></a>버전 1.3

**릴리스 날짜: 2017년 5월 9일**

**수정 사항:**

- 보고서 생성 시 관리 디스크에 대한 지원이 추가되었습니다. 단일 스토리지에 배치할 수 있는 VM 수는 장애 조치(failover)/테스트 장애 조치(failover)에 대해 관리 디스크를 선택한 경우에 따라 계산됩니다.

## <a name="version-12"></a>버전 1.2

**릴리스 날짜: 2017년 4월 7일**

**수정 사항:**

- VM이 보호에 대해 호환되는지 또는 호환되지 않는지를 확인하기 위해 각 VM에 대한 부팅 유형(BIOS 또는 EFI) 확인이 추가되었습니다.
- 호환되는 VM 및 호환되지 않는 VM 워크시트에서 각 가상 머신에 대한 OS 종류 정보가 추가되었습니다.
- 미국 정부 및 중국 Microsoft Azure 지역에 대해 GetThroughput 작업 지원이 추가되었습니다.
- vCenter 및 ESXi 서버에 대한 몇 가지 추가 필수 요건이 추가되었습니다.
- 로캘 설정이 비영어로 설정된 경우 잘못된 보고서가 생성되는 문제가 해결되었습니다.

## <a name="version-11"></a>버전 1.1

**릴리스 날짜: 2017년 3월 9일**

**수정 사항:**

- 다양한 vCenter ESXi 호스트에 동일한 이름 또는 IP 주소가 있는 VM이 둘 이상 있을 때 VM 프로파일링을 할 수 없는 문제가 해결되었습니다.
- 호환되는 VM 및 호환되지 않는 VM 워크시트에 대해 복사 및 검색을 사용하지 않도록 설정하는 문제가 해결되었습니다.

## <a name="version-10"></a>버전 1.0

**릴리스 날짜: 2017년 2월 23일**

**알려진 제한 사항:**

- VMware와 Azure 간 재해 복구 시나리오에만 지원됩니다. Hyper-V와 Azure 간 재해 복구 시나리오에 대해서는 [Hyper-V Capacity Planner 도구](./hyper-v-deployment-planner-overview.md)를 사용합니다.
- 미국 정부 및 중국 Microsoft Azure 지역에 대해서는 GetThroughput 작업이 지원되지 않습니다.
- 여러 ESXi 호스트에서 동일한 이름 또는 IP 주소가 있는 둘 이상의 VM이 vCenter 서버에 있는 경우 도구에서 VM을 프로파일링할 수 없습니다.
이 버전에서 도구는 VMListFile에서 중복된 VM 이름 또는 IP 주소에 대한 프로파일링을 건너뜁니다. 해결 방법은 VCenter 서버 대신 ESXi 호스트를 사용하여 VM을 프로파일링하는 것입니다. 각 ESXi 호스트에 대해 인스턴스 하나만 실행해야 합니다.
