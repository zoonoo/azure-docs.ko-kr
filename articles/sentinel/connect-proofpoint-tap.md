---
title: Proofpoint 대상 공격 보호(TAP) 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Proofpoint 대상 공격 보호(TAP) 데이터를 Azure Sentinel에 연결하는 방법 알아보기.
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
ms.openlocfilehash: 86018bafaa42eac01e5dccf8da1d290b64e2475c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092981"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Azure Function을 사용하여 Azure Sentinel에 Proofpoint TAP 연결

> [!IMPORTANT]
> Azure Sentinel의 Proofpoint TAP 데이터 커넥터는 현재 퍼블릭 미리 보기로 제공됩니다.
> 해당 기능은 별도의 Service Level Agreement(서비스 수준 규약) 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Proofpoint Targeted Attack Protection(TAP) 커넥터를 사용하여 모든 [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Proofpoint TAP과 Azure Sentinel 간의 통합에는 REST API를 사용하여 로그 데이터를 끌어오는 Azure Functions가 사용됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-proofpoint-tap"></a>Proofpoint TAP 구성 및 연결

Azure Functions는 Proofpoint TAP에서 직접 이벤트와 로그를 통합하고, 가져와 Azure Sentinel에 전달할 수 있습니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭하고 **Proofpoint TAP** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. **Proofpoint TAP** 페이지의 지시 사항을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL**, **ProofpointTAPClicksBlocked_CL** 테이블 아래에 데이터가 나타납니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다.

## <a name="next-steps"></a>다음 단계

이상 Azure Function 앱을 사용하여 Proofpoint TAP를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
