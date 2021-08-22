---
title: Azure Sentinel에 CyberArk EPV 데이터 연결| Microsoft Docs
description: EPV(CyberArk Enterprise Password Vault) 데이터 커넥터를 사용하여 해당 로그를 Azure Sentinel로 가져오는 방법 알아보기. 통합 문서에서 CyberArk EPV 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 3f18481821361feff525e3357543036784b21495
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538466"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>CyberArk EPV를 Azure Sentinel에 연결합니다.

> [!IMPORTANT]
> 현재 Azure Sentinel의 CyberArk EPV 데이터 커넥터는 퍼블릭 미리 보기로 제공되고 있습니다. 해당 기능은 별도의 서비스 수준 규약 없이 이용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

CyberArk Syslog 커넥터를 사용하면 모든 CyberArk 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. CyberArk와 Azure Sentinel의 통합은 CEF 데이터 커넥터가 CyberArk Syslog 메시지를 올바르게 구문 분석하고 표시할 수 있도록 합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-cyberark-epv"></a>CyberArk EPV 구성 및 연결

저장소에 설치된 Log Analytics 에이전트를 통해 CyberArk EPV 로그를 Vault에서 rsyslog 또는 syslog-ng를 실행하는 Linux 기반 로그 전달 서버로 전송하고, 다음으로 Azure Sentinel로 내보냅니다. 로그 전달 서버가 없는 경우, [본 지침](connect-cef-agent.md)을 참조하여 실행합니다.

1. Azure Sentinel Portal에서 **데이터 커넥터** 를 클릭하고 **CyberArk EPV 이벤트(미리 보기)** 에서 **커넥터 페이지 열기** 를 선택합니다.

1. [CyberArk EPV 지침](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm)에 따라 로그 전달 서버에 syslog 데이터 보내기를 구성합니다.

1. [본 지침](connect-cef-verify.md)을 사용하여 연결의 유효성을 검사하고 데이터 수집을 확인합니다. 로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 **Azure Sentinel** 섹션에 있는 *CommonSecurityLog* 테이블의 **로그** 에 표시됩니다.

Log Analytics에서 CyberArk EPV 로그를 쿼리하려면 `CommonSecurityLog`쿼리 창 상단에 입력합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 CyberArk Enterprise Password Vault 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.