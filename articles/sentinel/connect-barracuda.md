---
title: Azure Sentinel에 Barracuda 데이터 연결| Microsoft Docs
description: Barracuda WAF(웹 애플리케이션 방화벽) 커넥터를 사용하여 Barracuda 로그를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: e1462246b95da67591cbdfd1f9ed819220de5764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633064"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Barracuda WAF 어플라이언스 연결 

Barracuda WAF(웹 애플리케이션 방화벽) 커넥터를 사용하여 Azure Sentinel에 Barracuda 로그를 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 인사이트를 얻어, 보안 작업 기능이 향상됩니다. Azure Sentinel은 **Barracuda** 와 Log Analytics 에이전트 간의 네이티브 통합을 활용하여 원활한 통합을 제공합니다. 

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-barracuda-waf"></a>Barracuda WAF 구성 및 연결

Barracuda 웹 애플리케이션 방화벽은 Log Analytics 에이전트를 통해 Azure Sentinel에 통합되고 로그를 직접 내보낼 수 있습니다.

1. [Barracuda WAF 구성 흐름](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)으로 이동하고 지침에 따라 다음 매개 변수를 사용하여 연결을 설정합니다.

    - **작업 영역 ID**: Azure Sentinel Barracuda 커넥터 페이지에서 작업 영역 ID의 값을 복사합니다.

    - **기본 키**: Azure Sentinel Barracuda 커넥터 페이지에서 기본 키의 값을 복사합니다.

1. Barracuda 이벤트에 대한 Log Analytics에서 관련 스키마를 사용하려면 **CommonSecurityLog** 및 **barracuda_CL** 을 검색합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Barracuda 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.


