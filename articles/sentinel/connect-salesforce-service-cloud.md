---
title: Salesforce Service Cloud 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Salesforce Service Cloud 데이터 커넥터를 사용하여 Salesforce 로그를 Azure Sentinel로 가져오는 방법을 알아봅니다. 통합 문서에서 Salesforce 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 7c9fe214cd9527a5e49ff2717222355e9dd8319f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567828"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Salesforce Service Cloud를 Azure Sentinel에 연결

> [!IMPORTANT]
> Salesforce Service Cloud 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 Salesforce Service Cloud 솔루션을 Azure Sentinel에 연결하는 방법을 설명합니다. Salesforce Service Cloud 데이터 커넥터를 사용하면 Salesforce 데이터를 Azure Sentinel에 쉽게 연결하여 통합 문서에서 보고, 이를 사용하여 사용자 지정 경고를 만들고, 통합하여 조사를 개선할 수 있습니다. Salesforce Service Cloud와 Azure Sentinel의 통합에서는 REST API를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- 함수 앱을 만들려면 Azure Functions에 대한 읽기 및 쓰기 권한이 있어야 합니다. [Azure Functions에 대해 자세히 알아보세요](../azure-functions/index.yml).

- **Salesforce API 사용자 이름**, **Salesforce API 암호**, **Salesforce 보안 토큰**, **Salesforce 소비자 키**, **Salesforce 소비자 비밀** 등 Salesforce REST API 자격 증명이 있어야 합니다. [Salesforce REST API에 대해 자세히 알아보세요](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Salesforce Service Cloud 구성 및 연결

Salesforce Service Cloud는 로그를 통합하고 직접 Azure Sentinel로 내보낼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Salesforce Service Cloud(미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. 커넥터 페이지의 **구성** 섹션에 설명된 단계를 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 **로그** 의 **CustomLogs** 섹션에 있는 `SalesforceServiceCloud_CL` 테이블에 로그가 표시됩니다.

몇 가지 유용한 샘플 쿼리는 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Salesforce Service Cloud를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.