---
title: Azure Stream Analytics 오류 코드 문제 해결
description: 내부 오류 코드의 Azure Stream Analytics 문제를 해결합니다.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9dc3d873ddfef9a729f583cd914ca4700d562ff3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045232"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics 내부 오류 코드

활동 로그 및 리소스 로그를 사용하여 Azure Stream Analytics 작업에서 예기치 않은 동작을 디버그할 수 있습니다. 이 문서에는 모든 내부 오류 코드에 대한 설명이 나와 있습니다. 내부 오류는 Stream Analytics에서 오류가 시스템의 내부 가용성 오류인지 아니면 버그인지 여부를 구분할 수 없는 경우 Stream Analytics 플랫폼 내에서 throw되는 일반 오류입니다.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **원인**: Cosmos DB에 쓰는 데 사용되는 일괄 처리 크기가 너무 큽니다. 
* **권장 사항**: 더 작은 일괄 처리 크기로 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

* [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics 출력 문제 해결](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics 쿼리 문제 해결](stream-analytics-troubleshoot-query.md)
* [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics 데이터 오류](data-errors.md)