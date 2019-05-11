---
title: Azure 미리 보기 Sentinel Barracuda 데이터 연결할 | Microsoft Docs
description: Barracuda 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6b0285903537dafb004b5aca033b50560247c605
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204453"
---
# <a name="connect-your-barracuda-appliance"></a>Barracuda 어플라이언스에서 연결 

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Barracuda 웹 응용 프로그램 방화벽 (WAF) 커넥터를 사용 하 여 Azure Sentinel, 대시보드 보기, 사용자 지정 경고를 만들고, 조사가 향상 된 Barracuda 로그를 쉽게 연결할 수 합니다. 이 조직의 네트워크에 대 한 자세한 정보를 제공 하며 사용자 보안 작업 기능을 향상 시킵니다. Azure Sentinel 간의 네이티브 통합 활용 **Barracuda** 및 Microsoft Azure OMS의 원활한 통합을 제공 합니다. 


> [!NOTE]
> 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-barracuda-waf"></a>구성 및 Barracuda WAF를 연결 합니다.
Barracuda Web Application Firewall을 통합 하 고 Azure OMS 서버를 통해 Azure Sentinel에 직접 로그를 내보낼 수 있습니다.
1. 로 이동 [Barracuda WAF 구성 흐름](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), 이러한 매개 변수를 사용 하 여 연결을 설정 하는 지침을 따릅니다.
    - **작업 영역 ID**: Azure Sentinel Barracuda 커넥터 페이지에서 작업 영역 ID의 값을 복사 합니다.
    - **기본 키**: Azure Sentinel Barracuda 커넥터 페이지에서 기본 키의 값을 복사 합니다.
2. Sentinel Azure portal에서 Azure Sentinel 배포한는 작업 영역으로 이동 하 고 선택한 행의 끝에서 줄임표 (...)를 선택 **고급 설정**합니다. 
1. 선택 **데이터** 차례로 **Syslog**합니다.
1. Barracuda에 설정한 시설 존재 심각도 설정 하 고 클릭 했는지 **저장할**합니다.
6. Log Analytics에서 관련 스키마를 사용 하 여 Barracuda 이벤트를 검색할 **CommonSecurityLog**합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

수준도 로그를 Log Analytics에 나타나기 시작 될 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Barracuda 어플라이언스에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

