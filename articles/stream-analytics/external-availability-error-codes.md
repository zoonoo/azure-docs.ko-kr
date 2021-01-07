---
title: 외부 가용성 오류 코드 - Azure Stream Analytics
description: 외부 가용성 오류 코드의 Azure Stream Analytics 문제를 해결합니다.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045283"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure Stream Analytics 외부 가용성 오류 코드

활동 로그 및 리소스 로그를 사용하여 Azure Stream Analytics 작업에서 예기치 않은 동작을 디버그할 수 있습니다. 이 문서에서는 모든 외부 가용성 오류 코드에 대한 설명을 나열합니다. 외부 가용성 오류는 종속 서비스를 사용할 수 없을 때 발생합니다.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **원인**: 서비스를 일시적으로 사용할 수 없습니다.
* **권장 사항**: Stream Analytics에서 서비스에 계속 연결을 시도합니다.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **원인**: Stream Analytics에서 EventHub와 통신하는 동안 오류가 발생했습니다. 


## <a name="next-steps"></a>다음 단계

* [입력 연결 문제 해결](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics 출력 문제 해결](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics 쿼리 문제 해결](stream-analytics-troubleshoot-query.md)
* [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics 데이터 오류](data-errors.md)
