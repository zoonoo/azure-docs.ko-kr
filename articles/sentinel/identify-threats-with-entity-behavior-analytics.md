---
title: Azure 센티널의 UEBA (사용자 및 엔터티 동작 분석)를 사용 하 여 고급 위협 식별 | Microsoft Docs
description: 엔터티 (사용자, 호스트 이름, IP 주소)에 대 한 동작 기준을 만들고이를 사용 하 여 비정상적인 동작을 감지 하 고 제로 APT (고급 영구 위협)를 식별 합니다.
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
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: 657221a2acbf592a56cb4659ced2199023bc5e5b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658794"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Azure 센티널의 UEBA (사용자 및 엔터티 동작 분석)를 사용 하 여 고급 위협 식별

> [!IMPORTANT]
>
> - UEBA 및 엔터티 페이지 기능은 이제 다음 Azure 센티널 지역 및 지역에서 **일반** 공급으로 제공 됩니다.
>    - 미국 지리
>    - 유럽 서 부 지역
>    - 오스트레일리아 지리
>
> - 다른 모든 지역 및 지역에서 이러한 기능은 **미리 보기** 기간 동안 유지 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [**Microsoft Azure 미리 보기에**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>사용자 및 엔터티 동작 분석 (UEBA) 이란?

### <a name="the-concept"></a>개념

조직 내에서 위협 식별, 잠재적 영향-손상 된 엔터티 또는 악의적인 참가자가 항상 시간과 노력이 많이 걸리는 프로세스 인지 여부를 확인 합니다. 경고를 받고 하 고 점 및 활성 검색을 모두 사용 하면 최소한의 반환으로 많은 시간과 노력을 소요 된 하 고 정교한 위협으로 인해 검색을 evading 수 있습니다. 0 일, 대상이 지정 된 고급 및 고급 영구적 위협 등의 까다로운 위협은 조직에 가장 위험할 수 있으므로 검색은 더 중요 합니다.

Azure 센티널의 UEBA 기능을 통해 분석가의 워크 로드에서 drudgery 하 고, 노력 으로부터 불확실성을 제거 하 고, 조사 및 수정에 집중할 수 있도록 고성능의 실행 가능한 인텔리전스를 제공 합니다.

Azure 센티널은 모든 연결 된 데이터 원본에서 로그 및 경고를 수집 하 고,이를 분석 하 고 조직 엔터티 (사용자, 호스트, IP 주소, 응용 프로그램 등)의 기준 동작 프로필을 시간 및 피어 그룹 가로선으로 빌드합니다. 센티널은 다양 한 기술 및 기계 학습 기능을 사용 하 여 비정상적인 활동을 식별 하 고 자산이 손상 되었는지 여부를 확인 하는 데 도움이 됩니다. 뿐만 아니라 특정 자산의 상대적인 민감도를 파악 하 고, 자산의 피어 그룹을 식별 하 고, 손상 된 특정 자산의 잠재적 영향 ("폭발 반경")을 평가할 수도 있습니다. 이 정보를 사용 하 여 조사 및 인시던트 처리의 우선 순위를 효과적으로 지정할 수 있습니다. 

### <a name="architecture-overview"></a>아키텍처 개요

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="엔터티 동작 분석 아키텍처":::

### <a name="security-driven-analytics"></a>보안 기반 분석

UEBA 솔루션에 대 한 Gartner의 패러다임에서 제공 되는 Azure 센티널은 세 가지 참조 프레임을 기반으로 하는 "외부" 접근 방식을 제공 합니다.

- **사용 사례:** 다양 한 엔터티를 kill 체인의 교착 상태가 발생, perpetrators 또는 pivot 점으로 사용 하는 MITRE AT&T&접시 헤드 프레임 워크를 기반으로 하는 관련 공격 벡터 및 시나리오에 대 한 우선 순위를 설정 합니다. Azure 센티널은 각 데이터 원본이 제공할 수 있는 가장 중요 한 로그에 초점을 맞춘 것입니다.

- **데이터 원본:** Azure 데이터 원본을 먼저 지원 하는 반면, Azure 센티널 신중는 타사 데이터 원본을 선택 하 여 위협 시나리오와 일치 하는 데이터를 제공 합니다.

- **분석:** 다양 한 기계 학습 (기계 학습) 알고리즘을 사용 하 여 Azure 센티널은 비정상적인 활동을 식별 하 고, 상황별 강화 형식으로 증거를 명확 하 게 제시 합니다. 이러한 몇 가지 예가 아래에 나와 있습니다.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="동작 분석의 외부 접근 방식":::

Azure 센티널은 보안 분석가가 컨텍스트의 비정상적인 활동을 명확 하 게 이해 하 고 사용자의 기준 프로필을 비교 하는 데 도움이 되는 아티팩트를 제공 합니다. 사용자 (또는 호스트 또는 주소)가 수행 하는 작업은 컨텍스트 평가 됩니다. 여기서 "true" 결과는 식별 된 비정상을 나타냅니다.
- 지리적 위치, 장치 및 환경에서
- 시간 및 빈도 horizons (사용자 고유의 기록과 비교).
- 피어 동작과 비교 합니다.
- 조직의 동작과 비교해 볼 수 있습니다.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="엔터티 컨텍스트":::


### <a name="scoring"></a>점수 매기기

각 활동에는 "조사 우선 순위 점수"로 점수가 매겨집니다 .이는 사용자 및 해당 피어의 동작을 기반으로 특정 활동을 수행 하는 특정 사용자의 확률을 결정 합니다. 가장 비정상적인 것으로 식별 되는 활동은 가장 높은 점수 (0-10 배율)를 수신 합니다.

작동 방식에 대 한 예제는 [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) 에서 동작 분석을 사용 하는 방법을 참조 하세요.

## <a name="entities-in-azure-sentinel"></a>Azure 센티널의 엔터티

### <a name="entity-identifiers"></a>엔터티 식별자

경고가 Azure 센티널로 전송 되 면 Azure 센티널에서 엔터티 (예: 사용자 계정, 호스트, IP 주소 등)로 식별 하 고 분류 하는 데이터 요소를 포함 합니다. 경우에 따라 경고에 엔터티에 대 한 충분 한 정보가 포함 되어 있지 않은 경우이 식별이 쉽지 않을 수 있습니다.

예를 들어 사용자 계정은 Azure AD 계정의 숫자 식별자 (GUID) 또는 UPN (사용자 계정 이름) 값을 사용 하거나 사용자 이름 및 해당 NT 도메인 이름을 조합해 서 사용 하 여 두 가지 이상의 방법으로 식별할 수 있습니다. 서로 다른 데이터 원본으로 동일한 사용자를 식별할 수 있습니다. 따라서 가능 하면 Azure 센티널은 적절 하 게 식별 될 수 있도록 해당 식별자를 단일 엔터티로 병합 합니다.

그러나 리소스 공급자 중 하나에서 엔터티가 충분히 식별 되지 않은 경고 (예: 도메인 이름 컨텍스트를 사용 하지 않는 사용자 이름)를 생성 하는 경우에 발생할 수 있습니다. 이러한 경우, 사용자 엔터티는 동일한 사용자 계정의 다른 인스턴스와 병합 될 수 없습니다 .이는 별도의 엔터티로 식별 되며 이러한 두 엔터티는 통합이 아닌 별도의 상태로 유지 됩니다.

이러한 상황이 발생 하는 위험을 최소화 하기 위해 모든 경고 공급자가 생성 하는 경고에서 엔터티를 제대로 식별 하는지 확인 해야 합니다. 또한 사용자 계정 엔터티를 Azure Active Directory와 동기화 하면 통합 디렉터리가 생성 되어 사용자 계정 엔터티를 병합할 수 있습니다.

다음 유형의 엔터티는 현재 Azure 센티널에서 식별 됩니다.

- 사용자 계정 (계정)
- 호스트
- Ip 주소 (ip)
- 맬웨어
- 파일
- 프로세스
- 클라우드 응용 프로그램 (CloudApplication)
- 도메인 이름 (DNS)
- Azure 리소스
- 파일 (FileHash)
- 레지스트리 키
- 레지스트리 값
- 보안 그룹
- URL
- IoT 디바이스
- 사서함
- 메일 클러스터
- 메일 메시지
- 전송 메일

### <a name="entity-pages"></a>엔터티 페이지

검색, 경고 또는 조사에서 엔터티 (현재 사용자 및 호스트로 제한 됨)가 발생 하면 엔터티를 선택 하 고 엔터티 **페이지로** 이동 하 여 해당 엔터티에 대 한 유용한 정보를 모두 볼 수 있습니다. 이 페이지에서 찾을 수 있는 정보 유형에는 엔터티에 대 한 기본 팩트,이 엔터티와 관련 된 주목할 만한 이벤트의 타임 라인 및 엔터티 동작에 대 한 정보가 포함 됩니다.
 
엔터티 페이지는 다음 세 부분으로 구성 됩니다.
- 왼쪽 패널에는 Azure Active Directory, Azure Monitor, Azure Security Center, Microsoft Defender와 같은 데이터 원본에서 수집 된 엔터티의 식별 정보가 포함 되어 있습니다.

- 가운데 패널에는 경고, 책갈피 및 작업과 같이 엔터티와 관련 된 주목할 만한 이벤트의 그래픽 및 텍스트 타임 라인이 표시 됩니다. 활동은 Log Analytics에서 주목할 만한 이벤트의 집계입니다. 이러한 활동을 검색 하는 쿼리는 Microsoft 보안 연구 팀에서 개발 합니다.

- 오른쪽 패널은 엔터티에 대 한 동작 정보를 제공 합니다. 이러한 정보는 변칙 및 보안 위협을 빠르게 식별 하는 데 도움이 됩니다. 이 정보는 Microsoft 보안 연구 팀에서 개발 되었으며 변칙 검색 모델을 기반으로 합니다.

### <a name="the-timeline"></a>타임 라인

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="엔터티 페이지 타임 라인":::

타임 라인은 Azure 센티널의 동작 분석에 대 한 엔터티 페이지 기여의 주요 부분입니다. 엔터티 관련 이벤트에 대 한 스토리를 제공 하 여 특정 시간 프레임 내에서 엔터티의 작업을 이해 하도록 도와줍니다.

몇 가지 미리 설정 된 옵션 (예: *최근 24 시간*) 중에서 **시간 범위** 를 선택 하거나 사용자 지정 된 시간 프레임으로 설정할 수 있습니다. 또한 타임 라인의 정보를 특정 유형의 이벤트 또는 경고로 제한 하는 필터를 설정할 수 있습니다.

타임 라인에는 다음과 같은 유형의 항목이 포함 되어 있습니다.

- 경고-엔터티가 **매핑된 엔터티로** 정의 된 모든 경고입니다. 조직에서 [분석 규칙을 사용 하 여 사용자 지정 경고](./tutorial-detect-threats-custom.md)를 만든 경우 규칙의 엔터티 매핑이 제대로 수행 되었는지 확인 해야 합니다.

- 책갈피-페이지에 표시 된 특정 엔터티를 포함 하는 책갈피입니다.

- 활동-엔터티와 관련 된 주목할 만한 이벤트의 집계입니다. 
 
### <a name="entity-insights"></a>엔터티 정보
 
Entity insights는 분석가가 더 효율적이 고 효과적으로 조사 하는 데 도움이 되도록 Microsoft 보안 연구원에 의해 정의 된 쿼리입니다. 정보는 엔터티 페이지의 일부로 제공 되며 호스트와 사용자에 대 한 중요 한 보안 정보를 표 형식 데이터 및 차트 형식으로 제공 합니다. 여기에 정보를 입력 하면 Log Analytics 우회 필요가 없습니다. 이 정보에는 로그인, 그룹 추가, 비정상 이벤트 등에 대 한 데이터가 포함 되며 비정상적인 동작을 검색 하는 고급 ML 알고리즘이 포함 되어 있습니다. 정보는 다음과 같은 데이터 형식을 기반으로 합니다.
- syslog
- SecurityEvent
- 감사 로그
- 로그인 로그
- Office 활동
- BehaviorAnalytics (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>엔터티 페이지를 사용 하는 방법

엔터티 페이지는 여러 사용 시나리오의 일부로 설계 되었으며 인시던트 관리, 조사 그래프, 책갈피에서 액세스 하거나 Azure 센티널 주 메뉴의 **엔터티 동작 분석** 에 있는 엔터티 검색 페이지에서 직접 액세스할 수 있습니다.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="엔터티 페이지 사용 사례":::


## <a name="data-schema"></a>데이터 스키마

### <a name="behavior-analytics-table"></a>동작 분석 테이블

| 필드                     | 설명                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | 테 넌 트의 고유 ID 번호                                      |
| SourceRecordId            | EBA 이벤트의 고유 ID 번호                                   |
| TimeGenerated             | 활동 발생의 타임 스탬프                              |
| TimeProcessed 됨             | EBA 엔진에서 작업을 처리 하는 타임 스탬프            |
| ActivityType              | 활동의 상위 수준 범주                                 |
| ActionType                | 활동의 정규화 된 이름입니다.                                     |
| UserName                  | 활동을 시작한 사용자의 사용자 이름입니다.                    |
| UserPrincipalName         | 활동을 시작한 사용자의 전체 사용자 이름입니다.               |
| EventSource               | 원래 이벤트를 제공한 데이터 원본                        |
| Sourceipaddress의 경우           | 작업이 시작 된 IP 주소                        |
| SourceIPLocation          | 활동이 시작 된 국가 이며, IP 주소에서 보강 합니다. |
| SourceDevice              | 활동을 시작한 장치의 호스트 이름                  |
| DestinationIPAddress      | 활동 대상의 IP 주소                            |
| DestinationIPLocation     | 활동 대상의 국가 이며, IP 주소에서 보강 합니다.     |
| DestinationDevice         | 대상 장치의 이름                                           |
| **UsersInsights**         | 참여 하는 사용자의 상황별 강화                            |
| **DevicesInsights**       | 관련 장치의 상황별 강화                          |
| **ActivityInsights**      | 프로 파일링을 기반으로 하는 활동의 상황별 분석              |
| **InvestigationPriority** | 이상 점수, 0-10 (0 = 양성, 10 = 매우 비정상)         |
|

### <a name="querying-behavior-analytics-data"></a>동작 분석 데이터 쿼리

[KQL](/azure/data-explorer/kusto/query/)를 사용 하 여 동작 분석 테이블을 쿼리할 수 있습니다.

예를 들어 Azure 리소스에 로그인 하지 못한 사용자의 모든 사례를 찾으려고 하 고, 사용자가 지정 된 국가에서 연결을 처음 시도 하는 경우, 해당 국가에서의 연결은 일반적으로 사용자의 피어에 대해서도 발생 하지 않습니다. 다음 쿼리를 사용할 수 있습니다.

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>사용자 피어 메타 데이터-테이블 및 노트북

사용자 피어의 메타 데이터는 위협 검색에서 중요 한 컨텍스트를 제공 하 고, 인시던트를 조사 하 고, 잠재적 위협에 대 한 검색을 제공 합니다. 보안 분석가는 사용자의 피어에 대 한 정상적인 활동을 관찰 하 여 사용자의 활동이 사용자의 동료와 비교 하 여 비정상적인 지 확인할 수 있습니다.

Azure **센티널은 사용자** 의 Azure AD 보안 그룹 구성원 자격, 메일 그룹, et 오일을 1-20을 기반으로 사용자의 동료를 계산 하 고 순위를 매깁니다. 아래 스크린샷은 UserPeerAnalytics 테이블의 스키마를 표시 하 고 사용자 Kendall Collins의 상위 8 개 순위 피어를 표시 합니다. Azure 센티널은 *빈도-역 문서 빈도* (TF IDF) 알고리즘을 사용 하 여 순위 계산을 위해 평가 계수를 표준화 합니다. 그룹이 작을수록 가중치가 높아집니다. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="사용자 피어 메타 데이터 테이블의 스크린샷":::

Azure 센티널 GitHub 리포지토리에 제공 된 [Jupyter 노트북](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) 을 사용 하 여 사용자 피어 메타 데이터를 시각화할 수 있습니다. 노트북을 사용 하는 방법에 대 한 자세한 내용은 [단계별 분석-사용자 보안 메타 데이터](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) 노트북을 참조 하세요.

### <a name="permission-analytics---table-and-notebook"></a>권한 분석-테이블 및 노트북

권한 분석은 공격자의 조직 자산의 손상으로 인 한 잠재적 영향을 확인 하는 데 도움이 됩니다. 이러한 영향을 자산의 "폭발 반지름이" 라고도 합니다. 보안 분석가는이 정보를 사용 하 여 조사 및 인시던트 처리의 우선 순위를 지정할 수 있습니다.

Azure 센티널은 사용자가 직접 또는 그룹 또는 서비스 주체를 통해 액세스할 수 있는 Azure 구독을 평가 하 여 지정 된 사용자가 Azure 리소스에 대해 보유 한 직접 및 전이적 액세스 권한을 결정 합니다. 이 정보는 물론 사용자의 Azure AD 보안 그룹 멤버 자격에 대 한 전체 목록이 **Useraccessanalytics** 테이블에 저장 됩니다. 아래 스크린샷은 사용자 Alex Johnson에 대 한 UserAccessAnalytics 테이블의 예제 행을 보여 줍니다. **원본 엔터티** 는 사용자 또는 서비스 주체 계정이 고, **대상 엔터티** 는 원본 엔터티가 액세스할 수 있는 리소스입니다. **액세스 수준** 및 **액세스 형식의** 값은 대상 엔터티의 액세스 제어 모델에 따라 달라 집니다. Alex에 Azure 구독 *Contoso 호텔 테 넌 트* 에 대 한 참가자 액세스 권한이 있는 것을 볼 수 있습니다. 구독의 액세스 제어 모델은 RBAC입니다.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="사용자 액세스 분석 테이블의 스크린샷":::

Azure 센티널 GitHub 리포지토리에서 [Jupyter 노트북](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (위에서 언급 한 것과 동일한 노트북)을 사용 하 여 권한 분석 데이터를 시각화할 수 있습니다. 노트북을 사용 하는 방법에 대 한 자세한 내용은 [단계별 분석-사용자 보안 메타 데이터](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) 노트북을 참조 하세요.

### <a name="hunting-queries-and-exploration-queries"></a>구하기 쿼리 및 탐색 쿼리

Azure 센티널은 BehaviorAnalytics 테이블을 기반으로 하는 일련의 구하기 쿼리, 탐색 쿼리 및 통합 문서를 제공 합니다. 이러한 도구는 비정상적인 동작을 나타내는 특정 사용 사례에 초점을 맞춘 보강 데이터를 제공 합니다. 

Azure 센티널의 [구하기 및 조사 그래프](./hunting.md) 에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널의 엔터티 동작 분석 기능에 대해 알아보았습니다. 구현에 대 한 유용한 지침을 제공 하 고 얻은 통찰력을 사용 하려면 다음 문서를 참조 하세요.

- Azure 센티널에서 [엔터티 동작 분석을 사용 하도록 설정](./enable-entity-behavior-analytics.md) 합니다.
- [보안 위협에 대 한 헌트](./hunting.md).