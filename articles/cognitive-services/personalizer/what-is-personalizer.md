---
title: Personalizer란?
titleSuffix: Azure Cognitive Services
description: Personalizer는 사용자에게 표시할 최상의 환경을 선택할 수 있는 클라우드 기반 API 서비스로, 사용자의 실시간 동작을 통해 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 6b55ce851bb12e37aed37039889aa8e69223a286
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467196"
---
# <a name="what-is-personalizer"></a>Personalizer란?

Azure Personalizer는 사용자에게 표시할 최상의 환경을 선택할 수 있는 클라우드 기반 API 서비스로, 공통 실시간 동작을 통해 학습합니다.

* 사용자 및 콘텐츠에 대한 정보를 제공하고 사용자에게 표시할 상위 작업을 받으세요. 
* Personalizer를 사용하기 전에 데이터를 지우고 레이블을 지정할 필요가 없습니다.
* 불편하지 않다면 Personalizer에 대한 피드백을 보내주세요. 
* 실시간 분석 보기 

[Personalizer의 작동 원리](https://personalizercontentdemo.azurewebsites.net/) 데모를 참조하세요.

## <a name="how-does-personalizer-work"></a>Personalizer의 작동 원리

Personalizer는 기계 학습 모델을 사용하여 컨텍스트에서 가장 높은 순위를 지정할 작업을 검색합니다. 클라이언트 애플리케이션은 가능한 작업 목록 및 해당 정보와 컨텍스트 정보를 제공하며, 여기에는 사용자, 디바이스 등에 대한 정보가 포함될 수 있습니다. Personalizer는 수행할 작업을 결정합니다. 클라이언트 애플리케이션은 선택된 작업을 사용할 때 Personalizer에 보상 점수의 형태로 피드백을 제공합니다. 피드백을 받은 후 Personalizer는 향후 순위에 사용되는 자체 모델을 자동으로 업데이트합니다. 시간이 지남에 따라 Personalizer는 기능을 기준으로 각 컨텍스트에서 선택할 최적의 작업을 제안할 수 있는 모델을 학습합니다.

## <a name="how-do-i-use-the-personalizer"></a>Personalizer를 사용하려면 어떻게 할까요?

![Personalizer를 사용하여 사용자에게 표시할 비디오 선택](media/what-is-personalizer/personalizer-example-highlevel.png)

1. 앱에서 개인 설정할 환경을 선택합니다.
1. Azure Portal에서 개인 설정 서비스의 인스턴스를 만들고 구성합니다. 각 인스턴스는 Personalizer 루프입니다.
1. [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)를 사용하여 사용자에 대한 정보(_기능_)와 콘텐츠(_작업_)를 통해 Personalizer를 호출합니다. Personalizer를 사용하기 전에 깨끗하고 레이블이 지정된 데이터를 제공할 필요가 없습니다. API는 직접 호출하거나 다른 프로그래밍 언어에 사용할 수 있는 SDK를 사용하여 호출할 수 있습니다.
1. 클라이언트 애플리케이션에서, Personalizer가 선택한 작업을 사용자에게 표시합니다.
1. [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)를 사용하여 사용자가 Personalizer의 작업을 선택했는지 여부를 나타내는 피드백을 Personalizer에 제공합니다. 이는 _[보상 점수](concept-rewards.md)_ 입니다.
1. Azure Portal에서 분석 결과를 살펴보면서 시스템이 어떻게 작동하고 데이터가 어떻게 개인 설정을 도와주는지 평가합니다.

## <a name="where-can-i-use-personalizer"></a>Personalizer는 어디서 사용할 수 있나요?

예를 들어 클라이언트 애플리케이션은 Personalizer를 추가하여 다음 작업을 수행할 수 있습니다.

* 뉴스 웹 사이트에서 강조 표시되는 문서를 개인 설정합니다.    
* 웹 사이트의 광고 위치를 최적화합니다.
* 쇼핑 웹 사이트에 맞춤형 "추천 항목"을 표시합니다.
* 특정 사진에 적용할 필터와 같은 사용자 인터페이스 요소를 추천합니다.
* 사용자 의도를 구체화하거나 작업을 추천하기 위한 채팅 봇의 응답을 선택합니다.
* 사용자가 비즈니스 프로세스의 다음 단계로 수행해야 하는 제안 사항의 우선 순위를 지정합니다.

Personalizer는 사용자 프로필 정보를 지속 및 관리하거나 개별 사용자의 기본 설정 또는 기록을 로깅하는 서비스가 아닙니다. Personalizer는 유사한 기능이 발생할 때 최대 보상을 얻을 수 있는 단일 모델의 컨텍스트 작업에서 각 상호 작용의 기능을 학습합니다. 

## <a name="personalization-for-developers"></a>개발자를 위한 개인 설정

Personalizer 서비스는 다음과 같은 두 가지 API를 제공합니다.

* 사용자에 대한 정보(_기능_) 및 개인 설정할 콘텐츠(_작업_)를 보냅니다. Personalizer는 최상위 작업으로 응답합니다.
* 순위가 [보상 점수](concept-rewards.md)로 얼마나 잘 작동하는 지에 대한 피드백을 Personalizer에 보냅니다. 

![개인 설정에 대한 이벤트의 기본 시퀀스](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>다음 단계

* [Personalizer의 새로운 기능](whats-new.md)
* [Personalizer의 작동 원리](how-personalizer-works.md)
* [보충 학습이란?](concepts-reinforcement-learning.md)
* [Rank 요청에 대한 기능 및 작업에 대해 알아보기](concepts-features.md)
* [Reward 요청 점수를 확인하는 방법 알아보기](concept-rewards.md)
* [대화형 데모 사용](https://personalizationdemo.azurewebsites.net/)
