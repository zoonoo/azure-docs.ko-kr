---
title: Paer 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Paper 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: ae238a3c3af091b764147fcde8aa2a98986da92e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158893"
---
# <a name="paper-entity"></a>Paper 엔터티

<sub> *아래 특성은 Paper 엔터티에 한정됩니다. (Ty = '0') </sub>


이름    |설명                                        |type       | 작업
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                                          |Int64      |같음
Ti      |논문 제목                                        |문자열     |Equals,<br/>StartsWith
L       |“\@@@”으로 구분된 논문 언어 코드            |문자열     |같음
Y       |논문 연도                                         |Int32      |Equals,<br/>IsBetween
D       |논문 날짜                                         |Date       |Equals,<br/>IsBetween
CC      |인용 횟수                                     |Int32      |없음  
ECC     |예상 인용 횟수                           |Int32      |없음
AA.AuN  |작성자 이름                                        |문자열     |Equals,<br/>StartsWith
AA.AuId |작성자 ID                                          |Int64      |같음
AA.AfN  |작성자 소속 조직 이름                            |문자열     |Equals,<br/>StartsWith
AA.AfId |작성자 소속 조직 ID                              |Int64      |같음
AA.S    |논문의 작성자 순서                         |Int32      |같음
F.FN    |연구 분야 이름                                |문자열     |Equals,<br/>StartsWith
F.FId   |연구 분야 ID                                  |Int64      |같음
J.JN    |저널 이름                                       |문자열     |Equals,<br/>StartsWith
J.JId   |저널 ID                                         |Int64      |같음
C.CN    |컨퍼런스 시리즈 이름                             |문자열     |Equals,<br/>StartsWith
C.CId   |컨퍼런스 시리즈 ID                               |Int64      |같음
RId     |참조된 논문 ID                              |Int64[]    |같음
W       |논문 제목 및 요약의 단어                |문자열[]   |같음
E       |확장된 메타데이터(아래 표 참조)                |문자열     |없음  
        


## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

이름    | 설명               
--------|---------------------------    
DN      | 논문의 표시 이름 
S       | 원본 - 논문의 웹 소스 목록, 고정 순위별로 정렬
S.Ty    | 원본 유형 (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | 원본 URL
VFN     | 장소 전체 이름 - 저널 또는 컨퍼런스의 전체 이름
VSN     | 장소 약식 이름 - 저널 또는 컨퍼런스의 약식 이름
V       | 볼륨 - 저널 볼륨
BV      | 저널 이름
BT      | 
PB      | 저널 약어
I       | 쟁점 - 저널 쟁점
FP      | FirstPage - 논문의 첫 번째 페이지
LP      | LastPage - 논문의 마지막 페이지
DOI     | 디지털 개체 식별자
CC      | 인용 컨텍스트 – 참조된 논문 ID 및 논문에서 해당하는 컨텍스트의 목록(예: [{123:[“brown foxes are known for jumping as referenced in paper 123”, “the lazy dogs are a historical misnomer as shown in paper 123”]})
IA      | 반전된 요약
IA.IndexLength| 인덱스의 항목 수(요약의 단어 수)
IA.InvertedIndex| 요약 단어 및 원래 요약에서 해당하는 위치 목록(예: [{“the”:[0, 15, 30]}, {“brown”:[1]}, {“fox”:[2]}])
