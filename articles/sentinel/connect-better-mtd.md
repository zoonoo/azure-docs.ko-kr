---
title: Azure 센티널에 더 나은 MTD (모바일 위협 방어) 연결 | Microsoft Docs
description: 더 나은 MTD (모바일 위협 방어) 데이터 커넥터를 사용 하 여 MTD 로그를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 MTD 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541561"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>더 나은 MTD (모바일 위협 방어)를 Azure 센티널에 연결

> [!IMPORTANT]
> 더 나은 MTD (모바일 위협 방어) 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

더 나은 MTD (모바일 위협 방어) 커넥터를 사용 하면 모든 더 나은 MTD 보안 솔루션 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 더 나은 모바일 위협 방어와 Azure 센티널 간의 통합을 통해 REST API을 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>더 나은 모바일 위협 방어 구성 및 연결

더 나은 MTD는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **더 나은 MTD (모바일 위협 방어) (미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. 커넥터 페이지의 단계를 따르고 [더 나은 MTD 설명서의이 페이지](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) 에서 더 나은 MTD 콘솔에 대 한 통합을 마무리 합니다.

    **작업 영역 ID** 및 **기본 키** 값을 입력 하도록 요청 하는 경우 Azure 센티널 커넥터 페이지에서 복사 하 여 더 나은 MTD 구성에 붙여넣습니다.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{작업 영역 ID 및 기본 키}":::

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 에서 **customlogs** 섹션의 다음 테이블 중 하나 이상에 표시 됩니다.
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

분석 규칙, 구하기 쿼리 또는 Azure 센티널의 다른 위치에서 더 나은 MTD 로그를 쿼리하려면 쿼리 창의 맨 위에 위의 테이블 이름 중 하나를 입력 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 더 나은 MTD (모바일 위협 방어)를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
