---
title: "Batch 분석 - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 2fda4d9c-f782-4088-9320-656b450e3100
caps.latest.revision: 7
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 4b73637e0ec6798b701d5a13f96c504bcbb14cc7
ms.lasthandoff: 04/13/2017

---
# <a name="batch-analytics"></a>Batch 분석
Batch 분석의 항목에는 Batch 서비스 리소스에 사용할 수 있는 이벤트 및 경고에 대한 참조 정보가 포함되어 있습니다.

Batch 진단 로그를 활성화하고 사용하는 방법에 대한 자세한 내용은 [Azure Batch 진단 로깅](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)을 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그

Azure Batch 서비스는 특정 Batch 리소스의 수명 동안 다음과 같은 진단 로그 이벤트를 내보냅니다.

**서비스 로그 이벤트**
* [풀 만들기](batch-pool-create-event.md)
* [풀 삭제 시작](batch-pool-delete-start-event.md)
* [풀 삭제 완료](batch-pool-delete-complete-event.md)
* [풀 크기 조정 시작](batch-pool-resize-start-event.md)
* [풀 크기 조정 완료](batch-pool-resize-complete-event.md)
* [작업 시작](batch-task-start-event.md)
* [작업 완료](batch-task-complete-event.md)
* [작업 실패](batch-task-fail-event.md)
