---
title: Azure 센티널에 Apache HTTP 서버 연결 | Microsoft Docs
description: Apache HTTP 서버 커넥터를 사용 하 여 Azure 센티널로 Apache 로그를 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Apache 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096857"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Azure 센티널에 Apache HTTP 서버 연결

> [!IMPORTANT]
> Apache HTTP 서버 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

이 문서에서는 Azure 센티널에 Apache HTTP 서버를 연결 하는 방법을 설명 합니다. Apache HTTP 서버 커넥터를 사용 하면 Apache HTTP 서버 로그를 Azure 센티널에 쉽게 수집할 수 있으므로 통합 문서에서 데이터를 보고, 쿼리 하 여 사용자 지정 경고를 만들고, 조사를 개선 하는 데 통합할 수 있습니다. Apache HTTP 서버와 Azure 센티널 간의 통합으로 인해 Log Analytics 에이전트가 로컬 파일 처리를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure 센티널 작업 영역에 대 한 쓰기 권한이 있어야 합니다.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Log Analytics 에이전트를 통해 Apache HTTP 서버 로그 구성 및 통합

Log Analytics 에이전트를 통해 Azure 작업 영역에 로그 파일을 보내도록 Apache HTTP 서버를 구성 합니다.
Apache HTTP 서버 로그 파일을 읽도록 Log Analytics agent를 구성 합니다.

1. 의 지침 https://httpd.apache.org/docs/2.4/logs.html 에 따라 APACHE HTTP 서버에서 로그 파일 위치를 설정 합니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택한 다음 **Apache HTTP 서버 (미리 보기)** 를 선택 합니다.

1. **커넥터 페이지 열기** 를 선택 합니다.

1. Apache HTTP 서버 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터가 Log Analytics ApacheHTTPServer_CL에 표시 됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Apache HTTP 서버를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
