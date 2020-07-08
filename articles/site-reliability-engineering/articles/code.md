---
title: 'FAQ: SRE 및 코딩 | Microsoft Docs'
titleSuffix: Azure
description: 'FAQ: SRE와 코딩 간의 관계 이해'
author: dnblankedelman
manager: efreeman
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 06/19/2020
ms.author: dnb
ms.openlocfilehash: 7003844878fcd23b20957c393ac2ed011f5f5946
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834940"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>질문과 대답: SRE와 관련 하 여 코드를 실행 하는 방법을 알고 있어야 하나요?

사용자가 sre에 참여 하는 것을 고려 하 고 있는 경우 팀에서 SRE를 사용할 때 발생 하는 일반적인 질문은 "코딩 방법을 알아야 하나요?"입니다.

간단한 대답: 예. 

그러나 전체 답은 약간 더 미묘한. 코딩을 사용 하는 세 가지 위치를 살펴보겠습니다. 여기에는 각각에 필요한 코딩 전문 지식 수준과 함께 사이트 안정성 엔지니어링이 제공 됩니다. 이 목록은 완전 하지 않지만 이러한 시나리오는 몇 가지 일반적인 사용 사례입니다.

## <a name="scenario-1-removing-toil-through-automation"></a>시나리오 1: 자동화를 통해 toil 제거

사이트 안정성 엔지니어 및 SRE 사례를 사용 하는 다른 사용자는 언제 든 지 toil를 제거할 수 있습니다. "Toil"은 SRE의 특정 항목을 의미 합니다. Toil은 특정 특성을 가진 사람이 수행 하는 작업 작업을 나타냅니다. 번거로운 작업에는 장기간 사용되는 값이 없습니다. 의미 있는 방식으로 서비스를 개선하지 않습니다. 종종 반복적이며 자동화될 수 있더라도 대부분 수동 작업입니다. 시간에 따라 서비스 또는 시스템의 규모가 커지면 해당 시스템에 대한 요청의 수도 비례해서 양이 증가하고 수동 작업도 더 많이 필요해질 수 있습니다.

예를 들어 서비스에서 SRE 팀이 매주 항목을 다시 설정 하거나 새 계정과 디스크 공간을 수동으로 프로 비전 하거나 수동으로 다시 시작 하는 경우 (이는 toil 된 작업 부하) 해당 작업을 완료해도 장기적으로나 영구적으로 서비스가 개선되지 않습니다. 이러한 작업은 거듭 반복해야 할 가능성이 큽니다.

SRE는 번거로운 작업을 싫어합니다. 적절한 경우 최대한 제거하기 위해 노력합니다. 이 부분이 SRE에서 자동화가 제 역할을 톡톡히 하는 영역입니다. 이러한 요청을 자동으로 처리할 수 있는 경우 팀에서 더 많은 더욱 생산적인 작업을 수행 하 고 작업을 수행할 수 있습니다.

*코딩 전문 지식*: automation에는 몇 가지 코딩 전문 기술이 필요 하지만 전체 소프트웨어 엔지니어링 기술이 필요 하지는 않습니다. 작은 스크립트 (예를 들어, PowerShell 또는 Bourne shell)를 작성 하거나 거의 모든 코드로을 만드는 경우에도 [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) 이 앱은 여전히 toil을 제거 하는 데 도움이 될 수 있습니다.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>시나리오 2: Api/Dsl (도메인별 언어)/템플릿을 통한 제어

SRE 작업에 반드시 필요 하지는 않지만 Api, Dsl 및 템플릿 (특히 클라우드 환경)을 통해 환경을 제어할 수 있으면 Sre에서 작업을 확장할 수 있습니다. 인프라 프로 비전/프로 비전 해제, 모니터링 구성 및 여러 서비스 통합은 코딩을 통해 훨씬 더 효율적입니다.

*코딩 전문 지식*: 이전 시나리오와 마찬가지로이 경우 코딩 전문 지식이 필요 하지만 전체 소프트웨어 엔지니어링 기술이 필요 하지는 않습니다. 이전에 언급 한 스크립트 및 논리 앱 외에 [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) 도를 최소한의 코딩 환경에서 사용할 수 있습니다.

## <a name="scenario-3-fixing-the-code"></a>시나리오 3: 코드 수정

사이트 안정성 엔지니어는 시스템의 안정성을 향상 시키는 방법을 살펴봅니다. 이러한 목표를 달성 하려면 시스템의 소스 코드를 확인 하 여 문제를 확인 하 고 코드 베이스에 수정 사항을 다시 참여 해야 하는 경우가 있습니다. 이러한 작업의 복잡성 수준은 상황에 따라 크게 다를 수 있지만 코딩 전문 지식은 이러한 경우에는 명확한 요구 사항입니다.

*코딩 전문*지식:이 시나리오에서는 전체 소프트웨어 엔지니어링 전문 지식이 필요한 경우가 많습니다.


## <a name="next-steps"></a>다음 단계

사이트 안정성 엔지니어링 및 낮은 코드 작업에 대해 자세히 알아보고 싶으십니까? 위에 링크 된 제품 설명서 인 [사이트 안정성 엔지니어링 허브](../index.yml)를 확인 하세요.
