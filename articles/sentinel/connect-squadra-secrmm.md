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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588113"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Squadra 기술 secRMM 데이터를 Azure 센티널에 연결 

> [!IMPORTANT]
> Azure 센티널의 Squadra 기술 보안 이동식 미디어 관리자 (secRMM) 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


Squadra 기술 secRMM 커넥터를 사용 하면 Squadra 기술 secRMM 보안 솔루션 로그를 Azure 센티널과 쉽게 연결할 수 있습니다. 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이 커넥터는 USB 이동식 저장소 이벤트에 대 한 정보를 제공 합니다. Squadra 기술 secRMM와 Azure 센티널 간의 통합은 REST API를 사용 합니다.


> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra 기술 구성 및 연결 secRMM 

Squadra 기술 secRMM은 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.
1. Azure 센티널 포털에서 데이터 커넥터를 클릭 하 고 Squadra 기술 secRMM 및 커넥터 페이지 열기를 차례로 선택 합니다.

2. Azure 센티널에 [대 한 Squadra 기술 온 보 딩 가이드](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) 에 설명 된 단계에 따라 azure 센티널에서 Squadra secRMM 데이터를 가져옵니다.   


## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 CustomLogs secRMM_CL Log Analytics에 표시 됩니다.
Squadra 기술 secRMM에 Log Analytics에서 관련 스키마를 사용 하려면 secRMM_CL를 검색 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Squadra 기술을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.

