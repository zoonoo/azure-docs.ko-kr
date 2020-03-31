---
title: 바라쿠다 클라우드젠 방화벽을 Azure 센티넬에 연결| 마이크로 소프트 문서
description: 바라쿠다 CloudGen 방화벽을 Azure 센티넬에 연결하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588504"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall 연결

바라쿠다 CloudGen 방화벽(CGFW) 커넥터를 사용하면 바라쿠다 CGFW 로그를 Azure Sentinel과 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 더 많은 통찰력을 얻고 보안 운영 기능을 향상시킬 수 있습니다.




## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 사용 권한을 읽고 씁니다.

- 바라쿠다 CloudGen 방화벽은 Syslog를 통해 로그를 내보내도록 구성되어야 합니다.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Azure 센티넬을 바라쿠다 클라우드젠 방화벽에 연결

1. Azure 포털에서 Azure **Sentinel** > 데이터 커넥터로 이동한 다음 **Barracuda CloudGen 방화벽** 커넥터를**선택합니다.**

2. **연결선 열기 페이지를**선택합니다.

3. **바라쿠다 CloudGen 방화벽** 페이지의 지침을 따르십시오.


## <a name="next-steps"></a>다음 단계
이 문서에서는 바라쿠다 CloudGen 방화벽을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


