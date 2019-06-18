---
title: Azure Site Recovery Deployment Planner 버전 기록
description: 알려진된 다른 사이트 복구 배포 Planner 버전 수정 및 릴리스 날짜와 함께 알려진된 제한 사항이 있습니다.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927374"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery Deployment Planner 버전 기록

이 문서는 수정, 알려진 각각의 제한 사항 및 릴리스 날짜와 함께 Azure Site Recovery Deployment Planner의 모든 버전의 기록을 제공 합니다.

## <a name="version-24"></a>버전 2.4

**릴리스 날짜: 2019 년 4 월 17 일**

**수정 사항:**

- 지역화 기반 오류를 처리 하는 경우에 특히 운영 체제 호환성을 개선 합니다.
- 데이터의 최대 20 개의 Mbps 사용 하 여 추가 Vm 호환성 검사를 률 (변동)를 변경합니다.
- 향상 된 오류 메시지
  - VCenter 6.7에 대 한 지원이 추가 되었습니다.
  - Windows Server 2019 및 Red Hat Enterprise Linux (RHEL) 워크스테이션에 대 한 지원이 추가 되었습니다.



## <a name="version-23"></a>버전 2.3

**릴리스 날짜: 2018 년 12 월 3 일**

**수정 사항:**

- Deployment Planner는 제공 된 대상 위치 및 구독을 사용 하 여 보고서를 생성 하지 못하도록 하는 문제가 수정 되었습니다.

## <a name="version-22"></a>버전 2.2 

**릴리스 날짜: 2018 년 4 월 25 일**

**수정 사항:**

- GetVMList 작업:
  - GetVMList 지정한 폴더가 존재 하지 않으면 실패를 발생 시킨 문제를 해결 합니다. 이제 중 하나는 기본 디렉터리를 만들거나 outputfile 매개 변수에 지정 된 디렉터리를 만듭니다.
  - GetVMList에 대 한 실패 한 이유를 자세한 추가 합니다.
- Deployment Planner 보고서의 호환 되는 Vm 시트에서 열으로 VM 형식 정보가 추가 되었습니다.
- Hyper-v에서 Azure 재해 복구:
  - 공유 Vhd 및 통과 사용 하 여 제외 된 Vm 프로 파일링에서 디스크. Startprofiling 작업 콘솔에서 제외 된 Vm 목록을 보여 줍니다.
  - 호환 되지 않는 Vm 목록을 64 개 디스크를 사용 하 여 Vm을 추가 합니다.
  - IR (초기 복제) 및 델타 복제 (DR) 압축 비율을 업데이트 합니다.
  - SMB 저장소에 대 한 제한 된 지원이 추가 되었습니다.

## <a name="version-21"></a>버전 2.1

**릴리스 날짜: 2018 년 1 월 3 일**

**수정 사항:**

- Excel 보고서를 업데이트 합니다.
- GetThroughput 작업에서 수정 된 버그입니다.
- 프로 파일링 보고서를 생성 하는 Vm의 수를 제한 하려면 추가 옵션입니다. 기본 제한은 1,000 개의 Vm입니다.
- Azure 재해 복구 하려면 VMware:
  - 호환 되지 않는 테이블로 이동 하는 Windows Server 2016 VM의 문제를 해결 했습니다. 
  - 인터페이스 EFI (Extensible Firmware) Windows Vm에 대 한 호환성 메시지를 업데이트 합니다.
- VMware를 업데이트 합니다. Azure를 Azure로 Hyper-v VM 데이터 변동 VM 당 제한 합니다. 
- VM 목록 파일을 구문 분석의 안정성 향상된입니다.

## <a name="version-201"></a>버전 2.0.1

**릴리스 날짜: 2017 년 12 월 7 일**

**수정 사항:**

- 네트워크 대역폭을 최적화 하기 위한 권장 사항을 추가 합니다.

## <a name="version-20"></a>버전 2.0

**릴리스 날짜: 2017 년 11 월 28 일**

**수정 사항:**

- Azure 재해 복구에 Hyper-v에 대 한 지원이 추가 되었습니다.
- 추가 비용 계산기입니다.
- 추가 OS 버전 검사 VMware에 대 한 Azure 재해 복구 VM 호환 인지 보호에 대 한 호환 되지 않는 경우를 확인 합니다. 도구에는 해당 VM에 대 한 vCenter 서버에서 반환 되는 OS 버전 문자열을 사용 합니다. 게스트 운영 체제 버전의 VMware VM을 만드는 동안 해당 사용자가 선택 합니다.

**알려진된 제한 사항:**

- Hyper-v-Azure 재해 복구, 이름을 포함 하는 VM에 대 한 문자 같은: `,`, `"`를 `[`를 `]`, 및 ``` ` ``` 지원 되지 않습니다. 프로 파일링 하는 경우 보고서 생성 실패 하거나 잘못 된 결과 갖습니다.
- Azure 재해 복구 하려면 vmware의 경우 쉼표를 포함 하는 이름의 VM이 지원 되지 않습니다. 프로 파일링 하는 경우 생성 실패를 보고 하거나 잘못 된 결과 갖습니다.

## <a name="version-131"></a>버전 1.3.1

**릴리스 날짜: 2017 년 7 월 19 일** 

**수정 사항:**

- 보고서 생성에서 대용량 디스크 (> 1TB)에 대 한 지원이 추가 되었습니다. 이제 Deployment Planner를 사용 하 여 1TB 보다 큰 디스크 크기는 가상 컴퓨터에 대 한 복제 계획 (최대 4095GB).
[Azure Site Recovery에서 대용량 디스크 지원(영문)](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)에 대해 자세히 알아보세요.

## <a name="version-13"></a>버전 1.3

**릴리스 날짜: 2017 년 5 월 9 일**

**수정 사항:**

- 보고서 생성에서 관리 되는 디스크에 대 한 지원이 추가 되었습니다. 관리 디스크 장애 조치/테스트 장애 조치에 대해 선택한 경우에 단일 저장소 계정에 배치할 수 있는 Vm 수에 따라 계산 됩니다.

## <a name="version-12"></a>버전 1.2

**릴리스 날짜: 2017 년 4 월 7 일**

**수정 사항:**

- 추가 부팅 유형 (BIOS 또는 EFI) 확인 호환 또는 보호를 위해 호환 되지 않는 VM 인지 확인 하는 각 VM에 대 한 합니다.
- 추가 OS 호환 되는 Vm 및 호환 되지 않는 Vm 워크시트에서 각 가상 머신에 대 한 정보를 입력합니다.
- 미국 정부 및 중국 Microsoft Azure 지역에서는 GetThroughput 작업에 대 한 지원이 추가 되었습니다.
- vCenter 및 ESXi 서버에 대한 몇 가지 추가 필수 요건이 추가되었습니다.
- 로캘 설정을 영어 이외의 언어로 설정 되어 생성 된 잘못 된 보고서의 문제가 수정 되었습니다.

## <a name="version-11"></a>버전 1.1

**릴리스 날짜: 2017 년 3 월 9 일**

**수정 사항:**

- 여러 vCenter의 ESXi 호스트에서 동일한 이름 또는 IP 주소를 사용 하 여 두 개 이상의 Vm이 있는 경우 Vm을 프로 파일링 하지 못하는 문제가 수정 되었습니다.
- 복사 및 호환 되는 Vm 및 호환 되지 않는 Vm 워크시트에 대 한 사용 하지 않도록 설정할 검색 하는 문제가 수정 되었습니다.

## <a name="version-10"></a>버전 1.0

**릴리스 날짜: 2017 년 2 월 23 일**

**알려진된 제한 사항:**

- VMware에서 Azure 재해 복구 시나리오에 대해서만 지원 합니다. Hyper-v에서 Azure 재해 복구 시나리오를 사용 합니다 [Hyper-v capacity planner 도구](./site-recovery-capacity-planning-for-hyper-v-replication.md)합니다.
- 미국 정부 및 중국 Microsoft Azure 지역에서는 GetThroughput 작업이 지원 하지 않습니다.
- 여러 ESXi 호스트에서 동일한 이름 또는 IP 주소를 사용 하 여 두 개 이상의 Vm이 vCenter 서버에 하는 경우 Vm 도구 cann't 프로필.
이 버전에서 도구는 VMListFile에서 중복된 VM 이름 또는 IP 주소에 대한 프로파일링을 건너뜁니다. 해결 방법은 VCenter 서버 대신 ESXi 호스트를 사용하여 VM을 프로파일링하는 것입니다. 각 ESXi 호스트에 대 한 하나의 인스턴스를 실행 해야 합니다.
