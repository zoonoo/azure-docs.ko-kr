---
title: Academic Knowledge API에서 Author 엔터티 특성 | Microsoft Docs
description: Cognitive Services에서 Academic Knowledge API의 Author 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372702"
---
# <a name="author-entity"></a>Author 엔터티
<sub> *다음 특성은 Author 엔터티에 한정됩니다. (Ty = '1') </sub>

Name    |설명                            |type       | 작업
------- | ------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                              |Int64      |같음
AuN     |작성자의 정규화된 이름                 |문자열     |같음
DAuN    |작성자 표시 이름                    |문자열     |없음
CC      |작성자의 총 인용 횟수            |Int32      |없음  
ECC     |작성자의 총 예상 인용 횟수  |Int32      |없음
E       |확장 메타데이터(“확장 메타 특성” 표 참조)  |문자열     |없음  


## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

Name    | 설명               
--------|---------------------------    
LKA.Afn     | 작성자와 관련된 소속 표시 이름  
LKA.AfId        | 작성자와 관련된 소속의 엔터티 ID