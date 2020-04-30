---
title: Microsoft Azure Automation의 사례를 열 때 수집할 데이터 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure 지원 Azure Automation 대 한 사례를 열기 전에 수집 해야 하는 정보 중 일부에 대해 설명 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679414"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Automation에 대한 사례를 열 때 수집할 데이터

이 문서에서는 Microsoft Azure 지원 Azure Automation 대 한 사례를 열기 전에 수집 해야 하는 정보 중 일부에 대해 설명 합니다. 이 정보는 사례를 여는 데 필요 하지 않습니다. 그러나 Microsoft에서 문제를 더 신속 하 게 해결 하는 데 도움이 될 수 있습니다. 또한 사례를 연 후 지원 엔지니어가이 데이터를 묻는 메시지가 표시 될 수 있습니다.

## <a name="basic-data"></a>기본 데이터

기술 자료 문서 [4034605-Azure Automation 스크립팅된 진단을 캡처하는 방법](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)문서에서 설명 하는 기본 데이터를 수집 합니다.

## <a name="data-for-update-management-issues-on-linux"></a>Linux에서 업데이트 관리 문제에 대 한 데이터

1. KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열 된 항목 외에 다음 로그 수집 도구를 실행 합니다.

   [OMS Linux 에이전트 로그 수집기](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. **/Var/opt/microsoft/omsagent/run/automationworker/** 폴더의 콘텐츠를 압축 한 다음 압축 된 파일을 Azure 지원으로 보냅니다.
 
3. Linux 용 Log Analytics 에이전트가 보고 하는 작업 영역의 ID가 업데이트를 모니터링 하는 작업 영역의 ID와 동일한 지 확인 합니다.

## <a name="data-for-update-management-issues-on-windows"></a>Windows에서 업데이트 관리 문제에 대 한 데이터

1. [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열 된 항목에 대 한 데이터를 수집 합니다.

2. 다음 이벤트 로그를 .EVTX 형식으로 내보냅니다.

   * System (시스템)
   * 애플리케이션
   * 보안
   * Operations Manager
   * Microsoft SMA/Operational

3. 에이전트가 보고 하는 작업 영역의 ID가 Windows 업데이트에 의해 모니터링 되는 작업 영역의 ID와 동일한 지 확인 합니다.

## <a name="data-for-job-issues"></a>작업 문제에 대 한 데이터

1. [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열 된 항목에 대 한 데이터를 수집 합니다.

2. 문제가 있는 작업에 대 한 작업 ID를 수집 합니다.

   1. Azure Portal에서 **Automation 계정**으로 이동 합니다.
   2. 문제를 해결할 Automation 계정을 선택 하 고 이름을 확인 합니다.
   3. **작업**을 선택 합니다.
   4. 문제를 해결 하려는 작업을 선택 합니다.
   5. 작업 요약 창의 **작업 ID**에서 GUID 값을 찾습니다.

   ![작업 요약 창 내 작업 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 실행 중인 스크립트의 샘플을 수집 합니다.

4. 로그 파일을 수집 합니다.

   1. Azure Portal에서 **Automation 계정**으로 이동 합니다.
   2. 문제를 해결할 Automation 계정을 선택 합니다.
   3. **작업**을 선택 합니다.
   4. 문제를 해결 하려는 작업을 선택 합니다.
   5. **모든 로그**를 선택 합니다.
   6. 결과 창에서 데이터를 수집 합니다.

   ![모든 로그에 나열 된 데이터](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>모듈 문제에 대 한 데이터

[기본 데이터 항목](#basic-data)외에 다음 정보를 수집 합니다.

* 문제를 재현할 수 있도록 수행한 단계
* 모든 오류 메시지의 스크린샷
* 현재 모듈 및 해당 버전 번호의 스크린샷

## <a name="next-steps"></a>다음 단계

도움이 더 필요한 경우:

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* Azure 커뮤니티 [@AzureSupport](https://twitter.com/azuresupport)를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정인를 사용 하 여 연결 하세요.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 **지원 받기**를 선택 합니다.
