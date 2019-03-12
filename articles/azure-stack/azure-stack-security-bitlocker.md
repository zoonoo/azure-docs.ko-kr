---
title: Azure Stack의 암호화 된 미사용 데이터
description: 자세한 방법 휴지 상태의 암호화를 사용 하 여 데이터를 보호 하는 Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 018b8f6cf4fc5d3cd380535fca71a038b7fd4208
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769390"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Azure Stack에서 암호화 된 미사용 데이터

Azure Stack 사용자 및 미사용 암호화를 사용 하 여 저장소 하위 시스템 수준 인프라 데이터를 보호 합니다. Azure Stack의 저장소 하위 시스템은 128 비트 AES 암호화를 사용 하 여 BitLocker를 사용 하 여 암호화 됩니다. BitLocker 키를 내부 암호 저장소에 유지 됩니다.

암호화 미사용 데이터가 다양 한 주요 규정 준수 표준 (예: PCI-DSS, FedRAMP, HIPAA)에 대 한 일반적인 요구 사항입니다. Azure Stack을 사용 하면 구성이 필요 없거나 추가 작업을 사용 하 여 이러한 요구 사항을 충족할 수 있습니다. Azure Stack 어떻게 도움을 주는지 내용은 규정 준수 표준을 충족 하는 방법은 합니다 [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance)합니다.

> [!NOTE]
> 암호화 미사용 데이터는 하나 이상의 하드 드라이브를 물리적으로 훔치고 사람이 액세스 로부터 데이터를 보호 합니다. 데이터 암호화 미사용 데이터 보호 하지 않습니다 가로 챘 네트워크 (데이터 전송에서), 현재 사용 중인 데이터 (메모리의 데이터) 또는 더 일반적으로 데이터에 대 한 시스템이 동안 exfiltrated 되 고 실행 합니다.

## <a name="retrieving-bitlocker-recovery-keys"></a>BitLocker 복구 키를 검색합니다.

미사용 데이터에 대 한 azure Stack BitLocker 키 내부적으로 관리 됩니다. 일반 작업 또는 시스템 시작 중 제공 필요가 없습니다. 그러나 지원 시나리오에는 온라인 시스템에 BitLocker 복구 키 필요할 수 있습니다.  

> [!WARNING]
> BitLocker 복구 키를 검색 하 고 Azure Stack 외부의 안전한 위치에 저장 합니다. 특정 지원 시나리오 중 복구 키를가지고 있지 않은 경우 데이터 손실이 발생 하 고 필요한 이미지를 백업에서 시스템 복원을 수 있습니다.

BitLocker 복구 키를 검색 하려면에 액세스 해야 합니다 [privileged 끝점](azure-stack-privileged-endpoint.md) (PEP). PEP 세션에서 Get-AzsRecoveryKeys cmdlet을 실행 합니다.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

에 대 한 선택적 매개 변수 *Get AzsRecoveryKeys* cmdlet:

| 매개 변수 | 설명 | Type | 필수 |
|---------|---------|---------|---------|
|*원시* | 복구 키, 컴퓨터 이름 및 암호화 된 각 볼륨의 암호 id(s) 간의 매핑 원시 데이터를 반환합니다.  | 스위치 | 없음 (디자인에 따른 것임 지원 시나리오에 대 한)|


## <a name="troubleshoot-issues"></a>문제 해결

BitLocker 잠금을 해제 극단적인 상황에서 부팅 되지 않는 특정 볼륨에서 결과 요청이 실패할 수 있습니다. 아키텍처의 구성 요소 중 일부의 가용성에 따라 BitLocker 복구 키에 없는 경우 가동 중지 시간 및 잠재적 데이터 손실에이 오류가 발생할 수 있습니다.

> [!WARNING]
> BitLocker 복구 키를 검색 하 고 Azure Stack 외부의 안전한 위치에 저장 합니다. 특정 지원 시나리오 중 복구 키를가지고 있지 않은 경우 데이터 손실이 발생 하 고 필요한 이미지를 백업에서 시스템 복원을 수 있습니다.

시스템 시작 하지 못한 Azure Stack 같은 BitLocker 사용 하 여 문제가 되는 의심 되는 경우 지원에 문의 합니다. 지원에는 BitLocker 복구 키에 필요합니다. BitLocker 관련 된 문제는 해당 특정 볼륨에 대해 VM/호스트/FRU 작업을 사용 하 여 해결할 수 있습니다. 다른 경우에 대 한 BitLocker 복구 키를 사용 하는 수동 잠금 해제 절차를 수행할 수 있습니다. BitLocker 복구 키를 사용할 수 없는 경우 유일한 옵션 이미지를 백업에서 복원 하는 것입니다. 마지막 백업을 수행한 경우에 따라 데이터 손실이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack 보안에 자세히 알아보기](azure-stack-security-foundations.md)
- BitLocker가 Csv를 보호 하는 방법에 대 한 자세한 내용은 참조 하세요. [BitLocker를 사용 하 여 볼륨 및 저장소 영역 네트워크 공유 클러스터 보호](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker)합니다.