---
title: Sophos XG 방화벽 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Sophos XG 방화벽 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.openlocfilehash: b48773272e050b47af73b197d6f1c8156318fd71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099343"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Sophos XG 방화벽을 Azure 센티널에 연결

> [!IMPORTANT]
> Azure 센티널의 Sophos XG 방화벽 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Sophos XG 방화벽](https://www.sophos.com/products/next-gen-firewall.aspx) 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Sophos XG 방화벽 데이터 커넥터를 사용 하 여 Sophos XG 방화벽 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Sophos XG 방화벽과 Azure 센티널 간의 통합은 Syslog를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Sophos XG 방화벽 로그를 Syslog 에이전트로 전달  

Syslog 에이전트를 통해 Azure 작업 영역에 Syslog 메시지를 전달 하도록 Sophos XG 방화벽을 구성 합니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **Sophos xg 방화벽** 커넥터를 선택 합니다.

1. **커넥터 페이지 열기**를 선택 합니다.

1. **Sophos XG 방화벽** 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 된 후에는 해당 데이터가 Syslog 아래 Log Analytics에 표시 됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Sophos XG 방화벽을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.