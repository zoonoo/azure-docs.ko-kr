---
title: Azure Data Factory Mapping Data Flow 서로게이트 키 변환
description: Azure Data Factory Mapping Data Flow 서로게이트 키 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734951"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory Mapping Data Flow 서로게이트 키 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

서로게이트 키 변환을 사용하여 증가하는 비업무용 임의 키 값을 데이터 흐름 행 집합에 추가합니다. 이는 차원 테이블의 각 구성원이 Kimball DW 방법론의 일부인 비업무용 키인 고유 키를 가져야 하는 별모양 스키마 분석 데이터 모델에서 차원 테이블을 디자인할 때 유용합니다.

![서로게이트 키 변환](media/data-flow/surrogate.png "서로게이트 키 변환")

"키 열"은 새로운 서로게이트 키 열에 제공할 이름입니다.

"시작 값"은 증분 값의 시작점입니다.
