---
title: Azure 센티널에 CyberArk EPV 데이터 연결 | Microsoft Docs
description: EPV (CyberArk Enterprise Password Vault) 데이터 커넥터를 사용 하 여 해당 로그를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 CyberArk EPV 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.openlocfilehash: c375595951eb760d5341db424c5572719b97046a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102981"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>CyberArk 엔터프라이즈 암호 자격 증명 모음 (EPV)을 Azure 센티널에 연결

> [!IMPORTANT]
> Azure 센티널의 EPV (CyberArk Enterprise Password Vault) 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 됩니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

CyberArk Syslog 커넥터를 사용 하면 모든 CyberArk 보안 솔루션 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. CyberArk와 Azure 센티널 간의 통합을 통해 CEF 데이터 커넥터를 사용 하 여 CyberArk Syslog 메시지를 올바르게 구문 분석 하 고 표시 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-cyberark-epv"></a>CyberArk EPV 구성 및 연결

CyberArk EPV 로그는 저장소에 설치 된 Log Analytics 에이전트를 사용 하 여 rsyslog 또는 syslog를 실행 하는 Linux 기반 로그 전달 서버에 전송 됩니다. 그러면 로그를 Azure 센티널로 내보냅니다. 이러한 로그 전달 서버가 없는 경우 [다음 지침](connect-cef-agent.md) 을 참조 하 여 실행 합니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **CyberArk (엔터프라이즈 암호 자격 증명 모음) 이벤트 (미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다** .

1. CyberArk EPV 지침에 따라 로그 전달 서버에 syslog 데이터 보내기를 구성 합니다.

1. [이러한 지침](connect-cef-verify.md)을 사용 하 여 연결의 유효성을 검사 하 고 데이터 수집을 확인 합니다. 로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 의 **Azure 센티널** 섹션 아래 *CommonSecurityLog* 테이블에 표시 됩니다.

Log Analytics에서 CyberArk EPV 로그를 쿼리하려면 `CommonSecurityLog` 쿼리 창의 맨 위에를 입력 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 CyberArk 엔터프라이즈 암호 자격 증명 모음 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
