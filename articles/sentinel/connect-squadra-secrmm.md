---
title: 스쿼드라 테크놀로지스 secRMM 데이터를 Azure 센티넬에 연결| 마이크로 소프트 문서
description: 스쿼드라 테크놀로지스 secRMM 데이터를 Azure 센티넬에 연결하는 방법에 대해 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588113"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>스쿼드라 테크놀로지스 secRMM 데이터를 Azure 센티넬에 연결 

> [!IMPORTANT]
> Azure Sentinel의 Squadra Technologies 보안 이동식 미디어 관리자(secRMM) 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


Squadra Technologies secRMM 커넥터를 사용하면 Squadra Technologies secRMM 보안 솔루션 로그를 Azure Sentinel과 쉽게 연결할 수 있습니다. 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이 커넥터를 사용하면 USB 이동식 스토리지 이벤트에 대한 통찰력을 얻을 수 있습니다. 스쿼드라 테크놀로지스 secRMM과 Azure 센티넬 간의 통합은 REST API를 사용합니다.


> [!NOTE]
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>스쿼드라 테크놀로지스 secRMM 구성 및 연결 

Squadra Technologies secRMM은 Azure Sentinel에 직접 로그를 통합하고 내보낼 수 있습니다.
1. Azure Sentinel 포털에서 데이터 커넥터를 클릭하고 Squadra Technologies secRMM을 선택한 다음 커넥터 페이지를 엽니다.

2. [Azure Sentinel에 대한 Squadra Technologies 온보딩 가이드에](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) 설명된 단계를 수행하여 Azure Sentinel에서 Squadra secRMM 데이터를 가져옵니다.   


## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 사용자 지정 secRMM_CL 아래의 로그 분석에 데이터가 표시됩니다.
분대 기술 secRMM에 대한 로그 분석에서 관련 스키마를 사용하려면 secRMM_CL 검색합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Squadra Technologies secRMM을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

