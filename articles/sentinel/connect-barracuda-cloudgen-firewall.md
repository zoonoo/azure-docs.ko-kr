---
title: Azure Sentinel에 Barracuda CloudGen 방화벽 연결 | Microsoft Docs
description: Barracuda CloudGen 방화벽을 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 9e0aa186e742318ab5793fa8390251d94327bf08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632710"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall 연결

Barracuda CGFW(CloudGen 방화벽) 커넥터를 사용하면 Barracuda CGFW 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크 내부의 인사이트를 파악해, 보안 작업 기능이 향상됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한.

- Barracuda CloudGen 방화벽은 Syslog를 통해 로그를 내보내도록 구성되어야 합니다.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Barracuda CloudGen 방화벽에 Azure Sentinel 연결

1. Azure Portal에서 **Azure Sentinel** >  **데이터 커넥터로** 이동한 다음 **Barracuda CloudGen 방화벽** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. **Barracuda CloudGen 방화벽** 페이지의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Barracuda CloudGen 방화벽을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
