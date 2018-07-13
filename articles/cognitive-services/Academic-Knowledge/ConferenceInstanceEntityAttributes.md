---
title: Academic Knowledge API에서 Conference Instance 엔터티 특성 | Microsoft Docs
description: Cognitive Services에서 Academic Knowledge API의 Conference Instance 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372758"
---
# <a name="conference-instance-entity"></a>Conference Instance 엔터티

<sub> *다음 특성은 Conference Instance 엔터티에 한정됩니다. (Ty = '4') </sub>

Name    |설명                            |type       | 작업
------- | ------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                              |Int64      |같음
CIN     |컨퍼런스 인스턴스의 정규화된 이름({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |문자열     |같음
DCN     |컨퍼런스 인스턴스 표시 이름({ConferenceSeriesName} : {ConferenceInstanceYear})       |문자열     |없음
CIL     |컨퍼런스 인스턴스의 위치    |문자열     |Equals,<br/>StartsWith
CISD    |컨퍼런스 인스턴스의 시작 날짜  |Date       |Equals,<br/>IsBetween
CIED    |컨퍼런스 인스턴스의 종료 날짜    |Date       |Equals,<br/>IsBetween
CIARD   |컨퍼런스 인스턴스의 요약 등록 기한  |Date       |Equals,<br/>IsBetween
CISDD   |컨퍼런스 인스턴스의 제출 기한     |Date       |Equals,<br/>IsBetween
CIFVD   |컨퍼런스 인스턴스의 최종 버전 기한  |Date       |Equals,<br/>IsBetween
CINDD   |컨퍼런스 인스턴스의 알림 날짜   |Date       |Equals,<br/>IsBetween
CD.T    |컨퍼런스 인스턴스 이벤트의 제목   |Date       |Equals,<br/>IsBetween
CD.D    |컨퍼런스 인스턴스 이벤트의 날짜    |Date       |Equals,<br/>IsBetween
PCS.CN  |인스턴스의 컨퍼런스 시리즈 이름 |문자열     |같음
PCS.CId |인스턴스의 컨퍼런스 시리즈 ID |Int64    |같음
CC      |컨퍼런스 인스턴스의 총 인용 횟수           |Int32      |없음  
ECC     |컨퍼런스 인스턴스의 총 예상 인용 횟수 |Int32      |없음


## <a name="extended-metadata-attributes"></a>확장 메타데이터 특성 ##

Name    | 설명               
--------|---------------------------    
FN      | 컨퍼런스 인스턴스 전체 이름