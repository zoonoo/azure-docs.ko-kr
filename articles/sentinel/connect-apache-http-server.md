---
title: Azure Sentinel에 Apache HTTP Server 연결 | Microsoft Docs
description: Apache HTTP Server 커넥터를 사용하여 Apache 로그를 Azure Sentinel로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Apache 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6d93854d47c65b500f280107e0bf6d0af3f80681
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567811"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Azure Sentinel에 Apache HTTP Server 연결

> [!IMPORTANT]
> Apache HTTP Server 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 Apache HTTP Server를 Azure Sentinel에 연결하는 방법을 설명합니다. Apache HTTP Server 커넥터를 사용하면 Apache HTTP Server 로그를 Azure Sentinel에 쉽게 수집할 수 있으므로 통합 문서의 데이터를 보고 쿼리하여 사용자 지정 경고를 만들고, 통합하여 조사를 개선할 수 있습니다. Apache HTTP Server와 Azure Sentinel 간의 통합에는 Log Analytics 에이전트의 로컬 파일 처리가 사용됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure Sentinel 작업 영역에 대한 쓰기 권한이 있어야 합니다.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Log Analytics 에이전트를 통해 Apache HTTP Server 로그 구성 및 통합

Log Analytics 에이전트를 통해 로그 파일을 Azure 작업 영역에 보내도록 Apache HTTP Server를 구성합니다.
Apache HTTP Server 로그 파일을 읽도록 Log Analytics 에이전트를 구성합니다.

1. https://httpd.apache.org/docs/2.4/logs.html 의 지침에 따라 Apache HTTP Server에서 로그 파일 위치를 설정합니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택한 다음, **Apache HTTP Server(미리 보기)** 를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. Apache HTTP Server 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

성공적으로 연결되면 데이터는 ApacheHTTPServer_CL 아래의 Log Analytics에 표시됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Apache HTTP Server를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.