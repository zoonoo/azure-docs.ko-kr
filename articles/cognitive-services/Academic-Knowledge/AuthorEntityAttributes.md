---
title: Author 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Author 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609754"
---
# <a name="author-entity"></a>Author 엔터티
<sub> *다음 특성은 Author 엔터티에 한정됩니다. (Ty = '1') </sub>

Name    |설명                            |Type       | 작업
------- | ------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                              |Int64      |같음
AuN     |작성자의 정규화된 이름                 |문자열     |같음
DAuN    |작성자 표시 이름                    |String     |없음
CC      |작성자의 총 인용 횟수            |Int32      |없음  
ECC     |작성자의 총 예상 인용 횟수  |Int32      |없음
E       |확장 메타데이터(“확장 메타 특성” 표 참조)  |문자열     |없음  


## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

이름    | 설명               
--------|---------------------------    
LKA.Afn     | 작성자와 관련된 소속 표시 이름  
LKA.AfId        | 작성자와 관련된 소속의 엔터티 ID
