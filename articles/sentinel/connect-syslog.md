---
title: Azure 센티널 Preview에 Syslog 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Syslog 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: dad74410562aa54aeb61675e7dc1c0adccc44797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679217"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog를 사용 하 여 외부 솔루션 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Syslog를 지 원하는 온-프레미스 어플라이언스를 Azure 센티널에 연결할 수 있습니다. 어플라이언스와 Azure 센티널 사이에 Linux 컴퓨터를 기반으로 하는 에이전트를 사용 하 여이 작업을 수행 합니다. Linux 컴퓨터가 Azure에 있는 경우 어플라이언스 또는 응용 프로그램에서 Azure에 만든 전용 작업 영역으로 로그를 스트리밍하 고 연결할 수 있습니다. Linux 컴퓨터가 Azure에 없으면 어플라이언스의 로그를 Linux 용 에이전트를 설치 하는 전용 온-프레미스 VM 또는 컴퓨터로 스트리밍할 수 있습니다. 

> [!NOTE]
> 어플라이언스에서 Syslog CEF를 지 원하는 경우에는 연결이 더 완벽 하므로이 옵션을 선택 하 고 [CEF의 데이터 연결](connect-common-event-format.md)에 설명 된 지침을 따라야 합니다.

## <a name="how-it-works"></a>작동 방법

Syslog는 Linux에 공통되는 이벤트 로깅 프로토콜입니다. 애플리케이션은 로컬 컴퓨터에 저장되거나 Syslog 수집기에 배달될 수 있는 메시지를 전송합니다. Linux용 Log Analytics 에이전트를 설치하면 에이전트에 메시지를 전달하도록 로컬 Syslog 디먼이 구성됩니다. 그러면 에이전트는 레코드가 만들어진 Azure Monitor로 해당 메시지를 보냅니다.

자세한 내용은 [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/platform/data-sources-syslog.md)을 참조 하세요.

> [!NOTE]
> 에이전트는 여러 원본에서 로그를 수집할 수 있지만 전용 프록시 컴퓨터에 설치 해야 합니다.

## <a name="connect-your-syslog-appliance"></a>Syslog 어플라이언스 연결

1. Azure 센티널 포털에서 **데이터 커넥터** 를 선택 하 고 테이블에서 **syslog** 줄을 선택한 다음 오른쪽의 syslog 창에서 **커넥터 페이지 열기**를 클릭 합니다.
2. Linux 컴퓨터가 Azure 내에 있는 경우 **Azure Linux 가상 머신에서 에이전트 다운로드 및 설치**를 선택 합니다. 가상 컴퓨터 창에서 에이전트를 설치할 컴퓨터를 선택 하 고 맨 위에 있는 **연결** 을 클릭 합니다.
1. Azure 내에 없는 Linux 컴퓨터의 경우 **linux에서 azure가 아닌 컴퓨터에 에이전트 다운로드 및 설치**를 선택 합니다. **직접 에이전트** 창에서 **Linux 용 에이전트 다운로드 및** 등록 아래에 있는 명령을 복사 하 고 컴퓨터에서 실행 합니다. 
   > [!NOTE]
   > 조직의 보안 정책에 따라 컴퓨터의 보안을 구성 해야 합니다. 예를 들어 회사 네트워크 보안 정책에 맞게 네트워크를 구성 하 고, 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다. 

1. Syslog 커넥터 설정 창에서 **연결할 로그 구성** 에서 다음 지침을 따릅니다.
    1. 링크를 클릭 하 여 **작업 영역 고급 설정 구성을 엽니다**. 
    1. **데이터**, **Syslog**를 차례로 선택 합니다.
    1. 그런 다음 테이블에서 Syslog를 수집할 기능을 설정 합니다. Syslog 어플라이언스의 로그 헤더에 포함 된 기능을 추가 하거나 선택 해야 합니다. Syslog의 syslog 어플라이언스에서 syslog-d 폴더에 있는/etc/rsyslog.d/security-config-omsagent.conf 및/etc/syslog-ng/security-config-omsagent.conf. 아래의 r-Syslog에서이 구성을 볼 수 있습니다. 
       > [!NOTE]
       > **내 컴퓨터에 아래 구성 적용**확인란을 선택 하면이 작업 영역에 연결 된 모든 Linux 컴퓨터에이 구성이 적용 됩니다. 의 Syslog 컴퓨터에서이 구성을 볼 수 있습니다. 
1. **구성 블레이드를 열려면 여기를**클릭 하세요 .를 클릭 합니다.
1. **데이터** , **Syslog**를 차례로 선택 합니다.
   - Syslog를 통해 전송 하는 각 기능이 테이블에 있는지 확인 합니다. 각 기능에 대해 모니터링 하 고 심각도를 설정 합니다. **적용**을 클릭합니다.
1. Syslog 컴퓨터에서 해당 시설을 전송 하 고 있는지 확인 합니다. 

1. Syslog 로그에 대해 Log Analytics에서 관련 스키마를 사용 하려면 **syslog**를 검색 합니다.
1. [Azure Monitor 로그 쿼리의 함수를 사용 하 여](../azure-monitor/log-query/functions.md) Syslog 메시지를 구문 분석 한 다음 새 Log Analytics 함수로 저장 한 다음 함수를 새 데이터 형식으로 사용 하는 방법에 설명 된 Kusto 함수를 사용할 수 있습니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Syslog 온-프레미스 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats.md)시작 합니다.
