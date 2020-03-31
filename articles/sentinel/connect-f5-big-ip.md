---
title: F5 BIG-IP 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: F5 BIG-IP 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588283"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 BIG-IP 어플라이언스 연결 

> [!IMPORTANT]
> Azure Sentinel의 F5 BIG-IP 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

F5 BIG-IP 커넥터를 사용하면 모든 F5 BIG-IP 로그를 Azure Sentinel에 쉽게 연결하여 통합 문서를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 더 많은 통찰력을 얻고 보안 운영 기능을 향상시킬 수 있습니다. F5 BIG-IP와 Azure 센티넬 간의 통합은 REST API를 사용합니다.


> [!NOTE]
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG-IP 구성 및 연결 

F5 BIG-IP는 Azure Sentinel에 직접 로그를 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 포털에서 **데이터 커넥터를** 클릭하고 **F5 BIG-IP를** 선택한 다음 **커넥터 페이지를 엽니다.** 
1. F5 BIG-IP를 연결하려면 시스템의 API 끝점에 JSON 선언을 게시해야 합니다. 이 작업을 수행하는 방법에 대한 지침은 [F5 BIG-IP를 Azure Sentinel과 통합을](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)참조하십시오.
8. F5 BIG-IP 커넥터 페이지에서 작업 영역 ID 및 기본 키를 복사하여 스트리밍 데이터에서 지시받은 대로 [Azure Log Analytics에](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)붙여넣습니다.
1. F5 BIG-IP 지침을 완료하면 Azure Sentinel 커넥터 페이지에서 연결된 데이터 형식이 표시됩니다.
1. F5 BIG-IP 이벤트에 대한 로그 분석에서 관련 스키마를 사용하려면 **F5Telemetry_LTM_CL,** **F5Telemetry_system_CL**및 **F5Telemetry_ASM_CL**검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 F5 BIG-IP를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


