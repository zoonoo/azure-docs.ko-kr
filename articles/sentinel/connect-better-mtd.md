---
title: BETTER MTD(모바일 위협 방어)를 Azure Sentinel에 연결 | Microsoft Docs
description: BETTER MTD(모바일 위협 방어) 데이터 커넥터를 사용하여 MTD 로그를 Azure Sentinel에 끌어오는 방법을 알아봅니다. 통합 문서에서 MTD 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541561"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>BETTER MTD(모바일 위협 방어)를 Azure Sentinel에 연결

> [!IMPORTANT]
> BETTER MTD(모바일 위협 방어) 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

BETTER MTD(모바일 위협 방어) 커넥터를 사용하면 모든 BETTER MTD 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. BETTER 모바일 위협 방어와 Azure Sentinel 간의 통합을 통해 REST API를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>BETTER 모바일 위협 방어 구성 및 연결

BETTER MTD는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **BETTER MTD(모바일 위협 방어)(미리 보기)** 를 선택한 다음, **커넥터 페이지를 엽니다**.

1. 커넥터 페이지와 [BETTER MTD 설명서 페이지](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration)의 단계에 따라 BETTER MTD 콘솔에서 통합을 완료합니다.

    **작업 영역 ID** 와 **기본 키** 값을 입력하라는 요청이 있으면 Azure Sentinel 커넥터 페이지에서 복사하여 BETTER MTD 구성에 붙여넣습니다.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{작업 영역 ID 및 기본 키}":::

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 **로그** 에서 **CustomLogs** 섹션의 다음 표 중 하나 이상에 표시됩니다.
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

분석 규칙, 헌팅 쿼리 또는 Azure Sentinel의 다른 위치에서 BETTER MTD 로그를 쿼리하려면 쿼리 창의 맨 위에 위의 표 이름 중 하나를 입력합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 BETTER MTD(모바일 위협 방어)를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
