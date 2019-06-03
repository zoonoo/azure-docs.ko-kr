---
title: 프로젝트 개인 정보 채팅이란?
titlesuffix: Azure Cognitive Services
description: 이 문서는 봇의 대화형 기능을 향상시키기 위한 클라우드 기반 API인 Azure Project Personality Chat에 대한 개요입니다.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "62103622"
---
# <a name="what-is-project-personality-chat"></a>프로젝트 개인 정보 채팅이란?

Project Personality Chat은 별개의 선택된 성격에 따라 짧은 대화를 처리하여 봇의 대화형 기능을 향상시킵니다. Personality Chat은 의도 분류자를 사용하여 일반적인 짧은 대화 의도를 식별하고 성격과 일치하는 응답을 생성합니다. 의도 및 신뢰성 점수에 따라 봇은 조정된 편집 기준에서 최상의 응답을 선택하거나 DNN(심층 신경망 네트워크)을 사용하여 실시간으로 응답을 생성하도록 선택합니다. 세 가지 기본 가상 사용자 중에 선택할 수 있습니다. 가상 사용자 모델은 선택한 성격과 가장 유사한 응답을 반환합니다.

일반적인 짧은 대화 쿼리에 대한 사용자 지정이 가능한 편집 집합을 사용할 수 있습니다. Microsoft Bot Framework SDK를 사용하여 쉽게 통합할 수 있습니다. 이 SDK는 처음부터 응답을 작성하는 시간 및 비용을 줄여줍니다.

## <a name="getting-started-with-project-personality-chat"></a>Project Personality Chat 시작

Project Personality Chat 랩 페이지를 방문하여 사용 가능한 데모로 채팅하고, 서비스가 공급될 때 사전 액세스를 요청할 수 있습니다.
또한 현재 Microsoft Bot Framework SDK를 통해 사용자 지정 편집 전용 라이브러리를 봇에 통합할 수 있습니다. <br>
[샘플: 봇에 Personality Chat 통합](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Personality Chat 라이브러리 사용해보기](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>신경망을 사용하여 응답 생성

Personality Chat은 심층 학습 모델을 사용하여 일반적인 대화 패턴을 학습하고 응답을 생성합니다. 응답 생성 모델은 RNN(순환 신경망)입니다. 이 모델은 인간 상호 작용의 패턴을 이해하고 학습하는 수백만 개의 대화에서 학습됩니다. 학습된 문장 구조 패턴을 사용하여 새로운 쿼리가 형성되고 응답이 생성됩니다. 이 새 응답을 생성할 때 모델은 단어의 “쓰기 어휘”를 통해 검색하고 응답에 N 상위 첫 번째 단어를 선택합니다. 빔 검색을 사용하여 계속해서 각 생성된 첫 번째 단어에 대해 N 상위 두 번째 단어를 검색합니다. 응답은 모델이 “EOS(문장의 끝)” 단어를 선택하면 완료되었다고 간주됩니다. 모든 응답이 완료되면 전체 응답에 대한 확률 점수를 기준으로 N 상위 응답을 선택합니다.

모델은 컨텍스트에 따라 “구조”가 올바르고 컨텍스트에 따라 적절한 전환을 생성하는 것을 학습합니다. 예를 들어 “이제 이야기하시겠습니까?”란 질문은 타당한 회신의 구조에 대해 상당히 많은 정보를 말해줍니다. 회신은 아마도 “네”나 “아니오”의 다른 표현으로 시작될 것이며 인칭 대명사는 “나”일 가능성이 있습니다. 또한 “아니오” 응답은 공손한 설명을 포함할 가능성이 더 높습니다.

시스템은 대화의 기본 구조에 대한 언어 모델을 학습하려고 시도합니다. 이 구조는 주제, 성격 등과 같은 외부 제약 조건의 영향을 일부 받습니다.  이러한 제약 조건은 광범위한 패턴에서 학습되므로 짧은 대화를 위한 애플리케이션에 적합합니다(단, 이에 국한되지 않음).

![응답 생성을 위한 시퀀스 모델에 대한 시퀀스](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>성격 모델링

 모든 데이터 기반 대화형 모델을 사용하면 일관성 있는 “성격”을 일관되게 유지해야 하는 문제가 있습니다. 가상 사용자는 대화형 상호 작용 동안에 발생하는 성격으로 정의됩니다. 가상 사용자는 ID, 언어 동작 및 상호 작용 스타일의 요소로 구성되었다고 볼 수 있습니다. Personality Chat의 현재 버전에서는 언어 동작 및 상호 작용 스타일에 집중합니다.

이 모델은 각 개별 화자를 벡터 또는 임베딩으로 나타냅니다. 그들의 응답 콘텐츠 및 스타일에 영향을 주는 화자의 정보를 인코딩합니다. 그런 다음, 모델은 다른 화자가 제공하는 대화형 콘텐츠를 기반으로 화자의 표현을 학습합니다. 유사한 응답을 사용하는 화자는 벡터 공간에서 가까운 위치를 차지하는 유사한 임베딩을 가지는 경향이 있습니다. 이 방법에서 벡터 공간에 인접한 화자의 학습 데이터는 화자 모델의 일반화 기능을 향상시키는 데 도움이 됩니다. 모델은 “가상 사용자 ID”를 사용하여 가상 사용자 클러스터를 구성하기 위해 벡터 공간에서 유사한 표현이 있는 화자를 효과적으로 클러스터링합니다.

기본 가상 사용자의 경우 특성 및 조정된 응답을 사용하여 가장 유사하게 일치하는 화자 클러스터를 찾습니다. 그런 다음, 이 클러스터는 각 기본 성격의 가상 사용자 ID로 선택됩니다. 지속적인 사용자 지정은 봇/브랜드 응답 집합을 사용하여 어떠한 종류의 성격에서도 수행할 수 있습니다. 그런 다음, 개인의 가상 사용자가 선호하는 언어 응답 동작 및 다른 주요 특성을 정확하게 에뮬레이트하는 대화가 이루어집니다.

![화자 클러스터를 사용한 가상 사용자 모델링](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>짧은 대화 의도 이해

Personality Chat에는 의도 분류자가 있어 짧은 대화 의도에 대한 응답을 보장합니다. 이러한 분류자는 작업 또는 정보 쿼리 방식의 방해를 받지 않습니다. 고급 채팅 분류자는 쿼리가 짧은 대화인지 잡담 의도인지를 결정합니다. 어휘 및 의미 기반 분류자를 사용하고 해당 점수를 결합하여 결정합니다. 이러한 의도는 대화형 데이터(긍정 의도 샘플) 및 검색/작업 쿼리(부정 의도 샘플)를 사용하여 학습됩니다.

다른 하위 의도 분류자는 짧은 대화의 하위 클래스를 식별하여 서비스가 응답하는 짧은 대화의 유형을 제한합니다(예: 인사, 칭찬, 의견 등). 이러한 심층 학습 분류자는 CDSSM(나선형 심층 구조 의미 체계 모델)을 사용하여 모든 쿼리를 벡터로 변환합니다. 하위 의도에 대한 수만 개의 조정된 쿼리를 사용하여 학습됩니다. 그런 다음, 분류자는 벡터 공간에서 가장 유사한 일치를 찾아 쿼리를 기존의 식별된 의도 클래스와 일치시킵니다.

적절하지 않은 응답을 방지하고 Zo 같은 지능형 에이전트에서 기술 자료를 구축하는 데 도움을 주기 위해 다양한 컨트롤이 추가되었습니다. 기본적으로 Project Personality Chat은 인식된 사용자 의도에만 대응하도록 설정됩니다. Project Personality Chat이 사용자 환경에 적절한지 여부를 테스트할 수도 있습니다. 추가 학습이 필요한 부분이 확인되면 언제든지 의견을 보내주세요.
