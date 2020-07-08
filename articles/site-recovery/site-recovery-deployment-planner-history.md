---
title: Azure Site Recovery Deployment Planner 버전 기록
description: 알려진 다른 Site Recovery Deployment Planner 버전 수정 및 알려진 제한 사항이 릴리스 날짜와 함께 사용 됩니다.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: 74b02da51603366f5ec18aa4733117bc2c59faf7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84417857"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery Deployment Planner 버전 기록

이 문서에서는 모든 버전의 Azure Site Recovery Deployment Planner에 대 한 기록과 각 릴리스 날짜 및 해당 릴리스 날짜의 알려진 제한 사항을 제공 합니다.

## <a name="version-252"></a>버전 2.52

**릴리스 날짜: 2020 년 6 월 4 일**

**수정할**

- VCenter 7.0에 대 한 지원이 추가 됨
- 다음 운영 체제에 대 한 지원이 추가 되었습니다.

    - SUSE Linux Enterprise 15 (모든 부 버전 포함)
    - Red Hat Enterprise Linux 8 (모든 부 버전 포함)


## <a name="version-251"></a>버전 2.51

**릴리스 날짜: 2019 년 8 월 22 일**

**수정할**

- Deployment Planner 버전 2.5의 비용 권장 사항 문제를 수정 했습니다.

## <a name="version-25"></a>버전 2.5

**릴리스 날짜: 2019 년 7 월 29 일**

**수정할**

- VMware 가상 컴퓨터와 물리적 컴퓨터의 경우에는 Managed Disks에 대 한 복제에 따라 권장 사항이 업데이트 됩니다.
- Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 이상에 대 한 지원 추가

## <a name="version-24"></a>버전 2.4

**릴리스 날짜: 4 월 17 일 2019**

**수정할**

- 특히 지역화 기반 오류를 처리할 때 운영 체제 호환성이 향상 되었습니다.
- 호환성 검사 목록에 최대 20mbps의 데이터 변경 률 (변동)이 포함 된 Vm을 추가 했습니다.
- 오류 메시지가 향상되었습니다.
- VCenter 6.7에 대 한 지원이 추가 되었습니다.
- Windows Server 2019 및 Red Hat Enterprise Linux (RHEL) 워크스테이션에 대 한 지원이 추가 되었습니다.



## <a name="version-23"></a>버전 2.3

**릴리스 날짜: 2018 년 12 월 3 일**

**수정할**

- 제공 된 대상 위치 및 구독을 사용 하 여 Deployment Planner 보고서를 생성할 수 없도록 하는 문제를 해결 했습니다.

## <a name="version-22"></a>버전 2.2 

**릴리스 날짜: 4 월 25 일, 2018**

**수정할**

- GetVMList 작업:
  - 지정 된 폴더가 존재 하지 않는 경우 GetVMList가 실패 하는 문제가 해결 되었습니다. 이제 기본 디렉터리를 만들거나 outputfile 매개 변수에 지정 된 디렉터리를 만듭니다.
  - GetVMList에 대 한 자세한 실패 이유를 추가 했습니다.
- Deployment Planner 보고서의 호환 되는 Vm 시트에 열로 VM 유형 정보를 추가 했습니다.
- Hyper-v에서 Azure로 재해 복구:
  - 프로 파일링에서 공유 Vhd 및 통과 디스크를 사용 하 여 제외 된 Vm. Startprofiling 파일링 작업은 콘솔에서 제외 된 Vm 목록을 표시 합니다.
  - 호환 되지 않는 Vm 목록에 64 개 이상의 디스크를 포함 하는 Vm을 추가 했습니다.
  - 초기 복제 (IR) 및 델타 복제 (DR) 압축 요인이 업데이트 되었습니다.
  - SMB 저장소에 대 한 제한 된 지원이 추가 되었습니다.

## <a name="version-21"></a>버전 2.1

**릴리스 날짜: 2018 년 1 월 3 일**

**수정할**

- Excel 보고서가 업데이트 되었습니다.
- GetThroughput 작업에서 버그가 수정 되었습니다.
- 프로 파일링 하거나 보고서를 생성 하는 Vm 수를 제한 하는 옵션이 추가 되었습니다. 기본 제한은 1000 Vm입니다.
- VMware에서 Azure로 재해 복구:
  - 호환 되지 않는 테이블로 이동 하는 Windows Server 2016 VM의 문제를 해결 했습니다. 
  - EFI (Extensible 펌웨어 인터페이스) Windows Vm의 호환성 메시지를 업데이트 했습니다.
- VMware를 Azure로 업데이트 하 고 Hyper-v를 Azure로 업데이트 하 고 VM 당 VM 데이터 변동 제한을 업데이트 했습니다. 
- VM 목록 파일 구문 분석의 안정성이 향상 되었습니다.

## <a name="version-201"></a>버전 2.0.1

**릴리스 날짜: 2017 년 12 월 7 일**

**수정할**

- 네트워크 대역폭을 최적화 하기 위한 권장 사항이 추가 되었습니다.

## <a name="version-20"></a>버전 2.0

**릴리스 날짜: 2017 년 11 월 28 일**

**수정할**

- Hyper-v에서 Azure로 재해 복구에 대 한 지원이 추가 되었습니다.
- 비용 계산기를 추가 했습니다.
- VM이 보호에 대해 호환 되는지 또는 호환 되지 않는지 확인 하기 위해 VMware에서 Azure로 재해 복구에 대 한 OS 버전 검사가 추가 되었습니다. 이 도구는 해당 VM의 vCenter 서버에서 반환 하는 OS 버전 문자열을 사용 합니다. VMware에서 VM을 만드는 동안 사용자가 선택한 게스트 운영 체제 버전입니다.

**알려진 제한 사항:**

- Hyper-v에서 Azure로의 재해 복구를 위해,,,, 및와 같은 문자를 포함 하는 이름의 VM은 `,` `"` `[` `]` ``` ` ``` 지원 되지 않습니다. 프로 파일링 되는 경우 보고서 생성이 실패 하거나 결과가 잘못 됩니다.
- VMware에서 Azure로의 재해 복구를 위해 이름이 쉼표를 포함 하는 VM은 지원 되지 않습니다. 프로 파일링 되는 경우 보고서 생성에 실패 하거나 잘못 된 결과가 발생 합니다.

## <a name="version-131"></a>버전 1.3.1

**릴리스 날짜: 2017 년 7 월 19 일** 

**수정할**

- 보고서 생성 시 대량 디스크 (> 1tb)에 대 한 지원이 추가 되었습니다. 이제 Deployment Planner를 사용 하 여 디스크 크기가 1tb (최대 4095 GB) 이상인 가상 컴퓨터에 대 한 복제를 계획할 수 있습니다.
[Azure Site Recovery에서 대용량 디스크 지원(영문)](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)에 대해 자세히 알아보세요.

## <a name="version-13"></a>버전 1.3

**릴리스 날짜: 2017 년 5 월 9 일**

**수정할**

- 보고서 생성 시 관리 디스크에 대 한 지원이 추가 되었습니다. 단일 저장소 계정에 배치할 수 있는 Vm 수는 장애 조치 (Failover)/테스트 장애 조치 (Failover)를 위해 관리 디스크를 선택 했는지 여부에 따라 계산 됩니다.

## <a name="version-12"></a>버전 1.2

**릴리스 날짜: 2017 년 4 월 7 일**

**수정할**

- 각 VM에 대 한 부팅 유형 (BIOS 또는 EFI) 검사를 추가 하 여 VM이 보호에 대해 호환 되는지 또는 호환 되지 않는지 확인 합니다.
- 호환 되는 Vm 및 호환 되지 않는 Vm 워크시트의 각 가상 머신에 대 한 OS 유형 정보를 추가 했습니다.
- 미국 정부 및 중국 Microsoft Azure 지역에 대 한 GetThroughput 작업에 대 한 지원이 추가 되었습니다.
- vCenter 및 ESXi 서버에 대한 몇 가지 추가 필수 요건이 추가되었습니다.
- 로캘 설정이 영어가 아닌 것으로 설정 된 경우 잘못 된 보고서가 생성 되는 문제가 해결 되었습니다.

## <a name="version-11"></a>버전 1.1

**릴리스 날짜: 2017 년 3 월 9 일**

**수정할**

- 여러 vCenter ESXi 호스트에서 동일한 이름 또는 IP 주소를 가진 둘 이상의 Vm이 있는 경우 Vm 프로 파일링을 방지 하는 문제를 해결 했습니다.
- 호환 되는 Vm 및 호환 되지 않는 Vm 워크시트에서 복사 및 검색을 사용 하지 않도록 설정 하는 문제가 해결 되었습니다.

## <a name="version-10"></a>버전 1.0

**릴리스 날짜: 2 월 23 일, 2017**

**알려진 제한 사항:**

- 는 VMware에서 Azure로의 재해 복구 시나리오에만 지원 합니다. Hyper-v에서 Azure로의 재해 복구 시나리오의 경우 [hyper-v capacity planner 도구](./site-recovery-capacity-planning-for-hyper-v-replication.md)를 사용 합니다.
- 는 미국 정부 및 중국 Microsoft Azure 지역에 대 한 GetThroughput 작업을 지원 하지 않습니다.
- VCenter 서버에 여러 ESXi 호스트에서 이름이 나 IP 주소가 동일한 두 개 이상의 Vm이 있는 경우이 도구는 Vm 프로필을 cann't 합니다.
이 버전에서 도구는 VMListFile에서 중복된 VM 이름 또는 IP 주소에 대한 프로파일링을 건너뜁니다. 해결 방법은 VCenter 서버 대신 ESXi 호스트를 사용하여 VM을 프로파일링하는 것입니다. 각 ESXi 호스트에 대해 하나의 인스턴스를 실행 해야 합니다.
