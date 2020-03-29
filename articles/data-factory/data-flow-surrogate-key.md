---
title: 데이터 흐름 대리 키 변환 매핑
description: Azure Data Factory의 매핑 데이터 흐름 대리 키 변환을 사용하여 순차키 값을 생성하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930211"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>데이터 흐름 대리 키 변환 매핑



서로게이트 키 변환을 사용하여 증가하는 비업무용 임의 키 값을 데이터 흐름 행 집합에 추가합니다. 이는 차원 테이블의 각 구성원이 Kimball DW 방법론의 일부인 비업무용 키인 고유 키를 가져야 하는 별모양 스키마 분석 데이터 모델에서 차원 테이블을 디자인할 때 유용합니다.

![서로게이트 키 변환](media/data-flow/surrogate.png "대리 키 변환")

"키 열"은 새로운 서로게이트 키 열에 제공할 이름입니다.

"시작 값"은 증분 값의 시작점입니다.

## <a name="increment-keys-from-existing-sources"></a>기존 소스의 키 증분

소스에 있는 값에서 시퀀스를 시작하려는 경우 서로게이트 키 변환 바로 다음에 파생 된 열 변환을 사용하고 두 값을 함께 추가할 수 있습니다.

![SK, 최대 추가](media/data-flow/sk006.png "서로게이트 키 변환 최대 추가")

이전 max를 사용하여 키 값을 시드하려면 다음 두 가지 기술을 사용할 수 있습니다.

### <a name="database-sources"></a>데이터베이스 원본

소스 변환을 사용하여 소스에서 MAX()를 선택하려면 "쿼리" 옵션을 사용합니다.

![대리 키 쿼리](media/data-flow/sk002.png "대리 키 변환 쿼리")

### <a name="file-sources"></a>파일 소스

이전 최대 값이 파일에 있는 경우 집계 변환과 함께 소스 변환을 사용하고 MAX() 식 함수를 사용하여 이전 최대 값을 가져올 수 있습니다.

![서로게이트 키 파일](media/data-flow/sk008.png "서로게이트 키 파일")

두 경우 모두 이전 최대 값을 포함하는 원본과 함께 들어오는 새 데이터에 참여해야 합니다.

![서로게이트 키 조인](media/data-flow/sk004.png "서로게이트 키 조인")

## <a name="next-steps"></a>다음 단계

이러한 예제는 [조인](data-flow-join.md) 및 [파생 열](data-flow-derived-column.md) 변환을 사용합니다.
