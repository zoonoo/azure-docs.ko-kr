---
title: "오류 처리에 대한 Azure Event Hubs 모범 사례 | Microsoft Docs"
description: "Azure Event Hubs 오류 처리 및 다시 시도"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>오류 처리에 대한 🔧 Event Hubs 모범 사례

> [!NOTE]
> 
> 이 항목은 아직 작성되지 않았습니다. 
>
> 이 콘텐츠의 범위 및 접근 방식에 도움이 되는 여러분의 의견을 환영합니다. 이 [GitHub의 문제](https://github.com/Azure/azure-event-hubs/issues/305)에서 상태를 추적하고 입력을 제공할 수 있습니다.
> 
> [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md)에 참여하는 방법에 대해 자세히 알아보세요.

이 문서에서는 다음을 설명합니다.

- 발생하는 다른 종류의 오류는 무엇인가요?
- 오류가 발생하면 어떻게 되나요?
- 어떤 종류의 오류 시 다시 시도해야 하나요? 다시 시도하지 않는 오류 종류는 어느 것인가요?
- 사용자 지정 다시 시도 정책

