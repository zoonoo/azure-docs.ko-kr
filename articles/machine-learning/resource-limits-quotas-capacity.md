---
title: 서비스 제한
titleSuffix: Azure Machine Learning
description: 용량 계획에 사용되는 서비스 제한 및 Azure Machine Learning에 대한 요청 및 응답의 최대 제한입니다.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97930806"
---
# <a name="service-limits-in-azure-machine-learning"></a>Azure Machine Learning의 서비스 한도

이 섹션에는 Azure Machine Learning의 기본 할당량 및 제한 임계값이 나열됩니다. 리소스 할당량을 늘리는 방법에 대해 알아보려면 ["리소스 할당량 관리 및 늘리기"](how-to-manage-quotas.md)를 참조하세요.

## <a name="workspaces"></a>작업 영역
| 제한 | 값 |
| --- | --- |
| 작업 영역 이름 | 2-32자 |

## <a name="runs"></a>실행
| 제한 | 값 |
| --- | --- |
| 작업 영역당 실행 | 1천만 |
| RunId/ParentRunId | 256자 |
| DataContainerId | 261자 |
| DisplayName |256자|
| Description |5,000자|
| 속성 수 |50 |
| 속성 키의 길이 |100자 |
| 속성 값의 길이 |1,000자 |
| 태그 수 |50 |
| 태그 키의 길이 |100 |
| 태그 값의 길이 |1,000자 |
| CancelUri / CompleteUri / DiagnosticsUri |1,000자 |
| 오류 메시지 길이 |3,000자 |
| 경고 메시지 길이 |300자 |
| 입력 데이터 세트 수 |200 |
| 출력 데이터 세트 수 |20 |


## <a name="metrics"></a>메트릭
| 제한 | 값 |
| --- | --- |
| 실행당 메트릭 이름 수 |50|
| 메트릭 이름당 메트릭 행 수 |1천만|
| 메트릭 행당 열 수 |15|
| 메트릭 열 이름 길이 |255자 |
| 메트릭 열 값 길이 |255자 |
| 업로드된 배치당 메트릭 행 수 | 250 |

> [!NOTE]
> 변수의 형식을 메트릭 이름으로 지정하기 때문에 실행당 메트릭 이름 수 제한에 도달한 경우 대신 한 열이 변수 값이고 두 번째 열이 메트릭 값인 행 메트릭을 사용하는 것이 좋습니다.

## <a name="artifacts"></a>Artifacts

| 제한 | 값 |
| --- | --- |
| 실행당 아티팩트 수 |1천만|
| 아티팩트 경로의 최대 길이 |5,000자 |

## <a name="limit-increases"></a>제한 늘리기
[지원팀에 문의](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)하여 개별 작업 영역에 대한 일부 제한을 늘릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Machine Learning 환경 구성](how-to-configure-environment.md)
- ["리소스 할당량 관리 및 늘리기"](how-to-manage-quotas.md)에서 리소스 할당량을 늘리는 방법에 대해 알아봅니다.

