---
title: Speaker Recognition API란?
titleSuffix: Azure Cognitive Services
description: Cognitive Services의 Speaker Recognition API를 사용하여 화자 검증 및 화자 식별을 수행합니다.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73464909"
---
# <a name="speaker-recognition-api---preview"></a>Speaker Recognition API - 미리 보기

Speaker Recognition API는 화자 검증 및 화자 식별을 위해 고급 AI 알고리즘을 제공하는 클라우드 기반 API입니다. Speaker Recognition은 화자 검증과 화자 식별의 두 가지 범주로 나뉩니다.

## <a name="speaker-verification"></a>화자 검증

음성에는 개인과 연관지을 수 있는 고유한 특징이 있습니다.  콜 센터 및 웹 서비스와 같은 시나리오에서 애플리케이션은 확인을 위한 추가 요소로 음성을 사용할 수 있습니다.

Speaker Verification API는 음성 및 음성 암호를 모두 사용하여 사용자를 확인하는 데 도움이 되는 지능형 도구로 사용됩니다.

### <a name="enrollment"></a>등록

화자 검증에 대한 등록은 텍스트에 따라 다르기 때문에 화자가 등록 및 검증 단계에서 사용할 특정 전달 구를 선택해야 합니다.

화자 등록 단계에서 화자의 음성은 특정 문구를 말하여 녹음됩니다. 선택한 문구가 인식되는 동안 고유한 음성 서명을 구성하기 위해 음성 특징이 추출됩니다. 이 화자 등록 데이터를 함께 사용하여 화자를 확인합니다. 화자 등록 데이터는 보안 시스템에 저장됩니다. 보존되는 기간은 고객이 제어합니다. 고객은 API 호출을 통해 개별 화자에 대한 등록 데이터를 만들고, 업데이트하고, 제거할 수 있습니다.  구독이 삭제되면 구독과 연결된 모든 화자 등록 데이터도 삭제됩니다.

고객은 화자 검증을 위해 사용자로부터 적절한 권한을 받았는지 확인해야 합니다.

### <a name="verification"></a>확인

검증 단계에서 고객은 확인할 개인과 연결된 ID를 사용하여 Speaker Verification API를 호출해야 합니다.  서비스는 입력 음성 녹음에서 음성 특징 및 암호를 추출합니다. 그런 다음 고객이 확인하려는 화자에 대한 화자 등록 데이터의 해당 요소와 특징을 비교하고 일치 여부를 결정합니다.  응답은 다양한 신뢰 수준과 함께 "수락" 또는 "거부"를 반환합니다.  그러면 고객은 이 사용자가 등록된 화자인지 여부를 판단할 수 있도록 결과를 사용하는 방법을 결정합니다.

임계값 신뢰 수준은 사용되는 시나리오 및 기타 확인 요소에 따라 설정되어야 합니다. 신뢰 수준을 시험해 보고 각 애플리케이션에 대해 적절한 설정을 고려하는 것이 좋습니다. 이 API는 오디오가 실제 사용자의 것인지 또는 등록된 화자를 흉내낸 것인지 또는 녹음된 것인지를 결정하기 위한 것이 아닙니다.

서비스는 검증 단계 동안 서비스로 전송되는 음성 녹음 또는 추출된 음성 특징을 보관하지 않습니다.

화자 검증에 대한 자세한 내용은 API [화자 - 검증](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)을 참조하세요.

## <a name="speaker-identification"></a>화자 식별

애플리케이션은 음성을 사용하여 등록된 화자 그룹에 지정된 "화자"를 식별할 수 있습니다. Speaker Identification API는 모임 생산성, 개인 설정 및 콜 센터 전사 등의 시나리오에서 사용할 수 있습니다.

### <a name="enrollment"></a>등록

화자 식별 등록은 텍스트와 관련이 없으므로 화자가 오디오에서 말하는 내용에 대한 제한이 없습니다. 암호는 필요하지 않습니다.

등록 단계에서 화자의 음성이 녹음되고, 고유한 음성 서명을 구성하기 위해 음성 특징이 추출됩니다. 추출된 음성 오디오 및 특징은 보안 시스템에 저장됩니다. 보존되는 기간은 고객이 제어합니다. 고객은 API 호출을 통해 개별 화자에 대한 이 화자 등록 데이터를 만들고, 업데이트하고, 제거할 수 있습니다. 구독이 삭제되면 구독과 연결된 모든 화자 등록 데이터도 삭제됩니다.

고객은 화자 식별을 위해 사용자로부터 적절한 권한을 받았는지 확인해야 합니다.

### <a name="identification"></a>Identification(식별)

식별 단계에서 화자 식별 서비스는 입력 음성 녹음에서 음성 특징을 추출합니다. 그런 다음 지정된 화자 목록의 등록 데이터와 특징을 비교합니다. 등록된 화자와 일치하는 항목이 발견되면 응답은 신뢰 수준과 함께 화자 ID를 반환합니다.  반면 등록된 화자와 일치하는 화자가 없는 경우 응답은 "거부"를 반환합니다.

임계값 신뢰 수준은 시나리오에 따라 설정해야 합니다. 신뢰 수준을 시험해 보고 각 애플리케이션에 대해 적절한 설정을 고려하는 것이 좋습니다. 이 API는 오디오가 실제 사용자의 것인지 또는 등록된 화자를 흉내낸 것인지 또는 녹음된 것인지를 결정하기 위한 것이 아닙니다.

서비스는 식별 단계 동안 서비스로 전송되는 음성 녹음 또는 추출된 음성 특징을 보관하지 않습니다.

화자 식별에 대한 자세한 내용은 API  [화자 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)을 참조하세요.
