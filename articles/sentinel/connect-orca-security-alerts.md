---
title: Orca.exe 보안 경고를 Azure 센티널에 연결 | Microsoft Docs
description: Orca.exe 보안 경고 데이터를 Azure 센티널에 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 735f4b447d7258a9b444f3b75a6537dec20b1307
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076270"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Orca.exe 보안 경고를 Azure 센티널에 연결 

> [!IMPORTANT]
> Azure 센티널의 Orca.exe 보안 경고 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Orca.exe 보안 경고 커넥터를 사용 하 여 [orca.exe 경고](https://orca.security/) 보안 솔루션 경고를 Azure 센티널로 쉽게 가져올 수 있습니다. 따라서 통합 문서에서 볼 수 있으며, 사용자 지정 경고를 만들고 사용 하 여 조사를 개선할 수 있습니다. Orca.exe 보안 경고와 Azure 센티널 간의 통합은 REST API를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-orca-security-alerts"></a>Orca.exe 보안 경고 구성 및 연결

Orca.exe 보안 경고는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **orca.exe 보안 경고** 를 선택한 다음 **커넥터 페이지를 엽니다**.

2. https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integrationOrca.exe 플랫폼에서 통합을 완료 하려면를 참조 하십시오.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **OrcaAlerts_CL** 테이블의 **customlogs** 아래 Log Analytics에 나타납니다.
Orca.exe 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면를 검색 `OrcaAlerts_CL` 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Orca.exe 보안 경고를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

