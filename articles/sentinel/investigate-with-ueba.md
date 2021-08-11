---
title: UEBA 데이터를 사용하여 인시던트 조사 | Microsoft Docs
description: 조사하는 동안 UEBA 데이터를 사용하여 조직에서 발생할 수 있는 악의적인 활동에 대한 더 많은 컨텍스트를 얻는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2021
ms.author: bagol
ms.openlocfilehash: 1a3f199276d5c9b04ab5ac117c022a63fd3fb866
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665320"
---
# <a name="investigate-incidents-with-ueba-data"></a>UEBA 데이터를 사용하여 인시던트 조사

이 문서에서는 일반 조사 워크플로에서 [UEBA(사용자 엔터티 동작 분석)](identify-threats-with-entity-behavior-analytics.md)를 사용하기 위한 일반적인 방법 및 샘플 절차에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

조사에서 UEBA 데이터를 사용하려면 먼저 [Azure Sentinel에서 UEBA(사용자 및 엔터티 동작 분석)를 사용하도록 설정](enable-entity-behavior-analytics.md)해야 합니다. 

UEBA가 사용하도록 설정되면 약 1주일 후에 컴퓨터 기반 인사이트 검색을 시작합니다.

## <a name="run-proactive-routine-searches-in-entity-data"></a>엔터티 데이터에서 일상적인 사전 검색 실행

사용자 작업을 통해 정기적인 사전 검색을 실행하여 추가 조사를 위한 단서를 만드는 것이 좋습니다.

Azure Sentinel [사용자 및 엔터티 동작 분석 통합 문서](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries)를 사용하여 다음과 같은 데이터를 쿼리할 수 있습니다.

- **가장 위험한 사용자** - 변칙 또는 연결된 인시던트가 있는 사용자
- **특정 사용자에 대한 데이터** - 주체가 실제로 손상되었는지 여부 또는 사용자 프로필에서 벗어난 작업으로 인한 내부자 위협이 있는지 여부를 확인합니다.

또한 UEBA 통합 문서에서 일상적이지 않은 작업을 캡처하고, 이를 사용하여 비정상적인 작업과 잠재적인 비준수 사례를 찾습니다.

### <a name="investigate-an-anomalous-sign-in"></a>비정상적인 로그인 조사

예를 들어 다음 단계에서는 이전에 한 번도 사용하지 않은 VPN에 연결한 사용자를 조사합니다. 이는 비정상적인 작업입니다.

1. Sentinel **통합 문서** 영역에서 **사용자 및 엔터티 동작 분석** 통합 문서를 검색하여 엽니다.
1. 조사할 특정 사용자 이름을 검색하고, **조사할 상위 사용자** 테이블에서 해당 이름을 선택합니다.
1. **인시던트 분석** 및 **변칙 분석** 테이블을 아래로 스크롤하여 선택한 사용자와 관련된 인시던트 및 변칙을 확인합니다.
1. **성공한 비정상 로그온** 과 같은 변칙에서 아래 표에 설명된 세부 정보를 검토하여 조사합니다. 예를 들어:

    |단계  |Description  |
    |---------|---------|
    |**오른쪽의 설명 참고**     |    각 변칙에는 [MITRE ATT&CK 기술 자료](https://attack.mitre.org/)에서 자세히 알아볼 수 있는 링크가 포함된 설명이 있습니다. <br>예를 들어: <br><br>        **_초기 액세스_* _ <br>_악의적 사용자가 네트워크에 연결하려고 합니다.* <br>* 초기 액세스는 다양한 입력 벡터를 사용하여 네트워크 내에서 초기 기반을 확보하는 기술로 구성됩니다. 기반을 확보하는 데 사용되는 기술로 대상이 지정된 스피어 피싱과 퍼블릭 웹 서버의 취약성 악용이 있습니다. 초기 액세스를 통해 확보된 기반은 유효한 계정 및 외부 원격 서비스 사용과 같은 지속적인 액세스를 허용하거나 암호 변경으로 인해 사용이 제한될 수 있습니다.*     |
    |**설명 열의 텍스트 참고**     |   변칙 행에서 오른쪽으로 스크롤하여 추가 설명을 확인합니다. 전체 텍스트를 보려면 링크를 선택합니다. 예를 들어: <br><br> *악의적 사용자는 자격 증명 액세스 기술을 사용하여 특정 사용자 또는 서비스 계정의 자격 증명을 도용하거나, 초기 액세스를 얻기 위해 소셜 엔지니어링을 통해 정찰 프로세스 초기에 자격 증명을 캡처할 수 있습니다. 예를 들어 APT33은 유효한 계정을 초기 액세스에 사용했습니다. 아래의 쿼리는 사용자가 이전에 연결한 적이 없고 피어도 없는 새 지리적 위치에서 수행한 성공적인 로그인에 대한 출력을 생성합니다.*     |
    |**UsersInsights 데이터 참고**     |  변칙 행에서 오른쪽으로 더 스크롤하여 계정 표시 이름 및 계정 개체 ID와 같은 사용자 인사이트 데이터를 확인합니다. 오른쪽에서 전체 데이터를 보려면 텍스트를 선택합니다.         |
    |**증거 데이터 참고**     |  변칙 행에서 오른쪽으로 더 스크롤하여 변칙에 대한 증거 데이터를 확인합니다. 오른쪽에서 전체 데이터를 보려면 다음 필드와 같은 텍스트를 선택합니다. <br><br>-  **ActionUncommonlyPerformedByUser** <br>- **UncommonHighVolumeOfActions** <br>- **FirstTimeUserConnectedFromCountry** <br>- **CountryUncommonlyConnectedFromAmongPeers** <br>- **FirstTimeUserConnectedViaISP** <br>- **ISPUncommonlyUsedAmongPeers** <br>- **CountryUncommonlyConnectedFromInTenant** <br>- **ISPUncommonlyUsedInTenant** |
    |     |         |

**사용자 및 엔터티 동작 분석** 통합 문서에 있는 데이터를 사용하여 사용자 작업이 의심스럽고 추가 작업이 필요한지 여부를 확인합니다.

## <a name="use-ueba-data-to-analyze-false-positives"></a>UEBA 데이터를 사용하여 가양성 분석

조사에서 캡처된 인시던트가 가양성인 경우가 있습니다.

가양성의 일반적인 예는 동일한 시간 내에 뉴욕과 런던에서 애플리케이션 또는 포털에 로그인한 사용자와 같이 불가능한 이동 작업이 검색되는 경우입니다. Azure Sentinel은 불가능한 이동을 변칙으로 표시하지만, 사용자를 조사하면 VPN이 사용자가 실제로 있었던 대체 위치와 함께 사용되었음을 명확히 알 수 있습니다.

### <a name="analyze-a-false-positive"></a>가양성 분석

예를 들어 **불가능한 이동** 인시던트인 경우 VPN이 사용되었음을 사용자에게 확인한 후 인시던트에서 사용자 엔터티 페이지로 이동합니다. 여기에 표시된 데이터를 사용하여 캡처된 위치가 일반적으로 알려진 사용자의 위치에 포함되는지 여부를 확인합니다.

예를 들어:

[ ![인시던트의 사용자 엔터티 페이지 열기](media/ueba/open-entity-pages.png) ](media/ueba/open-entity-pages.png#lightbox)

사용자 엔터티 페이지는 [인시던트 페이지](tutorial-investigate-cases.md#how-to-investigate-incidents) 자체와 [조사 그래프](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)에서도 연결됩니다.

> [!TIP]
> 인시던트와 관련된 특정 사용자에 대한 사용자 엔터티 페이지의 데이터를 확인한 후 Azure Sentinel **헌팅** 영역으로 이동하여 사용자의 피어가 일반적으로 동일한 위치에서 연결되는지 여부를 파악합니다. 그렇다면 이 지식은 가양성에 대한 훨씬 더 강력한 사례를 만듭니다.
>
> **헌팅** 영역에서 **비정상적인 지리적 위치 로그온** 쿼리를 실행합니다. 자세한 내용은 [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)을 참조하세요.
>

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>암호 스프레이 및 스피어 피싱 시도 식별

MFA(다단계 인증)가 사용하도록 설정되지 않으면 사용자 자격 증명은 [암호 스프레이](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/) 또는 [스피어 피싱](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/) 시도로 공격을 손상시키려는 공격자에게 취약합니다.

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>UEBA 인사이트를 사용하여 암호 스프레이 인시던트 조사

예를 들어 UEBA 인사이트를 사용하여 암호 스프레이 인시던트를 조사하려면 다음을 수행하여 자세히 알아볼 수 있습니다.

1. 인시던트의 왼쪽 아래에서 **조사** 를 선택하여 공격의 대상이 될 수 있는 계정, 컴퓨터 및 기타 데이터 요소를 확인합니다.

    데이터를 검색하면 로그온 실패 횟수가 비교적 많은 관리자 계정이 표시될 수 있습니다. 이 경우 의심스럽지만 추가 확인 없이 계정을 제한하지 않을 수 있습니다.

1. 맵에서 관리 사용자 엔터티를 선택한 다음, 오른쪽에서 **인사이트** 를 선택하여 시간이 지남에 따른 로그인 그래프와 같은 자세한 정보를 찾습니다.

1. 오른쪽에서 **정보** 를 선택한 다음, **전체 세부 정보 보기** 를 선택하여 더 자세히 드릴다운할 수 있는 [사용자 엔터티 페이지](identify-threats-with-entity-behavior-analytics.md#entity-pages)로 이동합니다. 

    예를 들어 사용자의 첫 번째 잠재적 암호 스프레이 인시던트인지 여부를 확인하거나, 사용자의 로그인 기록을 조사하여 비정상적인 오류인지 여부를 파악합니다.

> [!TIP]
> **실패한 비정상 로그온** [헌팅 쿼리](hunting.md)를 실행하여 조직의 실패한 비정상 로그온을 모두 모니터링할 수도 있습니다. 쿼리의 결과를 사용하여 가능한 암호 스프레이 공격에 대한 조사를 시작합니다.
>

## <a name="next-steps"></a>다음 단계

UEBA, 조사 및 헌팅에 대해 자세히 알아보세요.

- [Azure Sentinel에서 UEBA(사용자 및 엔터티 동작 분석)를 사용하여 고급 위협 식별](identify-threats-with-entity-behavior-analytics.md)
- [Azure Sentinel UEBA 보강 참조](ueba-enrichments.md)
- [자습서: Azure Sentinel을 사용하여 인시던트 조사](tutorial-investigate-cases.md)
- [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)
