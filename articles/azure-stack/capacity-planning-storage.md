---
title: 저장소 용량 Azure Stack에 대 한 계획 | Microsoft Docs
description: Azure Stack 배포에 대 한 계획 저장소 용량에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 09/18/2018
ms.openlocfilehash: 5d9d01a482483d030569a4dcad03c9ecef7cffc0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245153"
---
# <a name="azure-stack-storage-capacity-planning"></a>Azure Stack 저장소 용량 계획
다음 섹션에서는 Azure Stack 저장소 용량 계획 정보는 솔루션의 저장소 요구 사항에 대 한 계획을 지 원하는 제공 합니다.

## <a name="uses-and-organization-of-storage-capacity"></a>사용 및 저장소 용량에는 조직
Azure Stack의 하이퍼 컨버지드 구성을 통해 실제 스토리지 디바이스를 공유할 수 있습니다. 사용 가능한 저장소의 세 가지 주요 부분은 blob, 테이블 및 Azure 일관 된 저장소 (ACS) 서비스의 큐를 지원 되는 저장소, 인프라 및 테 넌 트 가상 컴퓨터의 임시 저장소 사이입니다.

## <a name="spaces-direct-cache-and-capacity-tiers"></a>공간 다이렉트 캐시 및 용량 계층
저장소 용량에 사용 되는 운영 체제, 로컬 로깅, 덤프 및 기타 인프라를 임시 저장소 요구 사항입니다. 이 로컬 저장소 용량은 저장소 공간 다이렉트 구성의 관리를 통해 저장소 장치의 (장치 및 용량)를 구분 합니다. 저장소 장치를 나머지 배율 단위에서는 서버 수에 관계 없이 저장소 용량의 단일 풀에 배치 됩니다. 이러한 장치는 두 가지 유형입니다. 캐시 및 용량입니다.  캐시 장치는 해당 – 캐시에만 합니다. 공간 다이렉트는 이러한 장치 쓰기 저장 사용 되며 읽기 캐싱을. 이러한 캐시 장치 용량을 사용 하는 동안 커밋되지 않습니다는 서식이 지정 된 가상 디스크의 형식이 지정 된, "표시" 용량. 용량 장치가이 용도에 사용 되 고 저장소 공간에서 관리 되는 데이터의 "홈 위치"를 제공 합니다.

모든 저장소 용량 할당 되 고 Azure Stack 인프라에서 직접 관리 합니다. 연산자가 할당을 구성 하는 방법에 대 한 항목을 선택 또는 선택 항목이 처리 용량 확장 하더라도 필요. 이러한 디자인 결정 솔루션 요구 사항에 맞게 향상 되었습니다. 그리고 하거나 초기 설치/배포 중 또는 용량 확장 하는 동안 자동화 됩니다. 복원 력, 다시 빌드에 대 한 예약 된 용량 및 기타 세부 정보에 대 한 정보 디자인의 일환으로 고려 되었습니다. 

연산자는 모든 플래시 또는 하이브리드 저장소 구성 중에서 선택할 수 있습니다.

![Azure storage 용량 계획](media/azure-stack-capacity-planning/storage.png)

모든 플래시 구성에서 캐시 된 NVMe 용량 SATA SSD 또는 NVMe의 경우 선택 합니다. 하이브리드 구성에서 캐시 용량에는 HDD SATA SSD 및 NVMe 간에 선택 하는.

저장소 공간 다이렉트 및 Azure Stack 저장소 구성의 간단한 요약을 아래와 같습니다.
- 배율 단위 (모든 저장소 장치는 단일 풀 내의 구성) 당 하나의 저장소 공간 풀
- 가상 디스크는 세 가지 복사본 미러 최상의 성능 및 복원 력으로 만들어집니다.
- 각 가상 디스크는 ReFS 파일 시스템으로 포맷 됩니다.
- 가상 디스크 용량 계산 되며 하나의 용량 장치 풀에서 할당 되지 않은 데이터 용량 크기를 유지 하는 것에 대 한 방식으로 할당 됩니다. 서버당 하나의 용량 드라이브에 해당 하는 경우
- 각 ReFS 파일 시스템에는 BitLocker 미사용 데이터 암호화에 사용 하도록 설정 해야 합니다. 

가상 디스크가 자동으로 생성 및 해당 용량은 다음과 같습니다.




|이름|용량 계산|설명|
|-----|-----|-----|
|로컬/부팅 장치|340 GB의 최소<sup>1</sup>|운영 체제 이미지 및 "local" 인프라 Vm에 대 한 개별 서버 저장소|
|인프라|3.5TB|모든 Azure Stack 인프라 사용|
|VmTemp|아래 참조<sup>2</sup>|테 넌 트 가상 컴퓨터에 연결 된 임시 디스크 및 해당 데이터는 이러한 가상 디스크에 저장|
|ACS|아래 참조 <sup>3</sup>|Blob, 테이블 및 큐 서비스에 대 한 azure Consistent Storage 용량|

<sup>1</sup> Azure Stack 솔루션 파트너의 필요한 최소 저장소 용량입니다.

<sup>2</sup> 테 넌 트 가상 머신 임시 디스크에 사용 되는 가상 디스크 크기는 서버의 실제 메모리의 백분율로 계산 됩니다. Azure IaaS VM 크기에 대 한 아래 테이블에서 설명한 대로, 임시 디스크는 가상 머신에 할당할 실제 메모리의 비율입니다. Azure Stack에서 "임시 디스크" storage에 대 한 완료 할당은 대부분의 사용 사례를 캡처에 대 한 방식으로 수행 되지만 모든 임시 디스크 저장소 요구를 충족 하지 못할 수 있습니다. 선택하지 없습니다만 임시 디스크 용량에 대 한 저장소 용량 솔루션의 대부분을 사용 하는 동안 임시 저장소를 사용할 수 있도록 절충입니다. 하나의 임시 저장소 디스크는 배율 단위에서는 서버 별로 생성 됩니다. 임시 저장소의 용량 배율 단위의 저장소 풀의 사용 가능한 전체 저장소 용량의 10% 이상 증가 하지 않습니다. 계산을 사용 하면 다음 예제와 같이 것:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> 가상-디스크 ACS에서 사용 하기 위해 만드는 간단한 나누기의 나머지 용량입니다. 모든 가상 디스크는 3 방향 미러 언급 했 듯이 이며 각 서버에 대 한 용량의 용량 onedrive의 분량 할당 없습니다. 다양 한 가상 디스크 위에 열거는 먼저 할당 하 고 남은 용량을 ACS 가상 디스크에 사용 됩니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack 용량 계획 스프레드시트에 알아봅니다](capacity-planning-spreadsheet.md)
