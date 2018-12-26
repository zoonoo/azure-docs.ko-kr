---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019116"
---
1. Open Visual Studio 2017 Community Edition.
1. 새 **콘솔 앱(.Net Core)** 프로젝트를 만들고 프로젝트를 `QnaMakerQuickstart`로 이름 지정합니다. 나머지 설정에 대해 기본값을 수락합니다.
1. 솔루션 탐색기에서 프로젝트 이름 **QnaMakerQuickstart**를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리...** 를 선택합니다.
1. NuGet 창에서 선택 **브라우저**를 선택한 다음, **Newtonsoft.JSON**을 검색하고 패키지를 설치합니다. 이 패키지는 QnaMaker HTTP 응답에서 반환된 JSON을 구문 분석하는 데 사용됩니다. 