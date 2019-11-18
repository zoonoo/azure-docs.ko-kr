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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123071"
---
# <a name="paper-entity"></a>Paper 엔터티

> [!NOTE]
> 아래 특성은 paper 엔터티에만 적용 됩니다. (Ty = ' 0 ')

이름 | 설명 | 형식 | 작업
--- | --- | --- | ---
AA.AfId | 작성자 소속 조직 ID | Int64 | 같음
AA.AfN | 작성자 소속 조직 이름 | 문자열 | Equals, StartsWith
AA.AuId | 작성자 ID | Int64 | 같음
AA.AuN | 정규화 된 만든이 이름 | 문자열 | Equals, StartsWith
AA. DAuN | 원래 작성자 이름 | 문자열 | 없음
AA. DAfN | 원래 소속 이름 | 문자열 | 없음
AA.S | 작성자 목록의 숫자 위치 | Int32 | 같음
BT | BibTex 문서 형식 (' a ': 저널 문서, ' b ': 책, ' c ': 책 장, ' p ': 컨퍼런스 용지) | 문자열 | 없음
BV | BibTex 장소 이름 | 문자열 | 없음
C.CId | 컨퍼런스 시리즈 ID | Int64 | 같음
C.CN | 컨퍼런스 시리즈 이름 | 문자열 | Equals, StartsWith
CC | 인용 횟수 | Int32 | 없음  
CitCon | 인용 컨텍스트</br></br>참조 된 용지 ID와 용지에 있는 해당 컨텍스트 목록 (예: [{123: ["갈색 foxes는 용지 123에서 참조 되는 것으로 인식 됨)", "lazy는 용지 123"]}에 표시 된 기록 점에서 명칭 | 사용자 지정 | 없음
D | YYYY-MM-DD 형식으로 게시 된 날짜 | Date | Equals, IsBetween
DN | 원래 용지 제목 | 문자열 | 없음
DOI | 디지털 개체 식별자 | 문자열 | Equals, StartsWith
E | 확장 메타 데이터</br></br>**중요**:이 특성은 사용 되지 않으며 레거시 응용 프로그램에만 지원 됩니다. 이 특성을 개별적으로 요청 (즉, 특성 = Id, Ti, E) 하면 모든 확장 메타 데이터 특성이 *직렬화 된 JSON 문자열* 에서 반환 됩니다.</br></br>이제 확장 된 메타 데이터에 포함 된 모든 특성을 최상위 특성으로 사용할 수 있으며, 이러한 특성 (예: attributes = Id, Ti, DOI, IA)으로 요청할 수 있습니다. | [확장이](#extended) | 없음
ECC | 예상 인용 수 | Int32 | 없음
DFN | 연구 이름의 원래 필드 | 문자열 | 없음
F.FId | 연구 분야 ID | Int64 | 같음
F.FN | 연구 이름의 정규화 된 필드 | 문자열 | Equals, StartsWith
FP | 게시의 첫 번째 용지 페이지 | 문자열 | 같음
I | 게시 문제 | 문자열 | 같음
IA | 반전 추상 | [InvertedAbstract](#invertedabstract) | 없음
id | 용지 ID | Int64 | 같음
J.JId | 저널 ID | Int64 | 같음
J.JN | 저널 이름 | 문자열 | Equals, StartsWith
LP | 게시의 마지막 용지 페이지 | 문자열 | 같음
PB | 게시자 | 문자열 | 없음
P t | 게시 유형 (0: 알 수 없음, 1: 저널 문서, 2: 특허권, 3: 회의 용지, 4: 책 장, 5: 책, 6: 서적 참조 항목, 7: 데이터 집합, 8: 리포지토리) | 문자열 | 같음
RId | 참조 된 용지 Id 목록 | Int64[] | 같음
S | 관련성을 기준으로 정렬 된 용지의 원본 Url 목록 | [원본](#source)[] | 없음
Ti | 정규화 된 제목 | 문자열 | Equals, StartsWith
V | 게시 볼륨 | 문자열 | 같음
VFN | 저널 또는 회의 장소 전체 이름 | 문자열 | 없음
VSN | 저널 또는 회의 장소에 대 한 짧은 이름 | 문자열 | 없음
W | 제목의 고유한 단어 | 문자열[] | 같음
Y | 게시 된 연도 | Int32 | Equals, IsBetween

## <a name="extended"></a>확장이
> [!IMPORTANT]
> 이 특성은 더 이상 사용 되지 않으며 레거시 응용 프로그램에 대해서만 지원 됩니다. 이 특성을 개별적으로 요청 (즉, 특성 = Id, Ti, E) 하면 모든 확장 메타 데이터 특성이 *직렬화 된 JSON 문자열* 에서 반환 됩니다.</br></br>이제 확장 된 메타 데이터에 포함 된 모든 특성을 최상위 특성으로 사용할 수 있으며, 이러한 특성 (예: attributes = Id, Ti, DOI, IA)으로 요청할 수 있습니다.

> [!IMPORTANT]
> "E"를 사용 하 여 개별 확장 특성 요청을 지원 합니다. 범위 (예: "E. DN")는 더 이상 사용 되지 않습니다. 이는 여전히 기술적으로 지원 되지만 "E"를 사용 하 여 개별 확장 특성을 요청 합니다. 범위는 JSON 응답의 두 위치 ("E" 개체의 일부로, 최상위 특성)로 특성 값을 반환 합니다.

이름 | 설명 | 형식 | 작업
--- | --- | --- | ---
BT | BibTex 문서 형식 (' a ': 저널 문서, ' b ': 책, ' c ': 책 장, ' p ': 컨퍼런스 용지) | 문자열 | 없음
BV | BibTex 장소 이름 | 문자열 | 없음
CC | 인용 컨텍스트</br></br>참조 된 용지 ID와 용지에 있는 해당 컨텍스트 목록 (예: [{123: ["갈색 foxes는 용지 123에서 참조 되는 것으로 인식 됨)", "lazy는 용지 123"]}에 표시 된 기록 점에서 명칭 | 사용자 지정 | 없음
DN | 원래 용지 제목 | 문자열 | 없음
DOI | 디지털 개체 식별자 | 문자열 | 없음
FP | 게시의 첫 번째 용지 페이지 | 문자열 | 없음
I | 게시 문제 | 문자열 | 없음
IA | 반전된 요약 | [InvertedAbstract](#invertedabstract) | 없음
LP | 게시의 마지막 용지 페이지 | 문자열 | 없음
PB | 게시자 | 문자열 | 없음
S | 관련성을 기준으로 정렬 된 용지의 원본 Url 목록 | [원본](#source)[] | 없음
V | 게시 볼륨 | 문자열 | 없음
VFN | 저널 또는 회의 장소 전체 이름 | 문자열 | 없음
VSN | 저널 또는 회의 장소에 대 한 짧은 이름 | 문자열 | 없음

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> InvertedAbstract 특성은 반환 특성으로 직접 요청할 수 없습니다. 개별 특성이 필요한 경우에는 최상위 "IA" 특성을 요청 해야 합니다. 즉, IA를 가져옵니다. IndexLength 요청 특성 = IA

이름 | 설명 | 형식 | 작업
--- | --- | --- | ---
IndexLength | 인덱스의 항목 수(요약의 단어 수) | Int32 | 없음
InvertedIndex | 원래 추상 단어 및 해당 위치 (예: [{"the": [0, 15, 30]}, {"밤색": [1]}, {"fox": [2]}])의 목록입니다. | 사용자 지정 | 없음

## <a name="source"></a>원본

> [!IMPORTANT]
> 원본 특성은 반환 특성으로 직접 요청할 수 없습니다. 개별 특성을 필요로 하는 경우 최상위 "S" 특성을 요청 해야 합니다. 즉,. U 요청 특성 = S를 가져와야 합니다.

이름 | 설명 | 형식 | 작업
--- | --- | --- | ---
Ty | 원본 URL 유형 (1: HTML, 2: Text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | 문자열 | 없음
U | 원본 URL | 문자열 | 없음
