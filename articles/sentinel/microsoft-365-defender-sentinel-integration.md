---
title: Azure 센티널과 Microsoft 365 Defender 통합 | Microsoft Docs
description: Azure 센티널과 Microsoft 365 Defender를 통합 하 여 M365 보안 인시던트를 조사 하는 데 도움이 되는 M365D's 강도를 유지 하면서 유니버설 인시던트 큐로 Azure 센티널을 사용 하 고, Defender 구성의 고급 구하기 데이터를 Azure 센티널에 수집 하는 방법을 알아봅니다.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745924"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Azure 센티널과 Microsoft 365 Defender 통합

> [!IMPORTANT]
> Microsoft 365 Defender connector는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 는 이전에 **Microsoft 위협 방지** 또는 **MTP** 로 알려져 있었습니다.
>
> **Microsoft defender For Endpoint** 는 이전의 **Microsoft Defender Advanced Threat Protection** 또는 **mdatp** 로 알려져 있었습니다.
>
> 이전 이름이 일정 시간 동안 계속 사용 되 고 있는 것을 볼 수 있습니다.

## <a name="incident-integration"></a>인시던트 통합

Azure 센티널의 [M365D (Microsoft 365 Defender)](/microsoft-365/security/mtp/microsoft-threat-protection) 인시던트 통합을 사용 하면 모든 M365D 인시던트를 Azure 센티널로 스트리밍하 고 두 포털 간에 동기화 된 상태를 유지할 수 있습니다. M365D의 인시던트 (이전에는 Microsoft Threat Protection 또는 MTP)에는 연결 된 모든 경고, 엔터티 및 관련 정보가 포함 되어 있으므로 Azure 센티널에서 심사 및 예비 조사를 수행 하는 데 충분 한 컨텍스트를 제공 합니다. 센티널에서 인시던트는 M365D와 양방향 동기화 된 상태로 유지 되므로 인시던트 조사에서 두 포털의 이점을 활용할 수 있습니다.

이러한 통합을 통해 전체 조직에서 기본 인시던트 큐의 일부로 Azure 센티널에서 관리 될 수 있는 보안 인시던트를 Microsoft 365 수 있습니다. 따라서 다른 모든 클라우드 및 온-프레미스 시스템의 M365 인시던트를 함께 확인 하 고 상관 관계를 지정할 수 있습니다. 이와 동시에이 기능을 사용 하면 M365 에코 시스템의 M365D에 대 한 고유한 장점과 기능을 활용 하 여 심층적인 조사를 수행할 수 있습니다. M365 Defender 강화 및 그룹은 SOC의 인시던트 큐 크기를 줄이고 해결할 시간을 단축 하는 여러 M365 제품의 경고를 생성 합니다. M365 Defender stack의 일부인 구성 요소 서비스는 다음과 같습니다.

- **Microsoft Defender For Endpoint** (MDE, 이전 MDATP)
- **Id 용 Microsoft Defender** (MDI, 이전 aatp)
- **Office 365 용 Microsoft Defender** (mdo, 이전 O365ATP)
- **Microsoft Cloud App Security** (mcas)

이러한 구성 요소에서 경고를 수집 하는 것 외에도 M365 Defender는 자체에 대 한 경고를 생성 합니다. 이러한 모든 경고에서 인시던트를 만들어 Azure 센티널에 보냅니다.

### <a name="common-use-cases-and-scenarios"></a>일반적인 사용 사례 및 시나리오

- M365 Defender 구성 요소의 모든 경고 및 엔터티를 포함 하 여 M365 Defender 인시던트에 연결을 한 번 클릭 하 여 Azure 센티널로 연결 합니다.

- 상태, 소유자 및 종료 이유에 대해 센티널과 M365D 인시던트 간의 양방향 동기화

- Azure 센티널에서 M365 Defender 경고 그룹화 및 보강 기능을 활용 하 여 해결할 시간을 줄입니다.

- 두 포털에서 조사를 용이 하 게 하기 위해 Azure 센티널 인시던트와 parallel M365 Defender 인시던트 간의 상황에 맞는 딥 링크입니다.

### <a name="connecting-to-microsoft-365-defender"></a>Microsoft 365 Defender에 연결

Microsoft 365 Defender 데이터 커넥터를 사용 하도록 설정 하 여 [인시던트 및 경고를 수집](connect-microsoft-365-defender.md)하 고 나면 M365 defender에서 생성 되는 즉시 **제품 이름** 필드에 **Microsoft 365 Defender** 를 사용 하 여 Azure 센티널 인시던트 큐에 M365D 인시던트가 표시 됩니다.
- M365 Defender에서 인시던트가 생성 된 시간부터 Azure 센티널에 표시 되는 시간까지 최대 10 분 정도 걸릴 수 있습니다.

- 인시던트는 추가 비용 없이 수집 되 고 동기화 됩니다.

M365 Defender 통합이 연결 되 면 모든 구성 요소 경고 커넥터 (MDE, MDI, MDO, MDO)가 백그라운드에서 아직 연결 되지 않은 경우 자동으로 연결 됩니다. M365 Defender가 연결 된 후에 구성 요소 라이선스가 구매한 경우 새 제품의 경고와 인시던트는 추가 구성 또는 요금 없이도 Azure 센티널로 계속 전달 됩니다.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender 인시던트 및 Microsoft 인시던트 만들기 규칙

- M365 보안 제품에서 발생 하는 경고를 기반으로 M365 Defender에서 생성 한 인시던트는 사용자 지정 M365 논리를 사용 하 여 만들어집니다.

- Azure 센티널의 Microsoft 인시던트 생성 규칙은 또한 (a 다른) 사용자 지정 Azure 센티널 논리를 사용 하 여 동일한 경고에서 인시던트를 만듭니다.

- 두 메커니즘을 함께 사용 하는 것은 완전히 지원 되며,이 구성을 사용 하 여 새 M365 Defender 인시던트 생성 논리로 쉽게 전환할 수 있습니다. 그러나 동일한 경고에 대해 **중복 인시던트가** 생성 됩니다.

- 동일한 경고에 대 한 중복 인시던트가 생성 되지 않도록 하려면 M365 Defender를 연결할 때 고객에 게 M365 제품에 대 한 모든 **Microsoft 인시던트 생성 규칙** 을 해제 하는 것이 좋습니다. 커넥터 페이지에 관련 확인란을 표시 하 여이 작업을 수행할 수 있습니다. 이 작업을 수행 하는 경우 인시던트 생성 규칙에 의해 적용 된 모든 필터가 M365 Defender 인시던트 통합에 적용 되지 않습니다.

- MCAS (Microsoft Cloud App Security) 경고의 경우 현재 모든 경고 유형이 M365 Defender로 등록. 모든 MCAS 경고에 대해 계속 해 서 인시던트를 받고 있는지 확인 하려면 M365D에 *등록 되지 않는* 경고 유형에 대 한 **Microsoft 인시던트 생성 규칙** 을 유지 하거나 만들어야 합니다.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Azure 센티널 및 양방향 동기화에서 M365 Defender 인시던트 사용

M365 Defender 인시던트는 제품 이름 **Microsoft 365 defender** 와 함께 Azure 센티널 인시던트 큐에 표시 되며, 다른 센티널 인시던트에 대해 비슷한 세부 정보 및 기능을 포함 합니다. 각 인시던트에는 M365 Defender 포털의 병렬 인시던트에 다시 연결 되는 링크가 포함 되어 있습니다.

M365 Defender에서 인시던트가 진화 하 고 추가 경고 또는 엔터티가 추가 되 면 Azure 센티널 인시던트가 그에 따라 업데이트 됩니다.

M365D 또는 Azure 센티널에서 상태, 종결 이유 또는 M365 인시던트의 할당에 대 한 변경 내용은 다른의 인시던트 큐에서 적절 하 게 업데이트 됩니다. 인시던트 변경 내용이 적용 되 고 지연 없이 두 포털에서 동기화가 수행 됩니다. 최신 변경 내용을 보려면 새로 고침이 필요할 수 있습니다.

M365 Defender에서 한 인시던트의 모든 경고를 다른 인시던트에 전송할 수 있으며이로 인해 인시던트가 병합 됩니다. 이 문제가 발생 하면 Azure 센티널 인시던트에 변경 내용이 반영 됩니다. 한 인시던트에는 원래 인시던트의 모든 경고가 포함 되 고, 다른 인시던트는 "리디렉션" 태그가 추가 된 상태로 자동으로 닫힙니다.

> [!NOTE]
> Azure 센티널의 인시던트에는 최대 150 개의 경고가 포함 될 수 있습니다. M365D 인시던트는이를 초과할 수 있습니다. 150 개 이상의 경고를 포함 하는 M365D 인시던트가 Azure 센티널로 동기화 되는 경우 센티널 인시던트는 "150 +" 개의 경고를 포함 하는 것으로 표시 되며 전체 경고 집합을 볼 수 있는 M365D의 병렬 인시던트에 대 한 링크를 제공 합니다.

## <a name="advanced-hunting-event-collection"></a>고급 구하기 이벤트 수집

또한 Microsoft 365 Defender 커넥터를 사용 하면 Microsoft 365 Defender와 해당 구성 요소 서비스에서 Azure 센티널로 **고급 구하기** 이벤트 (원시 이벤트 데이터 유형)를 스트리밍할 수 있습니다. 현재 [끝점 (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [고급 구하기](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) 이벤트를 수집 하 고 Azure 센티널 작업 영역에서 용도에 맞게 작성 된 테이블로 직접 스트리밍할 수 있습니다. 이러한 테이블은 Microsoft 365 Defender 포털에서 사용 되는 것과 동일한 스키마를 사용 하 여 전체 고급 구하기 이벤트 집합에 대 한 완전 한 액세스를 제공 하 고 다음 작업을 수행할 수 있도록 합니다.

- 기존 Microsoft Defender for Endpoint 고급 구하기 쿼리를 Azure 센티널에 쉽게 복사 합니다.

- 원시 이벤트 로그를 사용 하 여 경고, 구하기 및 조사에 대 한 추가 정보를 제공 하 고 이러한 이벤트를 Azure 센티널의 추가 데이터 원본에 있는 다른 사용자와 상관 관계를 지정 합니다.

- Endpoint 또는 Microsoft 365 Defender의 기본 보존 기간 (30 일)을 초과 하 여 보존 기간이 증가 하는 로그를 저장 합니다. 작업 영역의 보존을 구성 하거나 Log Analytics에서 테이블당 보존을 구성 하 여이 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 365 Defender 커넥터를 사용 하 여 Azure 센티널과 함께 Microsoft 365 Defender를 사용 하는 방법에 대해 알아보았습니다.

- [Microsoft 365 Defender 커넥터를 사용 하도록 설정](connect-microsoft-365-defender.md)하기 위한 지침을 가져옵니다.
- [사용자 지정 경고](tutorial-detect-threats-custom.md) 를 만들고 [인시던트를 조사](tutorial-investigate-cases.md)합니다.
