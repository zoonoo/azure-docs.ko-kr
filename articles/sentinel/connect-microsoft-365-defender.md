---
title: Azure 센티널에 Microsoft 365 Defender 데이터 연결 | Microsoft Docs
description: Microsoft 365 Defender에서 Azure 센티널로 인시던트, 경고 및 원시 이벤트 데이터를 수집 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709345"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Microsoft 365 Defender의 데이터를 Azure 센티널에 연결

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 는 이전에 **Microsoft 위협 방지** 또는 **MTP** 로 알려져 있었습니다.
>
> **Microsoft defender For Endpoint** 는 이전의 **Microsoft Defender Advanced Threat Protection** 또는 **mdatp** 로 알려져 있었습니다.
>
> 이전 이름이 일정 시간 동안 계속 사용 되 고 있는 것을 볼 수 있습니다.

## <a name="background"></a>배경

인시던트 통합을 포함 하는 azure 센티널의 [M365D (Microsoft 365 Defender)](/microsoft-365/security/mtp/microsoft-threat-protection) 커넥터를 사용 하면 모든 M365D 인시던트 및 경고를 Azure 센티널로 스트리밍하 고 두 포털 간에 인시던트를 동기화 된 상태로 유지할 수 있습니다. M365D 인시던트는 모든 경고, 엔터티 및 기타 관련 정보를 포함 하며, M365D's 구성 요소 서비스 **Microsoft defender For Endpoint**, microsoft Defender for **Identity**, **microsoft defender for Office 365** 및 **Microsoft Cloud App Security** 에 대 한 경고를 보강 하 여 그룹화 합니다.

또한 커넥터를 사용 하 여 Microsoft Defender에서 끝점에 대 한 **고급 구하기** 이벤트를 azure 센티널로 스트리밍할 수 있습니다. 이렇게 하면 mde 고급 구하기 쿼리를 azure 센티널로 복사 하 고, mde 원시 이벤트 데이터를 사용 하 여 센티널 경고를 보강 하 여 추가 정보를 제공 하 고, 보존 기간이 증가 한 로그를 Log Analytics 수 있습니다

인시던트 통합 및 고급 구하기 이벤트 수집에 대 한 자세한 내용은 [Azure 센티널과 Microsoft 365 Defender 통합](microsoft-365-defender-sentinel-integration.md)을 참조 하세요.

> [!IMPORTANT]
>
> Microsoft 365 Defender connector는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Microsoft 365 defender 필수 구성 요소](/microsoft-365/security/mtp/prerequisites)에 설명 된 대로 Microsoft 365 defender에 대 한 유효한 라이선스가 있어야 합니다. 

- Azure Active Directory의 **전역 관리자** 또는 **보안 관리자** 여야 합니다.

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택 하 고 갤러리에서 **Microsoft 365 Defender (미리 보기)** 를 선택한 다음 **커넥터 열기 페이지** 를 선택 합니다.

1. **인시던트 & 경고에 연결** 섹션의 **구성** 에서 **인시던트 & 경고** 단추를 클릭 합니다.

1. 인시던트가 중복 되지 않도록 하려면 **해당 제품에 대 한 모든 Microsoft 인시던트 생성 규칙을 해제** 하 라는 확인란을 표시 하는 것이 좋습니다.

    > [!NOTE]
    > Microsoft 365 Defender 커넥터를 사용 하도록 설정 하면 모든 M365D 구성 요소 커넥터 (이 문서의 시작 부분에서 언급 된 커넥터)가 백그라운드에서 자동으로 연결 됩니다. 구성 요소 커넥터 중 하나의 연결을 끊으려면 먼저 Microsoft 365 Defender connector와의 연결을 끊어야 합니다.

1. M365 Defender 인시던트 데이터를 쿼리하려면 쿼리 창에서 다음 문을 사용 합니다.
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Microsoft Defender for Endpoint에서 고급 구하기 이벤트를 수집 하려는 경우 해당 하는 고급 구하기 테이블에서 다음과 같은 유형의 이벤트를 수집할 수 있습니다.

    1. 수집할 이벤트 유형으로 테이블의 확인란을 표시 합니다.

       | 테이블 이름 | 이벤트 유형 |
       |-|-|
       | DeviceInfo | 컴퓨터 정보 (OS 정보 포함) |
       | DeviceNetworkInfo | 컴퓨터의 네트워크 속성 |
       | DeviceProcessEvents | 프로세스 만들기 및 관련 이벤트 |
       | DeviceNetworkEvents | 네트워크 연결 및 관련 이벤트 |
       | DeviceFileEvents | 파일 생성, 수정 및 기타 파일 시스템 이벤트 |
       | DeviceRegistryEvents | 레지스트리 항목 생성 및 수정 |
       | DeviceLogonEvents | 로그인 및 기타 인증 이벤트 |
       | DeviceImageLoadEvents | DLL 로드 이벤트 |
       | DeviceEvents | 추가 이벤트 유형 |
       | DeviceFileCertificateInfo | 서명 된 파일의 인증서 정보 |
       |

    1. **변경 내용 적용** 을 클릭합니다.

    1. Log Analytics에서 고급 구하기 테이블을 쿼리하려면 쿼리 창에서 위의 목록에 있는 테이블 이름을 입력 합니다.

## <a name="verify-data-ingestion"></a>데이터 수집 확인

커넥터 페이지의 데이터 그래프는 사용자가 데이터를 수집 것을 나타냅니다. 인시던트, 경고 및 이벤트에 대 한 한 줄이 표시 되 고 이벤트 줄은 사용 하도록 설정 된 모든 테이블에서 이벤트 볼륨의 집계입니다. 커넥터를 사용 하도록 설정 했으면 다음 KQL 쿼리를 사용 하 여 보다 구체적인 그래프를 생성할 수 있습니다.

들어오는 M365 Defender 인시던트의 그래프에 대해 다음 KQL 쿼리를 사용 합니다.

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

다음 KQL 쿼리를 사용 하 여 단일 테이블에 대 한 이벤트 볼륨 그래프를 생성할 수 있습니다. *Deviceevents* 테이블을 선택한 필수 테이블로 변경 합니다.

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

**다음 단계** 탭에서는 포함 된 몇 가지 유용한 통합 문서, 샘플 쿼리 및 분석 규칙 템플릿을 찾을 수 있습니다. 이러한 항목을 지점에서 실행 하거나 수정 하 고 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 365 Defender 커넥터를 사용 하 여 Microsoft Defender for Endpoint의 Azure 센티널에 Microsoft 365 Defender 인시던트 및 고급 구하기 이벤트 데이터를 통합 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./tutorial-detect-threats-built-in.md)을 시작합니다.
