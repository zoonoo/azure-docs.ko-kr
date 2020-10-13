---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169914"
---
| | |
|--|--|
|**`<DESTINATION>`**| 로그가 전송 되는 대상입니다. 유효한 값은 `AppInsights` 및 `Blob`입니다.<br/>를 사용 하 `AppInsights` [는 경우 함수 앱에서 Application Insights를 사용할 수](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration)있는지 확인 합니다.<br/>대상을로 설정 하면 `Blob` `azure-functions-scale-controller` 응용 프로그램 설정에 설정 된 기본 저장소 계정에 지정 된 blob 컨테이너에 로그가 생성 됩니다 `AzureWebJobsStorage` . |
|**`<VERBOSITY>`** | 로깅 수준을 지정 합니다. 지원되는 값은 `None`, `Warning` 및 `Verbose`입니다.<br/>로 설정 되 면 `Verbose` 크기 조정 컨트롤러는 작업자 수의 모든 변경에 대 한 이유 뿐만 아니라 해당 결정에 해당 하는 트리거에 대 한 정보를 기록 합니다. 자세한 로그에는 크기 조정 컨트롤러를 실행 하기 전후에 트리거에 사용 되는 트리거 경고와 해시가 포함 됩니다. |

> [!CAUTION]
> 크기 조정 컨트롤러 로깅을 사용 하도록 설정 하지 마세요. 충분 한 데이터를 수집 하 여 크기 조정 컨트롤러의 동작을 이해 한 다음 사용 하지 않도록 설정할 때까지 로깅을 사용 합니다.