---
title: GitHub에 연결
description: GitHub를 사용 하 여 공통 데이터 모델 엔터티 참조 지정
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771039"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>GitHub를 사용 하 여 Common Data Model 엔터티 참조 읽기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory의 GitHub 커넥터는 데이터 흐름 매핑에서 [공통 데이터 모델](format-common-data-model.md) 형식에 대 한 엔터티 참조 스키마를 수신 하는 데만 사용 됩니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

GitHub 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 **GitHub**로 설정 되어야 합니다. | 예
| userName | GitHub 사용자 이름 | 예 |
| password | GitHub 암호 | 예 |

## <a name="next-steps"></a>다음 단계

매핑 데이터 흐름에서 [원본 데이터 집합](data-flow-source.md) 을 만듭니다.