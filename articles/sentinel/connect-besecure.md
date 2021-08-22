---
title: Azure Sentinel에 Beyond Security beSECURE 데이터 연결 | Microsoft Docs
description: Beyond Security beSECURE 데이터 커넥터를 사용하여 beSECURE 로그를 Azure Sentinel로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 beSECURE 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.openlocfilehash: c7daf98effad685d5e0044df3c416f3023fe42ba
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538426"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Azure Sentinel에 Beyond Security beSECURE 연결

> [!IMPORTANT]
> Beyond Security beSECURE 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Beyond Security beSECURE 커넥터를 사용하면 모든 beSECURE 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. beSECURE와 Azure Sentinel 간의 통합에는 REST API가 사용됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-besecure"></a>beSECURE 구성 및 연결

beSECURE는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Beyond Security beSECURE(미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. 아래 단계에 따라 beSECURE 솔루션을 구성하여 검색 결과, 검색 상태 및 감사 내역 로그를 Azure Sentinel로 보냅니다.

    **통합 메뉴에 액세스:**
    1. ‘자세히’ 메뉴 옵션을 클릭합니다.

    1. 서버 선택

    1. 통합 선택

    1. Azure Sentinel 사용

    **Azure 센티널 설정을 사용하 여 다음과 같이 beSECURE를 제공합니다.**

      Azure 센티널 커넥터 페이지에서 *작업 영역 ID* 및 *기본 키* 값을 복사하고 beSECURE 구성에 붙여넣은 후 **수정** 을 클릭합니다.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{작업 영역 ID 및 기본 키}":::

## <a name="find-your-data"></a>데이터 찾기

연결이 설정되면 데이터는 **로그** 에서 **CustomLogs** 섹션의 다음 표 중 하나 이상에 표시됩니다.
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

분석 규칙, 헌팅 쿼리, 조사 또는 Azure Sentinel의 다른 위치에서 beSECURE 로그를 쿼리하려면 쿼리 창의 맨 위에 위의 표 이름 중 하나를 입력합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 beSECURE를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.