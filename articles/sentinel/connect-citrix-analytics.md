---
title: Azure Sentinel에 Citrix Analytics(보안) 연결 | Microsoft Docs
description: Azure Sentinel에 Citrix Analytics(보안)를 연결하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: dc9fb34d4338c9376a67050f6aa0f01d14999d5e
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538502"
---
# <a name="connect-citrix-analytics-security"></a>Citrix Analytics 연결(보안) 

Citrix Analytics(보안)와 Azure Sentinel을 통합하면 Citrix Analytics(보안)에서 위험한 이벤트에 대해 분석된 데이터를 Azure Sentinel로 내보내는 데 도움이 됩니다. 사용자 지정 대시보드를 만들고 Citrix Analytics(보안)의 데이터와 함께 다른 원본의 데이터를 분석하고 Logic Apps를 사용하여 사용자 지정 워크플로를 만들어 보안 이벤트를 모니터링하고 완화할 수 있습니다. 



## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한.

- Citrix Cloud의 Citrix Security Analytics에 대한 권리.


## <a name="connect-azure-sentinel-to-citrix-analytics-security"></a>Citrix Analytics(보안)에 Azure Sentinel 연결

1. Azure Portal에서 **Azure Sentinel**  >  **데이터 커넥터** 로 이동한 다음 **Citrix Analytics(보안)** 커넥터를 선택합니다.

2. **커넥터 페이지 열기** 를 선택합니다.

3. **Citrix Analytics(보안)** 페이지의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Citrix Analytics(보안)를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.