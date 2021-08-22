---
title: Azure Sentinel에 Pulse Connect Secure 데이터 연결 | Microsoft Docs
description: Azure Sentinel에 Pulse Connect Secure 데이터를 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: 8eeea8f8f0410cb0c0d559cf1816e45e6c60c12e
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538331"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Azure Sentinel에 Pulse Connect Secure 연결

> [!IMPORTANT]
> Azure Sentinel의 Pulse Connect Secure 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/) 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Pulse Connect Secure 데이터 커넥터를 사용하면 Pulse Connect Secure 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Pulse Connect Secure와 Azure Sentinel 간의 통합은 Syslog를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Pulse Connect Secure 로그를 Syslog 에이전트에 전달  

Syslog 에이전트를 통해 Syslog 메시지를 Azure 작업 영역에 전달하도록 Pulse Connect Secure를 구성합니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭하고 **Pulse Connect Secure** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. **Pulse Connect Secure** 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 Syslog에 있는 Log Analytics에 표시됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Pulse Connect Secure를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.