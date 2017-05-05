---
title: "성능 및 확장을 관리하기 위한 Azure Event Hubs 모범 사례 | Microsoft Docs"
description: "Azure Event Hubs 성능 및 확장 모범 사례"
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
ms.openlocfilehash: 920378e21985ae6bb63914e6c065314aaf979214
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-managing-performance-and-scale"></a>성능 및 확장을 관리하기 위한 🔧 Event Hubs 모범 사례

> [!NOTE]
> 
> 이 항목은 아직 작성되지 않았습니다. 
>
> 이 콘텐츠의 범위 및 접근 방식에 도움이 되는 여러분의 의견을 환영합니다. 이 [GitHub의 문제](https://github.com/Azure/azure-event-hubs/issues/306)에서 상태를 추적하고 입력을 제공할 수 있습니다.
> 
> [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md)에 참여하는 방법에 대해 자세히 알아보세요.

이 문서에서는 다음을 설명합니다.

- 분할
- 처리량 단위
- 병렬 처리
- 일반적인 병목 상태


