---
title: Salesforce 서비스 클라우드 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Salesforce 서비스 클라우드 데이터 커넥터를 사용 하 여 Salesforce 로그를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Salesforce 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570540"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Salesforce 서비스 클라우드를 Azure 센티널에 연결

> [!IMPORTANT]
> Salesforce 서비스 클라우드 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

이 문서에서는 Salesforce 서비스 클라우드 솔루션을 Azure 센티널에 연결 하는 방법을 설명 합니다. Salesforce 서비스 클라우드 데이터 커넥터를 사용 하면 Salesforce 데이터를 Azure 센티널에 쉽게 연결 하 여 통합 문서에서 보고,이를 사용 하 여 사용자 지정 경고를 만들고,이를 통합 하 여 조사를 개선할 수 있습니다. Salesforce Service 클라우드와 Azure 센티널 간의 통합을 통해 REST API을 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역에 대 한 공유 키에 대 한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- 함수 앱을 만들려면 Azure Functions에 대 한 읽기 및 쓰기 권한이 있어야 합니다. [Azure Functions에 대해 자세히 알아보세요](../azure-functions/index.yml).

- Salesforce **Api Username**, Salesforce **api Password**, **Salesforce 보안 토큰**, **salesforce 소비자 키**, **salesforce 소비자 암호** 와 같은 salesforce REST API 자격 증명이 있어야 합니다. [Salesforce REST API에 대해 자세히 알아보세요](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Salesforce 서비스 클라우드 구성 및 연결

Salesforce 서비스 클라우드는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **Salesforce Service Cloud (미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. 커넥터 페이지의 **구성** 섹션에 설명 된 단계를 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 에서 **customlogs** 섹션의 테이블에 표시 됩니다 `SalesforceServiceCloud_CL` .

몇 가지 유용한 샘플 쿼리는 커넥터 페이지의 **다음 단계** 탭을 참조 하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Salesforce 서비스 클라우드를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.