---
title: Author 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Author 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900692"
---
# <a name="author-entity"></a>Author 엔터티
<sub> *다음 특성은 Author 엔터티에 한정됩니다. (Ty = '1') </sub>

이름    |설명                            |type       | 작업
------- | ------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                              |Int64      |같음
AuN     |작성자의 정규화된 이름                 |문자열     |같음
DAuN    |작성자 표시 이름                    |문자열     |없음
CC      |작성자의 총 인용 횟수            |Int32      |없음  
ECC     |작성자의 총 예상 인용 횟수  |Int32      |없음
E       |확장 메타데이터(“확장 메타 특성” 표 참조)  |문자열     |없음  


## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

이름    | 설명               
--------|---------------------------    
LKA.Afn     | 작성자와 관련된 소속 표시 이름  
LKA.AfId        | 작성자와 관련된 소속의 엔터티 ID