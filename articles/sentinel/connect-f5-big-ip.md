---
title: Azure Sentinel에 F5 BIG-IP 데이터 연결| Microsoft Docs
description: Azure Sentinel에 F5 BIG-IP 데이터를 연결하는 방법 알아보기.
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
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092794"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 BIG-IP 어플라이언스 연결 

F5 BIG-IP 커넥터를 사용하면 모든 F5 BIG-IP 로그를 Azure Sentinel에 쉽게 연결하여, 통합 문서를 보고, 사용자 지정 경고를 만들어, 조사 기능을 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 인사이트를 얻어, 보안 작업 기능이 향상됩니다. F5 BIG-IP와 Azure Sentinel 간의 통합에는 REST API가 사용됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG-IP 구성 및 연결 

F5 BIG-IP는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭하고 **F5 BIG-IP** 를 선택한 다음 **커넥터 페이지를 엽니다**. 
1. F5 BIG-IP를 연결하려면 시스템의 API 엔드포인트에 JSON 선언을 게시해야 합니다. 해당 작업을 수행하는 방법에 대한 지침은 [F5 BIG-IP를 Azure Sentinel과 통합](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)을 참조하세요.
8. F5 BIG-IP 커넥터 페이지에서 작업 영역 ID 및 기본 키를 복사하고, [데이터를 Azure 로그 분석으로 스트리밍](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)에 설명된 대로 붙여넣습니다.
1. F5 BIG-IP 지침을 완료하고 나면 Azure Sentinel 커넥터 페이지에 연결된 데이터 형식이 표시됩니다.
1. F5 BIG-IP 이벤트에 대한 로그 분석 내 관련 스키마를 사용하려면 **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**, **F5Telemetry_ASM_CL** 을 검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 F5 BIG-IP를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.


