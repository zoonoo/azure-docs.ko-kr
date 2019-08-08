---
title: Academic Knowledge API에서 Affiliation 엔터티 특성
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Affiliation 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705106"
---
# <a name="affiliation-entity"></a>Affiliation 엔터티

<sub> *다음 특성은 Affiliation 엔터티에 한정됩니다. (Ty = '5') </sub>

이름    |Description                            |형식       | 작업
------- | ------------------------------------- | --------- | ----------------------------
ID      |엔터티 ID                              |Int64      |같음
AfN     |소속의 정규화된 이름        |문자열     |같음
DAfN    |소속 표시 이름       |String     |없음
CC      |소속의 총 인용 횟수           |Int32      |없음  
ECC     |소속의 총 예상 인용 횟수 |Int32      |없음

## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

이름    | Description               
--------|---------------------------    
PC      |소속의 문서 수
