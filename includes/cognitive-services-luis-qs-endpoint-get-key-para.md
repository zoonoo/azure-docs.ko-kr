---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 14678a143e1b14b9b0b89435f356ac5df98ef40c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478686"
---
엔드포인트 키에 제공되는 예측 엔드포인트에 액세스합니다. 이 빠른 시작에서는 LUIS 계정과 연결된 무료 시작 키를 사용하세요. 
 
1. LUIS 계정을 사용하여 로그인합니다. 

    [![Language Understanding(LUIS) 앱 목록 스크린샷](media/cognitive-services-luis/app-list.png "Language Understanding(LUIS) 앱 목록 스크린샷")](media/cognitive-services-luis/app-list.png)

2. 오른쪽 맨 위 메뉴에서 이름을 선택한 후 **설정**을 선택합니다.

    ![LUIS 사용자 설정 메뉴 액세스](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. **제작 키** 값을 복사합니다. 이는 빠른 시작의 뒷부분에서 사용할 예정입니다. 

    [![Language Understanding(LUIS) 사용자 설정 스크린샷](media/cognitive-services-luis/get-user-authoring-key.png "Language Understanding(LUIS) 사용자 설정 스크린샷")](media/cognitive-services-luis/get-user-authoring-key.png)

    제작 키는 제작 API에 무료 무제한 요청을 허용하고, 예측 엔드포인트 API에 모든 LUIS 앱당 매월 최대 1000개의 쿼리를 허용합니다. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
