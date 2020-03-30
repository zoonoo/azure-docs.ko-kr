---
title: 바라쿠다 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: 바라쿠다 데이터를 Azure 센티넬에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588487"
---
# <a name="connect-your-barracuda-appliance"></a>바라쿠다 기기 연결 



Barracuda 웹 응용 프로그램 방화벽(WAF) 커넥터를 사용하면 Barracuda 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 더 많은 통찰력을 얻고 보안 운영 기능을 향상시킬 수 있습니다. Azure Sentinel은 **Barracuda와** Log Analytics 에이전트 간의 기본 통합을 활용하여 원활한 통합을 제공합니다. 


> [!NOTE]
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-barracuda-waf"></a>바라쿠다 WAF 구성 및 연결
Barracuda 웹 응용 프로그램 방화벽은 로그 분석 에이전트를 통해 Azure Sentinel에 직접 로그를 통합하고 내보낼 수 있습니다.
1. [Barracuda WAF 구성 흐름으로](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)이동하여 다음 매개 변수를 사용하여 연결을 설정하는 방법에 따라 다음을 수행합니다.
    - **작업 영역 ID**: Azure Sentinel Barracuda 커넥터 페이지에서 작업 영역 ID 값을 복사합니다.
    - **기본 키**: Azure Sentinel Barracuda 커넥터 페이지에서 기본 키 값을 복사합니다.
1. Barracuda 이벤트에 대한 로그 분석에서 관련 스키마를 사용하려면 **CommonSecurityLog** 및 **barracuda_CL**검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 바라쿠다 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


