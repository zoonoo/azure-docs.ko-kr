---
title: Azure Sentinel에 Windows 보안 이벤트 데이터 연결 | Microsoft Docs
description: Security Events 커넥터를 사용하여 Windows 시스템에서 Azure Sentinel 작업 영역으로 모든 보안 이벤트를 스트리밍하는 방법 알아보기.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570549"
---
# <a name="connect-windows-security-events"></a>Windows 보안 이벤트 연결 

보안 이벤트 커넥터를 사용하여 Windows 시스템(서버 및 워크스테이션, 물리적 및 가상)의 모든 보안 이벤트를 Azure Sentinel 작업 영역으로 스트리밍할 수 있습니다. 이렇게 하면 대시보드에서 Windows 보안 이벤트를 보고 이를 사용하여 사용자 지정 경고를 만들고 조사를 개선하여 조직의 네트워크에 대한 더 많은 인사이트를 제공하고 보안 운영 기능을 확장할 수 있습니다. 다음 <a name="event-sets"></a> 집합 중에서 스트리밍할 이벤트를 선택할 수 있습니다.

- **모든 이벤트** - 모든 Windows 보안 및 AppLocker 이벤트입니다.
- **일반** - 감사 용도 이벤트의 표준 집합입니다. 전체 사용자 감사 내역이 해당 집합에 포함됩니다. 예를 들어 사용자 로그인 및 사용자 로그아웃 이벤트(이벤트 ID 4624, 4634)가 모두 포함됩니다. 보안 그룹 변경 사항, 주요 도메인 컨트롤러 Kerberos 작업 및 허용된 모범 사례와 일치하는 기타 이벤트 유형 등도 있습니다.

    **공통** 이벤트 집합에는 일반적이지 않은 일부 이벤트 유형이 포함될 수 있습니다.  **일반적인** 집합에서는 전체 감사 내역 기능을 계속 유지하면서 이벤트 볼륨을 관리하기 쉬운 수준으로 줄이는 것이 중요하기 때문입니다.

- **최소** - 잠재적인 위협을 나타낼 수 있는 작은 이벤트 집합입니다. 이 집합은 전체 감사 내역을 포함하지 않습니다. 보안 위반 이벤트 및 발생 빈도는 낮지만 중요한 이벤트를 다룹니다. 예를 들어 로그온 성공/실패(이벤트 ID 4624, 4625)는 이 집합에 포함되지만 로그아웃 정보(4634)는 포함되지 않습니다. 로그아웃의 경우 감사에서는 중요하지만 위반 검색에서는 중요하지 않으며 양이 비교적 많기 때문입니다. 이 집합에 포함되는 데이터 볼륨 중 대부분은 로그인 이벤트 및 프로세스 만들기 이벤트(이벤트 ID 4688)입니다.

- **없음** - 보안 또는 AppLocker 이벤트가 없습니다. (이 설정은 커넥터를 사용하지 않도록 설정하는 데 사용됩니다.)

    다음 목록은 각 집합별 전체 보안 내역 및 App Locker 이벤트 ID를 제공합니다.

    | 이벤트 집합 | 수집된 이벤트 ID |
    | --- | --- |
    | **최소** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **일반** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> 단일 작업 영역의 컨텍스트 내에서 보안 이벤트 수집은 Azure Security Center 또는 Azure Sentinel에서 구성할 수 있지만 둘 다 함께 구성할 수는 없습니다. 이미 Azure Security Center로부터 Azure Defender 경고를 받고 있는 작업 영역에서 Azure Sentinel을 온보딩하고 보안 이벤트를 수집하도록 설정된 경우 두 가지 옵션이 있습니다.
> - Azure Security Center의 보안 이벤트 컬렉션은 그대로 둡니다. Azure Sentinel 및 Azure Defender에서 이러한 이벤트를 쿼리하고 분석할 수 있습니다. 그러나 커넥터의 연결 상태를 모니터링하거나 Azure Sentinel에서 해당 구성을 변경할 수 없습니다. 이것이 중요하다면 두 번째 옵션을 고려합니다.
>
> - Azure Security Center에서 [보안 이벤트 수집을 사용하지 않도록 설정](../security-center/security-center-enable-data-collection.md)한 다음, Azure Sentinel에서 보안 이벤트 커넥터를 추가합니다. 첫 번째 옵션과 마찬가지로 Azure Sentinel 및 Azure Defender/ASC에서 이벤트를 쿼리하고 분석할 수 있지만, 이제 Azure Sentinel에서만 커넥터의 연결 상태를 모니터링하거나 구성을 변경할 수 있습니다.

## <a name="set-up-the-windows-security-events-connector"></a>Windows 보안 이벤트 커넥터 설정하기

Azure Sentinel에서 Windows 보안 이벤트를 수집하기 위해서는

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **보안 이벤트** 를 클릭한 다음 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭합니다. 그런 다음이 섹션의 나머지 부분에 설명된 대로 **지침** 탭의 화면 지시를 따릅니다.

1. 커넥터 페이지의 **사전 요구 사항** 섹션에 설명된 대로 적절한 사용 권한이 있는지 확인합니다.

1. 보안 이벤트를 Azure Sentinel로 스트리밍하려는 컴퓨터에 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)(Microsoft Monitoring Agent 또는 MMA라고도 함)를 다운로드하여 설치합니다.

    Azure Virtual Machines용
    
    1. **Azure Windows Virtual Machine에서 에이전트 설치** 를 선택한 다음 아래에 표시되는 링크를 클릭합니다.
    1. 연결하려는 각 가상 머신에 대해 오른쪽에 표시되는 목록에서 해당 이름을 선택하고 **연결** 을 클릭합니다.

    비 Azure Windows 컴퓨터인 경우(물리적, 가상 온-프레미스 또는 다른 클라우드의 가상):

    1. **비 Azure Windows 컴퓨터에 에이전트 설치** 를 선택한 다음 아래에 표시되는 링크를 클릭합니다.
    1. **Windows 컴퓨터** 의 오른쪽에 표시되는 적절한 다운로드 링크를 클릭합니다.
    1. 다운로드한 실행 파일을 사용하여 선택한 Windows 시스템에 에이전트를 설치하고 위에서 언급한 다운로드 링크 아래에 표시되는 **작업 영역 ID 및 키** 를 사용하여 구성합니다.

    > [!NOTE]
    >
    > 인터넷이 연결되지 않은 Windows 시스템에서 Azure Sentinel로 이벤트를 스트리밍할 수 있도록 하려면 오른쪽 아래의 링크를 사용하여 별도의 머신에 **OMS 게이트웨이** 를 다운로드 및 설치하고 프록시로 작동하게 합니다.  이벤트를 수집하려는 각 Windows 시스템에는 Log Analytics 에이전트를 설치해야 합니다.
    >
    > 해당 시나리오에 대한 자세한 내용은 [**Log Analytics 게이트웨이** 설명서](../azure-monitor/agents/gateway.md)를 참조하세요.

    추가 설치 옵션 및 세부 정보는 [**Log Analytics 에이전트** 설명서](../azure-monitor/agents/agent-windows.md)를 참조하세요.

1. 스트리밍할 이벤트 집합([모두, 공통 또는 최소](#event-sets))을 선택합니다.

1. **업데이트** 를 클릭합니다.

1. Windows 보안 이벤트에 대한 Log Analytics에서 관련 스키마를 사용하려면 쿼리 창에 `SecurityEvent`를 입력합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 약 20분 정도 소요될 수 있습니다. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>비정상적인 RDP 로그인 검색에 대한 보안 이벤트 커넥터 구성

> [!IMPORTANT]
> 비정상적인 RDP 로그인 검색은 현재 퍼블릭 미리 보기로 제공됩니다.
> 해당 기능은 별도의 Service Level Agreement(서비스 수준 규약) 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel은 ML(기계 학습)을 보안 이벤트 데이터에 적용하여 비정상적인 RDP(원격 데스크톱 프로토콜) 로그인 활동을 식별할 수 있습니다. 다음과 같은 시나리오가 있습니다.

- **비정상적인 IP** -지난 30일 동안 IP 주소가 거의 또는 전혀 관찰되지 않았습니다.

- **비정상적인 지리적 위치** -지난 30일 동안 IP 주소, 구/군/시, 국가 및 ASN이 거의 또는 전혀 관찰되지 않았습니다.

- **새 사용자** -새 사용자가 IP 주소 및 지리적 위치에서 로그인하거나 둘 다 또는 어느 쪽도 30일 이전 데이터를 기반으로 하여 표시되지 않습니다.

**구성 지침**

1. **보안 이벤트** 데이터 커넥터를 통해 RDP 로그인 데이터(이벤트 ID 4624)를 수집해야 합니다. ‘없음’ 외에 Azure Sentinel로 스트리밍할 [이벤트 집합](#event-sets)을 선택했는지 확인합니다.

1. Azure Sentinel 포털에서 **분석** 을 클릭한 다음 **규칙 템플릿** 탭을 선택합니다. **(미리 보기)비정상적인 RDP 로그인 검색** 규칙을 선택하고 **상태** 슬라이더를 **사용** 으로 옮깁니다.

    > [!NOTE]
    > 기계 학습 알고리즘은 사용자 동작의 기준 프로필을 컴파일하는 데 30일 분량의 데이터가 필요하므로 인시던트 이전에 30일간의 보안 이벤트 데이터를 수집해야 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Windows 보안 이벤트를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색 시작하기.

