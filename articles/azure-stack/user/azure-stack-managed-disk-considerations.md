---
title: 차이점 및 Azure Stack의 Managed Disks에 대 한 고려 사항 | Microsoft Docs
description: Azure Stack의 Managed Disks를 사용 하 여 작업 하는 경우 차이점 및 고려 사항에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 9eed4c4bd8cd6290bd2126c91bcf4e37c1b0fa0b
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341952"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack에 Managed Disks: 차이점 및 고려 사항
이 문서에서는 Azure Stack의 Managed Disks 및 azure Managed Disks의 알려진된 차이점을 요약 합니다. Azure Stack 및 Azure 간의 대략적인 차이 대 한 자세한 내용은 참조는 [고려 사항 키](azure-stack-considerations.md) 문서.

Managed Disks를 관리 하 여 IaaS Vm 용 디스크 관리를 간소화 합니다 [저장소 계정](/azure/azure-stack/azure-stack-manage-storage-accounts) 는 VM 디스크와 연결 합니다.

> [!Note]  
> Azure Stack에 managed Disks는 1808에서 사용할 수 있습니다.
  

## <a name="cheat-sheet-managed-disk-differences"></a>참고 자료: 관리 디스크 차이점

| 기능 | Azure (전역) | Azure Stack |
| --- | --- | --- |
|미사용 데이터 암호화 |Azure Disk Encryption (ADE) azure Storage 서비스 암호화 (SSE)     |BitLocker 128 비트 AES 암호화      |
|이미지          | 관리 되는 사용자 지정 이미지를 지원 합니다. |아직 지원 되지 않음|
|백업 옵션 |Azure Backup 서비스를 지원 합니다. |아직 지원 되지 않음 |
|재해 복구 옵션 |Azure Site Recovery 지원 |아직 지원 되지 않음|
|디스크 유형     |Premium SSD, 표준 SSD (미리 보기) 및 표준 HDD |Premium SSD, HDD 표준 |
|프리미엄 디스크  |완전하게 지원 |성능 제한이 있지만 프로비저닝할 수 있습니다 또는 보장  |
|프리미엄 디스크 IOPs  |디스크 크기에 따라 달라 집니다.  |디스크당 IOPs는 2300까지 |
|프리미엄 디스크 처리량 |디스크 크기에 따라 달라 집니다. |디스크당 초당 145 MB |
|디스크 크기  |Azure Premium 디스크: P4 (32 GiB) P80를 (32 TiB)<br>Azure 표준 SSD 디스크: E 10 (128 GiB) E80를 (32 TiB)<br>Azure 표준 HDD 디스크: S4 (32 GiB) S80를 (32 TiB) |M4: 32GiB<br>M6: 64GiB<br>M10: 128GiB<br>M15: 256GiB<br>M20: 512GiB<br>M30: 1024GiB |
|디스크 스냅숏 복사|Azure 스냅숏 지원 되는 실행 중인 VM에 연결 된 디스크를 관리 합니다.|아직 지원 되지 않음 |
|디스크 성능 분석 |메트릭을 집계 하 고 지원 되는 디스크 메트릭 당 |아직 지원 되지 않음 |
|마이그레이션      |VM을 다시 만들 필요 없이 기존 관리 되지 않는 Azure Resource Manager Vm에서 마이그레이션하는 도구를 제공 합니다.  |아직 지원 되지 않음 |

> [!Note]  
> 관리 되는 디스크 IOPs 및 처리량이 Azure Stack 하드웨어 및 Azure Stack에서 실행 중인 워크 로드에 영향을 받이 있습니다 프로 비전 된 숫자가 대신 cap 숫자입니다.


## <a name="metrics"></a>메트릭
저장소 메트릭 사용 하 여 차이점도 있습니다.
- Azure Stack을 사용 하 여 storage 메트릭에서는 트랜잭션 데이터는 내부 또는 외부 네트워크 대역폭을 다르지 않습니다.
- Storage 메트릭에서는 azure Stack 트랜잭션 데이터는 탑재 된 디스크에 대 한 가상 컴퓨터 액세스를 포함 하지 않습니다.


## <a name="api-versions"></a>API 버전
Azure Stack Managed Disks는 다음과 같은 API 버전을 지원합니다.
- 2017-03-30


## <a name="next-steps"></a>다음 단계
[Azure Stack virtual machines에 알아보기](azure-stack-compute-overview.md)
