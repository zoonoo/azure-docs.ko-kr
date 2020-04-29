---
title: Azure 센티널에 Barracuda CloudGen 방화벽 연결 | Microsoft Docs
description: Barracuda CloudGen 방화벽을 Azure 센티널에 연결 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: aaedbfdd3b1bbbc653756d74ee86fc277b21caec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588504"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall 연결

CGFW (Barracuda CloudGen Firewall) 커넥터를 사용 하면 Barracuda CGFW 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이렇게 하면 조직의 네트워크에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다.




## <a name="prerequisites"></a>전제 조건

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한입니다.

- Barracuda CloudGen 방화벽은 Syslog를 통해 로그를 내보내도록 구성 되어야 합니다.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Barracuda CloudGen 방화벽에 Azure 센티널 연결

1. Azure Portal에서 **Azure 센티널** > **데이터 커넥터** 로 이동한 다음 **Barracuda cloudgen 방화벽** 커넥터를 선택 합니다.

2. **커넥터 페이지 열기**를 선택 합니다.

3. **Barracuda CloudGen 방화벽** 페이지의 지침을 따릅니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Barracuda CloudGen 방화벽을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


