---
title: Orca 보안 경고를 Azure Sentinel에 연결 | Microsoft Docs
description: Orca 보안 경고 데이터를 Azure Sentinel에 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 5d90b6fb67d2cb00bb86ca10eca03d07f1135559
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567820"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Orca 보안 경고를 Azure Sentinel에 연결 

> [!IMPORTANT]
> Azure Sentinel의 Orca 보안 경고 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Orca 보안 경고 커넥터를 사용하면 [Orca 경고](https://orca.security/) 보안 솔루션 경고를 Azure Sentinel로 쉽게 가져올 수 있으므로 통합 문서에서 보고, 사용자 지정 경고를 만들고, 통합하여 조사를 개선할 수 있습니다. Orca 보안 경고와 Azure Sentinel 간의 통합은 REST API를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-orca-security-alerts"></a>Orca 보안 경고 구성 및 연결

Orca 보안 경고는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭하고 **Orca 보안 경고** 를 선택한 다음, **커넥터 페이지를 엽니다**.

2. Orca 플랫폼에서 통합을 완료하려면 https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration 을 참조하세요.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 Log Analytics에 있는 **OrcaAlerts_CL** 테이블의 **CustomLogs** 아래에 데이터가 표시됩니다.
Orca 경고의 Log Analytics에서 관련 스키마를 사용하려면 `OrcaAlerts_CL`을 검색합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Orca 보안 경고를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.