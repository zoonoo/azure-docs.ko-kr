---
title: Azure 센티널에 VMware 참조 블랙 Cloud 끝점 표준 데이터 연결 | Microsoft Docs
description: VMware 참조 블랙 Cloud 끝점 표준 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098439"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Azure Function을 사용 하 여 Azure 센티널에 VMware 참조 블랙 Cloud 끝점 표준 연결

> [!IMPORTANT]
> Azure 센티널의 VMware 참조 블랙 클라우드 끝점 표준 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

VMware 참조 블랙 Cloud Endpoint Standard 커넥터를 사용 하면 모든 [VMware 참조 블랙 끝점 표준](https://www.carbonblack.com/products/endpoint-standard/) 보안 솔루션 로그를 Azure 센티널과 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. VMware 참조와 Azure 센티널 간의 통합은 Azure Functions를 사용 하 여 REST API를 사용 하 여 로그 데이터를 끌어옵니다.


> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>VMware 참조 블랙 Cloud 끝점 표준 구성 및 연결

Azure Functions는 VMware 참조 블랙 Cloud 끝점 표준에서 직접 이벤트와 로그를 통합 하 고 끌어오고 Azure 센티널에 전달할 수 있습니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **VMware 참조 검정** 커넥터를 선택 합니다.
2. **커넥터 페이지 열기**를 선택 합니다.
3. **VMware 참조 블랙** 페이지의 지침을 따릅니다.


## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 Log Analytics **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** 및 * * * * CarbonBlackEvents_CL * * * * 테이블에 표시 됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 함수 앱을 사용 하 여 VMware 참조 블랙 Cloud 끝점 표준을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

