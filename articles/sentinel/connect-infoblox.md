---
title: Azure Sentinel에 Infoblox NIOS(네트워크 ID 운영 체제) 데이터 연결| Microsoft Docs
description: Azure Sentinel에 Infoblox NIOS(네트워크 ID 운영 체제) 데이터를 연결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 28abb9f09e3bca2522b959c6a9b890de5320b17a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567450"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Infoblox NIOS을 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Infoblox NIOS 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Sentinel에 [Infoblox NIOS(네트워크 ID 운영 체제) 어플라이언스](https://www.infoblox.com/glossary/network-identity-operating-system-nios/)를 연결하는 방법을 설명합니다. Infoblox NIOS 데이터 커넥터를 사용하면 Infoblox 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Infoblox NIOS와 Azure Sentinel간 통합은 Syslog를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Syslog 에이전트에 Infoblox 로그 전달  

Syslog 에이전트를 통해 Azure Sentinel 작업 영역에 Syslog 메시지를 전달하도록 Infoblox를 구성합니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭하고 **Infoblox NIOS** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. **Infoblox NIOS** 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정된 후에는 Syslog 아래 Log Analytics에 데이터가 표시됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Infoblox NIOS를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.