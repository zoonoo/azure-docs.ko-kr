---
title: Paer 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Paper 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161735"
---
# <a name="paper-entity"></a>Paper 엔터티

<sub>* 아래 특성은 종이 엔터티에만 적용 됩니다. (Ty = ' 0 ')</sub>

name | 설명 | Type | 운영
--- | --- | --- | ---
AA.AfId | 작성자 소속 조직 ID | Int64 | 같음
AA.AfN | 작성자 소속 조직 이름 | string | Equals, StartsWith
AA.AuId | 작성자 ID | Int64 | 같음
AA.AuN | 정규화 된 만든이 이름 | string | Equals, StartsWith
AA. DAuN | 원래 작성자 이름 | string | 없음
AA. DAfN | 원래 소속 이름 | string | 없음
AA.S | 작성자 목록의 숫자 위치 | Int32 | 같음
CC | 인용 횟수 | Int32 | 없음  
C.CId | 컨퍼런스 시리즈 ID | Int64 | 같음
C.CN | 컨퍼런스 시리즈 이름 | string | Equals, StartsWith
D | YYYY-MM-DD 형식으로 게시 된 날짜 | 날짜 | Equals, IsBetween
E | 확장된 메타데이터(아래 표 참조) | string | N/A  
ECC | 예상 인용 수 | Int32 | 없음
DFN | 연구 이름의 원래 필드 | string | 없음
F.FId | 연구 분야 ID | Int64 | 같음
F.FN | 연구 이름의 정규화 된 필드 | string | Equals, StartsWith
Id | 용지 ID | Int64 | 같음
J.JId | 저널 ID | Int64 | 같음
J.JN | 저널 이름 | string | Equals, StartsWith
P t | 게시 유형 (0: 알 수 없음, 1: 저널 문서, 2: 특허권, 3: 회의 용지, 4: 책 장, 5: 책, 6: 서적 참조 항목, 7: 데이터 집합, 8: 리포지토리) | string | 같음
RId | 참조 된 용지 Id 목록 | Int64[] | 같음
Ti | 정규화 된 제목 | string | Equals, StartsWith
W | 제목의 고유한 단어 | 문자열[] | 같음
Y | 게시 된 연도 | Int32 | Equals, IsBetween

## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

name | 설명               
--- | ---
BT | BibTex 문서 형식 (' a ': 저널 문서, ' b ': 책, ' c ': 책 장, ' p ': 컨퍼런스 용지)
BV | BibTex 장소 이름
CC | 인용 컨텍스트 – 참조 된 용지 ID의 목록 및 용지에서 해당 하는 컨텍스트 목록 (예: [{123: ["갈색 foxes는 용지 123에서 참조 되는 것으로 인식 됨)", "lazy는 용지 123"]}에 표시 된 기록 점에서 명칭)
DN | 원래 용지 제목
DOI | 디지털 개체 식별자
FP | 게시의 첫 번째 용지 페이지
I | 게시 문제
IA | 반전된 요약
IA.IndexLength | 인덱스의 항목 수(요약의 단어 수)
IA.InvertedIndex | 원래 추상 단어 및 해당 위치 (예: [{"the": [0, 15, 30]}, {"밤색": [1]}, {"fox": [2]}])의 목록입니다.
LP | 게시의 마지막 용지 페이지
PB | 게시자
S | 원본 - 논문의 웹 소스 목록, 고정 순위별로 정렬
S.Ty | 원본 유형 (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U | 원본 URL
V | 게시 볼륨
VFN | 저널 또는 회의 장소 전체 이름
VSN | 저널 또는 회의 장소에 대 한 짧은 이름
