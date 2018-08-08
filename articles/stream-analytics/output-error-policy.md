---
title: Azure Stream Analytics의 출력 오류 정책
description: Azure Stream Analytics에서 사용할 수 있는 출력 오류 처리 정책에 대해 알아봅니다.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392570"
---
# <a name="output-error-policy"></a>출력 오류 정책
이 문서에서는 Azure Stream Analytics에서 구성할 수 있는 출력 데이터 오류 처리 정책에 대해 설명합니다.

출력 데이터 오류 처리 정책은 Stream Analytics 작업에 의해 생성된 출력 이벤트가 대상 싱크의 스키마와 맞지 않는 경우 발생하는 데이터 변환 오류에만 적용됩니다. **재시도** 또는 **드롭**을 선택하여 이 정책을 구성할 수 있습니다. Azure Portal에서 Stream Analytics 작업의 **구성** 아래에서 **오류 정책**을 선택하고 옵션을 선택합니다.

![오류 정책 - 위치](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>다시 시도
오류가 발생하면 Azure Stream Analytics는 쓰기가 성공할 때까지 무기한으로 이벤트 작성을 다시 시도합니다. 재시도에 대한 시간 제한은 없습니다. 결국, 재시도 중인 이벤트에 의해 모든 후속 이벤트의 처리가 차단됩니다. 이 옵션은 기본 출력 오류 처리 정책입니다.

## <a name="drop"></a>드롭
Azure Stream Analytics는 데이터 변환 오류를 초래하는 모든 출력 이벤트를 삭제합니다. 삭제된 이벤트는 나중에 다시 처리하도록 복구할 수 없습니다.


모든 일시적인 오류(예: 네트워크 오류)는 출력 오류 처리 정책 구성과 상관없이 다시 시도됩니다.


## <a name="next-steps"></a>다음 단계
[Azure Stream Analytics 문제 해결 가이드](stream-analytics-troubleshooting-guide.md)
