---
title: Azure 센티널에 Barracuda 데이터 연결 | Microsoft Docs
description: Barracuda 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 5142204db86158937c16d19bf52fb677099598a5
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72284665"
---
# <a name="connect-your-barracuda-appliance"></a>Barracuda 어플라이언스 연결 



Barracuda WAF (웹 응용 프로그램 방화벽) 커넥터를 사용 하면 Azure 센티널에 Barracuda 로그를 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이렇게 하면 조직의 네트워크에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. Azure 센티널은 **Barracuda** 와 Log Analytics 에이전트 간의 네이티브 통합을 활용 하 여 원활한 통합을 제공 합니다. 


> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-barracuda-waf"></a>Barracuda WAF 구성 및 연결
Barracuda 웹 응용 프로그램 방화벽은 Log Analytics 에이전트를 통해 Azure 센티널에 직접 로그를 통합 하 고 내보낼 수 있습니다.
1. [Barracuda WAF 구성 흐름](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)으로 이동 하 고 지침에 따라 다음 매개 변수를 사용 하 여 연결을 설정 합니다.
    - **작업 영역 id**: Azure 센티널 Barracuda 커넥터 페이지에서 작업 영역 id의 값을 복사 합니다.
    - **기본 키**: Azure 센티널 Barracuda 커넥터 페이지에서 기본 키의 값을 복사 합니다.
1. Barracuda 이벤트에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **CommonSecurityLog** 및 **barracuda_CL**를 검색 합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Barracuda 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.

