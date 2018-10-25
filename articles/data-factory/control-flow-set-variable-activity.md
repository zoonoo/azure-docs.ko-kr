---
title: Azure Data Factory의 변수 설정 작업 | Microsoft Docs
description: 변수 설정 작업을 사용하여 Data Factory 파이프라인에 정의된 기존 변수의 값을 설정하는 방법 알아보기
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: ff9bfce1f9262d78ba17abdd88c481d5057d5f38
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076422"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory의 변수 설정 작업

Data Factory 파이프라인에서 정의된 String, Bool 또는 Array 형식의 기존 변수 값을 설정하려면 변수 설정 작업을 사용합니다.

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 필수
-------- | ----------- | --------
이름 | 파이프라인의 작업 이름입니다. | yes
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다. | no
형식 | 작업 형식은 SetVariable입니다. | 예
값 | 지정된 변수를 설정하는 데 사용하는 문자열 리터럴 또는 식 개체 값입니다. | 예
variableName | 이 작업에 의해 설정되는 변수의 이름입니다. | 예


## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 추가 작업](control-flow-append-variable-activity.md)
