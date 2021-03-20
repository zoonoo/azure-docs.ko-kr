---
title: Squadra 기술 secRMM 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Squadra 기술을 Azure 센티널에 연결 하는 방법에 대해 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632904"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Squadra 기술 secRMM 데이터를 Azure 센티널에 연결 

Squadra 기술 secRMM 커넥터를 사용 하면 Squadra 기술 secRMM 보안 솔루션 로그를 Azure 센티널과 쉽게 연결할 수 있습니다. 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이 커넥터는 USB 이동식 저장소 이벤트에 대 한 정보를 제공 합니다. Squadra 기술 secRMM와 Azure 센티널 간의 통합은 REST API를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra 기술 구성 및 연결 secRMM 

Squadra 기술 secRMM은 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.
1. Azure 센티널 포털에서 데이터 커넥터를 클릭 하 고 Squadra 기술 secRMM 및 커넥터 페이지 열기를 차례로 선택 합니다.

2. Azure 센티널에 [대 한 Squadra 기술 온 보 딩 가이드](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) 에 설명 된 단계에 따라 azure 센티널에서 Squadra secRMM 데이터를 가져옵니다.   

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 에서 **customlogs** 섹션의 테이블에 표시 됩니다 `secRMM_CL` .

Squadra 기술 secRMM 로그를 쿼리하려면 쿼리 창의 맨 위에 테이블 이름을 입력 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Squadra 기술을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
