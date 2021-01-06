---
title: 서비스 제한
titleSuffix: Azure Machine Learning
description: 용량 계획에 사용 되는 서비스 제한 및 Azure Machine Learning에 대 한 요청 및 응답의 최대 제한입니다.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930806"
---
# <a name="service-limits-in-azure-machine-learning"></a>Azure Machine Learning의 서비스 제한

이 섹션에는 Azure Machine Learning의 기본 할당량 및 제한 임계값이 나열 됩니다. 리소스 할당량을 늘리는 방법에 [대 한 자세한 내용은 "리소스에 대 한 할당량 관리 및 증가"](how-to-manage-quotas.md) 를 참조 하십시오.

## <a name="workspaces"></a>작업 영역
| 제한 | 값 |
| --- | --- |
| 작업 영역 이름 | 2-32 문자 |

## <a name="runs"></a>실행
| 제한 | 값 |
| --- | --- |
| 작업 영역 당 실행 | 1천만 |
| RunId/ParentRunId | 256자 |
| DataContainerId | 261 문자 |
| DisplayName |256자|
| Description |5000 문자|
| 속성 수 |50 |
| 속성 키의 길이 |100 문자 |
| 속성 값의 길이 |1000 문자 |
| 태그 수 |50 |
| 태그 키의 길이 |100 |
| 태그 값의 길이 |1000 문자 |
| CancelUri/CompleteUri/DiagnosticsUri |1000 문자 |
| 오류 메시지 길이 |3000 문자 |
| 경고 메시지 길이 |300 문자 |
| 입력 데이터 집합 수 |200 |
| 출력 데이터 집합 수 |20 |


## <a name="metrics"></a>메트릭
| 제한 | 값 |
| --- | --- |
| 실행 당 메트릭 이름 |50|
| 메트릭 별 메트릭 행 이름 |1천만|
| 메트릭 행 당 열 수 |15|
| 메트릭 열 이름 길이 |255자 |
| 메트릭 열 값 길이 |255자 |
| 일괄 처리당 업로드 된 메트릭 행 수 | 250 |

> [!NOTE]
> 메트릭 이름으로 변수를 서식 지정 하기 때문에 실행 당 메트릭 이름 제한에 도달 하는 경우에는 대신 한 열이 변수 값이 고 두 번째 열이 메트릭 값인 행 메트릭을 사용 하는 것이 좋습니다.

## <a name="artifacts"></a>Artifacts

| 제한 | 값 |
| --- | --- |
| 실행 당 아티팩트 수 |1천만|
| 아티팩트 경로의 최대 길이 |5000 문자 |

## <a name="limit-increases"></a>제한 늘리기
[지원에 문의](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)하 여 개별 작업 영역에 대 한 일부 제한을 늘릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Machine Learning 환경 구성](how-to-configure-environment.md)
- ["리소스에 대 한 할당량 관리 및 증가"](how-to-manage-quotas.md)에서 리소스 할당량을 늘리는 방법에 대해 알아봅니다.

