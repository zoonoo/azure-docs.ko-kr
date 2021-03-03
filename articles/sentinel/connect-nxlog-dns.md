---
title: NXLog Windows DNS 로그 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: NXLog DNS 로그 데이터 커넥터를 사용 하 여 Windows DNS 이벤트를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Windows DNS 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745988"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>NXLog Windows DNS 로그를 Azure 센티널에 연결

> [!IMPORTANT]
> NXLog DNS 로그 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

[Nxlog DNS 로그](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) 커넥터를 사용 하면 모든 Windows DNS 서버 이벤트를 실시간으로 Azure 센티널로 쉽게 내보내 조직 전체에서 도메인 이름 서버 작업에 대 한 통찰력을 제공할 수 있습니다. 감사 및 분석 DNS 서버 이벤트를 실시간으로 수집 하 고 사용자 지정 필드를 사용 하 여 이벤트 보강을 지 원하는 [ETW (고성능 ETW(Windows용 이벤트 추적))](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) 를 사용 합니다. NXLog DNS 로그와 Azure 센티널 간의 통합은 REST API를 통해 촉진 됩니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-nxlog-dns-logs"></a>NXLog DNS 로그 구성 및 연결

NXLog를 구성 하 여 JSON 형식의 이벤트를 Azure 센티널로 직접 보낼 수 있습니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **NXLOG DNS 로그** 커넥터를 선택 합니다.

1. **커넥터 페이지 열기** 를 선택 합니다.

1. *Nxlog 사용자 가이드* 통합 항목에서 [센티널 Microsoft Azure](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) 의 단계별 지침에 따라 REST API를 통해 전달을 구성 합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 *DNS_Logs_CL* 테이블의 **사용자 지정 로그** 섹션 아래에 있는 **로그** 에 표시 됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 NXLog를 사용 하 여 Windows DNS 로그를 Azure 센티널에 수집 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
