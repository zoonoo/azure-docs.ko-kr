---
title: Symantec VIP 데이터를 Azure Sentinel| Microsoft Docs에 연결
description: Symantec VIP 데이터를 Azure Sentinel에 연결하는 방법 알아보기.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090414"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Symantec VIP를 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Symantec VIP 데이터 커넥터는 현재 퍼블릭 미리 보기로 제공됩니다.
> 해당 기능은 별도의 Service Level Agreement(서비스 수준 규약) 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서는 [Symantec VIP](https://vip.symantec.com/) 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Symantec VIP 데이터 커넥터를 사용하면 Symantec VIP 로그를 Azure Sentinel에 쉽게 연결하여, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 기능을 개선할 수 있습니다. Symantec VIP와 Azure Sentinel 간의 통합에는 Syslog를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Symantec VIP 로그를 Syslog 에이전트로 전달  

Syslog 에이전트를 통해 Syslog 메시지를 Azure 작업 영역에 전달하도록 Symantec VIP를 구성합니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭하고 **Symantec VIP** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. **Symantec VIP** 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정된 후에는 Syslog 아래 Log Analytics에 데이터가 표시됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다. 

## <a name="next-steps"></a>다음 단계

이상 Symantec VIP 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.