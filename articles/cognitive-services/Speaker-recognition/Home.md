---
title: Speaker Recognition API란?
titleSuffix: Azure Cognitive Services
description: Cognitive Services의 Speaker Recognition API를 사용하여 화자 검증 및 화자 식별에 고급 알고리즘을 사용합니다.
services: cognitive-services
author: dwlin
manager: cgronlun
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: dwlin
ms.openlocfilehash: c9193a51c677b327e7d799412e389467ac5cc1c0
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016860"
---
# <a name="speaker-recognition-api"></a>Speaker Recognition API

Azure Cognitive Services Speaker Recognition API에 오신 것을 환영합니다. Speaker Recognition API는 화자 검증 및 화자 식별을 위해 가장 발전된 알고리즘을 제공하는 클라우드 기반 API입니다. Speaker Recognition은 화자 검증과 화자 식별의 두 가지 범주로 나눌 수 있습니다.


## <a name="speaker-verification"></a>화자 검증

음성에는 지문처럼 사람을 식별하는 데 사용할 수 있는 고유한 특성이 있습니다.  액세스 제어 및 인증 시나리오에 대한 신호로 음성을 사용하는 것은 기본적으로 고객의 인증 환경을 간소화하는 높은 수준의 보안을 제공하는 새로운 혁신적인 도구로 등장했습니다.

화자 검증 API는 해당 음성 또는 발화를 사용하여 사용자를 자동으로 검증하고 인증할 수 있습니다.

### <a name="enrollment"></a>등록

화자 검증에 대한 등록은 텍스트에 따라 다르기 때문에 화자가 등록 및 검증 단계에서 사용할 특정 전달 구를 선택해야 합니다.

등록 단계에서 화자의 음성은 특정 구를 말하도록 기록된 후에 다양한 기능이 추출되고 선택한 구가 인식됩니다. 추출된 기능과 선택한 구가 함께 고유한 음성 시그니처를 구성합니다.

### <a name="verification"></a>확인

검증에서는 입력된 음성 및 구를 등록된 음성 서명 및 구와 비교하여 동일한 사람인지 여부와 올바른 구를 말하고 있는지를 확인합니다.

화자 검증에 대한 자세한 내용은 API [화자 - 검증](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)을 참조하세요.

## <a name="speaker-identification"></a>화자 식별

화자 식별 API는 잠재적인 화자 그룹이 지정된 오디오 파일에서 말하는 사람을 자동으로 식별할 수 있습니다. 입력 오디오는 제공된 화자 그룹에 연결되고 일치하는 항목이 있을 경우 화자의 신원이 반환됩니다.

모든 화자는 먼저 등록 프로세스를 통해 시스템에 등록된 음성 가져오고 음성 인쇄를 생성해야 합니다.


### <a name="enrollment"></a>등록

화자 식별 등록은 텍스트와 관련이 없으므로 화자가 오디오에서 말하는 내용에 대한 제한이 없습니다. 화자의 음성이 녹음되고, 고유한 음성 서명을 구성하기 위해 다양한 특징이 추출됩니다.


### <a name="recognition"></a>인식

잠재적인 그룹의 화자와 함께 알 수 없는 화자의 오디오는 인식 단계에서 제공됩니다. 누구의 음성인지를 결정하기 위해 모든 화자와 입력 음성을 비교하고 일치를 찾은 경우 화자의 신원을 반환합니다.

화자 식별에 대한 자세한 내용은 API [화자 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)을 참조하세요.
