---
title: Forcepoint DLP를 Azure Sentinel에 연결 | Microsoft Docs
description: Azure Sentinel에 Forcepoint DLP를 연결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 56ed14cf3010b967965eddf0605d36bafebdbb57
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538475"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Forcepoint DLP를 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Forcepoint DLP(데이터 손실 방지) 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다. 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


Forcepoint DLP 커넥터를 사용하면 DLP 인시던트 데이터를 Azure Sentinel로 자동으로 내보낼 수 있습니다. 이를 통해 사용자 활동 및 데이터 손실 인시던트에 대한 가시성을 얻을 수 있습니다. 또한 Azure 워크로드 및 다른 피드의 데이터와의 상관 관계를 설정하고 Azure Sentinel 내의 통합 문서로 모니터링 기능을 향상시킵니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-forcepoint-dlp"></a>Forcepoint DLP 구성 및 연결

Forcepoint DLP [통합 가이드](https://frcpnt.com/dlp-sentinel)에 설명된 대로 REST API를 통해 JSON 형식의 인시던트 데이터를 Azure 작업 영역에 전달하도록 Forcepoint DLP를 구성합니다.


## <a name="find-your-data"></a>데이터 찾기

Forcepoint DLP 커넥터를 설정하면 Log Analytics의 CustomLogs **ForcepointDLPEvents_CL** 아래에 데이터가 표시됩니다.


Forcepoint DLP용 Log Analytics에서 관련 스키마를 사용하려면 **ForcepointDLPEvents_CL** 을 검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Forcepoint DLP를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.