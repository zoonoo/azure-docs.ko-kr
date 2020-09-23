---
title: Azure 센티널에 Microsoft 365 Defender 원시 데이터 연결 | Microsoft Docs
description: Microsoft 365 Defender에서 Azure 센티널로 원시 이벤트 데이터를 수집 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: e9e892ae08c65ee5c5008bbdc1b419d8dec30183
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938483"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Microsoft 365 Defender의 데이터를 Azure 센티널에 연결

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 는 이전에 **Microsoft 위협 방지** 또는 **MTP**로 알려져 있었습니다.
>
> **Microsoft defender For Endpoint** 는 이전의 **Microsoft Defender Advanced Threat Protection** 또는 **mdatp**로 알려져 있었습니다.
>
> 이전 이름이 일정 시간 동안 계속 사용 되 고 있는 것을 볼 수 있습니다.

## <a name="background"></a>배경

새 [Microsoft 365 defender](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-threat-protection) 커넥터를 사용 하면 Microsoft 365 Defender에서 Azure 센티널로의 원시 이벤트 데이터 유형인 **고급 구하기** 로그를 스트리밍할 수 있습니다. 

Microsoft 365 Defender security 파라솔에 [Microsoft defender For endpoint (MDATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 를 통합 하면 이제 Microsoft 365 defender 커넥터를 사용 하 여 끝점 [고급 구하기](https://aka.ms/mdatpAH) 이벤트에 대 한 microsoft defender를 수집 하 고 Azure 센티널 작업 영역에서 새로운 용도의 새 테이블로 직접 스트리밍할 수 있습니다. 이러한 테이블은 Microsoft 365 Defender 포털에서 사용 되는 것과 동일한 스키마를 사용 하 여 전체 고급 구하기 로그 집합에 대 한 완전 한 액세스를 제공 하 고 다음 작업을 수행할 수 있도록 합니다.

- 기존 Microsoft Defender ATP 고급 구하기 쿼리를 Azure 센티널로 쉽게 복사 합니다.

- 원시 이벤트 로그를 사용 하 여 경고, 구하기 및 조사에 대 한 추가 정보를 제공 하 고, Azure 센티널의 추가 데이터 원본 데이터와 이벤트의 상관 관계를 지정 합니다.

- Endpoint 또는 Microsoft 365 Defender의 기본 보존 기간 (30 일)을 초과 하 여 보존 기간이 증가 하는 로그를 저장 합니다. 작업 영역의 보존을 구성 하거나 Log Analytics에서 테이블당 보존을 구성 하 여이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
>
> Microsoft 365 Defender connector는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [끝점 배포용 Microsoft Defender 설정](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)에 설명 된 대로 끝점에 대 한 microsoft defender에 대 한 유효한 라이선스가 있어야 합니다. 

- 사용자에 게 테 넌 트에 대 한 전역 관리자 역할을 할당 해야 합니다 (Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender에 연결

Microsoft Defender for Endpoint가 배포 되 고 데이터를 수집 이벤트 로그를 Azure 센티널로 쉽게 스트리밍할 수 있습니다.

1. Azure 센티널에서 **데이터 커넥터**를 선택 하 고 갤러리에서 **Microsoft 365 Defender (미리 보기)** 를 선택한 다음 **커넥터 열기 페이지**를 선택 합니다.

1. 해당 하는 고급 구하기 테이블에서 다음과 같은 유형의 이벤트를 수집할 수 있습니다. 수집 하려는 이벤트 유형의 확인란을 표시 합니다.

    | 이벤트 유형 | 테이블 이름 |
    |-|-|
    | 컴퓨터 정보 (OS 정보 포함) | DeviceInfo |
    | 컴퓨터의 네트워크 속성 | DeviceNetworkInfo |
    | 프로세스 만들기 및 관련 이벤트 | DeviceProcessEvents |
    | 네트워크 연결 및 관련 이벤트 | DeviceNetworkEvents |
    | 파일 생성, 수정 및 기타 파일 시스템 이벤트 | DeviceFileEvents |
    | 레지스트리 항목 생성 및 수정 | DeviceRegistryEvents |
    | 로그인 및 기타 인증 이벤트 | DeviceLogonEvents |
    | DLL 로드 이벤트 | DeviceImageLoadEvents |
    | 추가 이벤트 유형 | DeviceEvents |
    |

1. **변경 내용 적용**을 클릭합니다. 

1. Log Analytics에서 고급 구하기 테이블을 쿼리하려면 쿼리 창에서 위의 목록에 있는 테이블 이름을 입력 합니다.

## <a name="verify-data-ingestion"></a>데이터 수집 확인

커넥터 페이지의 데이터 그래프는 사용자가 데이터를 수집 것을 나타냅니다. 사용 하도록 설정 된 모든 테이블에서 이벤트 볼륨을 집계 하는 한 줄이 표시 되는 것을 알 수 있습니다. 커넥터를 사용 하도록 설정한 후에는 다음 KQL 쿼리를 사용 하 여 단일 테이블에 대해 유사한 이벤트 볼륨 그래프를 생성할 수 있습니다. *Deviceevents* 테이블을 선택한 필수 테이블로 변경 합니다.

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

**다음 단계** 탭에서는 포함 된 몇 가지 샘플 쿼리를 찾을 수 있습니다. 이러한 항목을 지점에서 실행 하거나 수정 하 고 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 365 Defender 커넥터를 사용 하 여 끝점에 대 한 Microsoft Defender에서 Azure 센티널로 원시 이벤트 데이터를 가져오는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
