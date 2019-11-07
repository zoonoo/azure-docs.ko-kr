---
title: Azure Data Factory의 변수 추가 작업
description: Data Factory 파이프라인에 정의된 기존 배열 변수에 값을 추가하기 위해 변수 추가 작업을 설정하는 방법 알아보기
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679985"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory의 변수 추가 작업

변수 추가 작업을 사용하여 Data Factory 파이프라인에 정의된 기존 배열 변수에 값을 추가할 수 있습니다.

## <a name="type-properties"></a>형식 속성

속성 | 설명 | 필수
-------- | ----------- | --------
name | 파이프라인의 작업 이름입니다. | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다. | no
type | 활동 유형은 AppendVariable입니다. | 예
값 | 지정된 변수에 추가하는 데 사용되는 문자열 리터럴 또는 표현식 개체 값 | 예
variableName | 활동별로 수정될 변수의 이름, 변수는 ‘Array’ 유형이어야 합니다. | 예

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 설정 작업](control-flow-set-variable-activity.md)
