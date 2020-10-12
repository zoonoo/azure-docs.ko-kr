---
title: Azure 센티널에 Citrix 분석 (보안) 연결 | Microsoft Docs
description: Azure 센티널에 Citrix Analytics (보안)를 연결 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: bbc77340375b22f137045d68b7449dd1e8adc4b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588385"
---
# <a name="connect-citrix-analytics-security"></a>Citrix Analytics 연결(보안) 

Citrix Analytics (보안)와 Azure 센티널의 통합을 통해 Citrix 분석 (보안)에서 위험한 이벤트에 대해 분석 된 데이터를 Azure 센티널로 내보낼 수 있습니다. 사용자 지정 대시보드를 만들고 Citrix Analytics (보안)에서와 함께 다른 원본에서 데이터를 분석 하 고 Logic Apps를 사용 하 여 사용자 지정 워크플로를 만들어 보안 이벤트를 모니터링 하 고 완화할 수 있습니다. 



## <a name="prerequisites"></a>사전 요구 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한입니다.

- Citrix 클라우드의 Citrix 보안 분석으로의 권리


## <a name="connect-azure-sentinel-to-citrix-analytics-security"></a>Citrix Analytics에 Azure 센티널 연결 (보안)

1. Azure Portal에서 **Azure 센티널**  >  **데이터 커넥터** 로 이동한 다음 **Citrix Analytics (보안)** 커넥터를 선택 합니다.

2. **커넥터 페이지 열기**를 선택 합니다.

3. **Citrix Analytics (보안)** 페이지의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Citrix Analytics (보안)를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.


