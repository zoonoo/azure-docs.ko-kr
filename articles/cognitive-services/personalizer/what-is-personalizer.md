---
title: Personalizer란?
titleSuffix: Azure Cognitive Services
description: Azure Personalizer는 사용자에게 표시할 최상의 환경을 선택할 수 있는 클라우드 기반 API 서비스로, 사용자의 실시간 동작을 통해 학습합니다.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c969029bcc0412267507efe81549ec6f8b2988ce
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026657"
---
# <a name="what-is-personalizer"></a>Personalizer란?

Azure Personalizer는 사용자에게 표시할 최상의 환경을 선택할 수 있는 클라우드 기반 API 서비스로, 사용자의 실시간 동작을 통해 학습합니다.

* 사용자 및 콘텐츠에 대한 정보를 제공하고 사용자에게 표시할 상위 작업을 받으세요. 
* Personalizer를 사용하기 전에 데이터를 지우고 레이블을 지정할 필요가 없습니다.
* 불편하지 않다면 Personalizer에 대한 피드백을 보내주세요. 
* 실시간 분석 보기 
* Personalizer를 보다 큰 데이터 과학 작업의 일환으로 사용하여 기존 실험의 유효성을 검사합니다.

## <a name="how-does-personalizer-work"></a>Personalizer의 작동 원리

Personalizer는 기계 학습 모델을 사용하여 컨텍스트에서 가장 높은 순위를 지정할 작업을 검색합니다. 클라이언트 애플리케이션은 가능한 작업 목록 및 해당 정보와 컨텍스트 정보를 제공하며, 여기에는 사용자, 디바이스 등에 대한 정보가 포함될 수 있습니다. Personalizer는 수행할 작업을 결정합니다. 클라이언트 애플리케이션은 선택된 작업을 사용할 때 Personalizer에 보상 점수의 형태로 피드백을 제공합니다. 피드백 루프가 완료되면 Personalizer는 향후 순위에 사용되는 자체 모델을 자동으로 업데이트합니다.

## <a name="how-do-i-use-the-personalizer"></a>Personalizer를 사용하려면 어떻게 할까요?

![Personalizer를 사용하여 사용자에게 표시할 비디오 선택](media/what-is-personalizer/personalizer-example-highlevel.png)

1. 앱에서 개인 설정할 환경을 선택합니다.
1. Azure Portal에서 개인 설정 서비스를 만들고 구성합니다.
1. SDK를 사용하여 사용자에 대한 정보(_기능_)와 콘텐츠(_작업_)를 통해 Personalizer를 호출합니다. Personalizer를 사용하기 전에 깨끗하고 레이블이 지정된 데이터를 제공할 필요가 없습니다. 
1. 클라이언트 애플리케이션에서, Personalizer가 선택한 작업을 사용자에게 표시합니다.
1. SDK를 사용하여 사용자가 Personalizer의 작업을 선택했는지 여부를 나타내는 피드백을 Personalizer에 제공합니다. 이것이 _보상 점수_이며, 일반적으로 -1부터 1 사이입니다.
1. Azure Portal에서 분석 결과를 살펴보면서 시스템이 어떻게 작동하고 데이터가 어떻게 개인 설정을 도와주는지 평가합니다.

## <a name="where-can-i-use-personalizer"></a>Personalizer는 어디서 사용할 수 있나요?

예를 들어 클라이언트 애플리케이션은 Personalizer를 추가하여 다음 작업을 수행할 수 있습니다.

* 뉴스 웹 사이트에서 강조 표시되는 문서를 개인 설정합니다.    
* 웹 사이트의 광고 위치를 최적화합니다.
* 쇼핑 웹 사이트에 맞춤형 "추천 항목"을 표시합니다.
* 특정 사진에 적용할 필터와 같은 사용자 인터페이스 요소를 추천합니다.
* 사용자 의도를 구체화하거나 작업을 추천하기 위한 채팅 봇의 응답을 선택합니다.
* 사용자가 비즈니스 프로세스의 다음 단계로 수행해야 하는 제안 사항의 우선 순위를 지정합니다.

## <a name="personalization-for-developers"></a>개발자를 위한 개인 설정

Personalizer 서비스는 다음과 같은 두 가지 API를 제공합니다.

* 사용자에 대한 정보(_기능_) 및 개인 설정할 콘텐츠(_작업_)를 보냅니다. Personalizer는 최상위 작업으로 응답합니다.
* 순서 지정이 얼마나 잘 작동하는지 일반적으로 0부터 1 사이의 숫자로 Personalizer에 피드백을 보냅니다(이전 섹션에서는 -1부터 1 사이). 

![개인 설정에 대한 이벤트의 기본 시퀀스](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>다음 단계

[빠른 시작: C#으로 피드백 루프 만들기](csharp-quickstart-commandline-feedback-loop.md)
