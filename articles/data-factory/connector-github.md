---
title: GitHub에 연결
description: GitHub를 사용하여 Common Data Model 엔터티 참조 지정
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222115"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>GitHub를 사용하여 Common Data Model 엔터티 참조 읽기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory의 GitHub 커넥터는 데이터 흐름 매핑에서 [Common Data Model](format-common-data-model.md) 형식에 대한 엔터티 참조 스키마를 수신하는 데만 사용됩니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

GitHub 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **GitHub** 로 설정해야 합니다. | yes
| userName | GitHub 사용자 이름 | yes |
| password | GitHub 암호 | yes |

## <a name="next-steps"></a>다음 단계

매핑 데이터 흐름에서 [원본 데이터 세트](data-flow-source.md)를 만듭니다.