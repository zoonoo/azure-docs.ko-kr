---
title: '빠른 시작: 음성, 의도 및 엔터티 인식, Python - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660581"
---
의도 인식 빠른 시작을 완료하려면 LUIS 미리 보기 포털을 사용하여 LUIS 계정 및 프로젝트를 만들어야 합니다. 이 빠른 시작에서는 LUIS 구독만 필요합니다. 음성 서비스 구독은 필요하지 않습니다.

가장 먼저 해야 할 일은 LUIS 미리 보기 포털을 사용하여 LUIS 계정 및 앱을 만드는 것입니다. 만든 LUIS 앱은 의도, 엔터티 및 예제 발화를 제공하는 홈 자동화에 대해 미리 빌드된 도메인을 사용합니다. 마치면 클라우드에서 Speech SDK를 사용하여 호출할 수 있는 LUIS 엔드포인트를 실행하게 됩니다. 

다음 지침을 따라 LUIS 앱을 만듭니다. 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">빠른 시작: 미리 빌드된 도메인 앱 빌드</a>

완료되면 다음 세 가지 항목이 필요합니다. 

* LUIS 키
* LUIS 지역
* LUIS 앱 ID

[LUIS 미리 보기 포털](https://preview.luis.ai/)에서 이 정보를 찾을 수 있는 위치는 다음과 같습니다.

1. LUIS 미리 보기 포털에서 **관리**를 선택한 다음, **Azure 리소스**를 선택합니다. 이 페이지에서 LUIS 키 및 위치(_지역_이라고도 함)를 찾을 수 있습니다.  

   > [!div class="mx-imgBorder"]
   > ![LUIS 키 및 위치](../../../media/luis/luis-key-region.png)

2. 키와 위치를 가져온 후에는 앱 ID가 필요합니다. **애플리케이션 설정** 선택 - 이 페이지에서 앱 ID를 사용할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![LUIS 앱 ID](../../../media/luis/luis-app-id.png)