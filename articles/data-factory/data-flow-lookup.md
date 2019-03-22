---
title: Azure Data Factory Mapping Data Flow 조회 변환
description: Azure Data Factory Mapping Data Flow 조회 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738388"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory Mapping Data Flow 조회 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

조회를 사용하여 다른 원본의 참조 데이터를 Data Flow에 추가합니다. 조회 변환에는 참조 테이블을 가리키고 키 필드가 일치하는 정의된 원본이 필요합니다.

![조회 변환](media/data-flow/lookup1.png "조회")

수신 스트림 필드와 참조 원본 필드 간의 일치에 사용할 키 필드를 선택합니다. Data Flow 디자인 캔버스에서 조회의 오른쪽 항목으로 사용할 새 원본을 먼저 만들어야 합니다.

일치 항목이 발견되면 참조 원본의 결과 행과 열이 데이터 흐름에 추가됩니다. Data Flow의 끝에서 싱크에 포함할 관심 필드를 선택할 수 있습니다.
