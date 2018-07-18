---
title: Academic Knowledge API에서 Journal 엔터티 특성 | Microsoft Docs
description: Cognitive Services에서 Academic Knowledge API의 Journal 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e782c57b8ac57028e070c16382c53ec76666e94d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372687"
---
# <a name="journal-entity"></a>Journal 엔터티

<sub> *다음 특성은 Journal 엔터티에 한정됩니다. (Ty = '2') </sub>

Name    |설명                            |type       | 작업
------- | ------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                              |Int64      |같음
DJN     |업무 일지의 정규화된 이름                |문자열     |없음
JN      |업무 일지 표시 이름                   |문자열     |같음
CC      |업무 일지의 총 인용 횟수           |Int32      |없음  
ECC     |업무 일지의 총 예상 인용 횟수 |Int32      |없음