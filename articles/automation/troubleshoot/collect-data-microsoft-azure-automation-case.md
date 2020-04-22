---
title: Microsoft Azure 자동화에 대한 사례를 열 때 수집할 데이터| 마이크로 소프트 문서
description: 이 문서에서는 Microsoft Azure 지원을 통해 Azure 자동화에 대한 사례를 열기 전에 수집해야 하는 몇 가지 정보를 설명합니다.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679414"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Automation에 대한 사례를 열 때 수집할 데이터

이 문서에서는 Microsoft Azure 지원을 통해 Azure 자동화에 대한 사례를 열기 전에 수집해야 하는 몇 가지 정보를 설명합니다. 이 정보는 케이스를 열 필요가 없습니다. 그러나 Microsoft가 문제를 보다 신속하게 해결하는 데 도움이 될 수 있습니다. 또한 서비스 케이스를 연 후 지원 엔지니어가 이 데이터를 요청받을 수도 있습니다.

## <a name="basic-data"></a>기본 데이터

기술 자료 문서 [4034605 - Azure 자동화 스크립트 진단을 캡처하는 방법에 설명된](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)기본 데이터를 수집합니다.

## <a name="data-for-update-management-issues-on-linux"></a>리눅스에서 업데이트 관리 문제에 대 한 데이터

1. KB [4034605에](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)나열된 항목 외에도 다음 로그 수집 도구를 실행합니다.

   [OMS 리눅스 에이전트 로그 수집기](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. **/var/opt/microsoft/omsagent/run/automationworker/폴더의** 내용을 압축 한 다음 압축 된 파일을 Azure 지원에 보냅니다.
 
3. Linux의 Log Analytics 에이전트가 보고하는 작업 영역의 ID가 업데이트에 대해 모니터링되는 작업 영역의 ID와 동일한지 확인합니다.

## <a name="data-for-update-management-issues-on-windows"></a>Windows에서 업데이트 관리 문제에 대한 데이터

1. [4034605에](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)나열된 항목에 대한 데이터를 수집합니다.

2. 다음 이벤트 로그를 EVTX 형식으로 내보내기:

   * 시스템
   * 애플리케이션
   * 보안
   * Operations Manager
   * 마이크로소프트-SMA/운영

3. 에이전트가 보고하는 작업 영역의 ID가 Windows Update에서 모니터링하는 작업 영역의 ID와 동일한지 확인합니다.

## <a name="data-for-job-issues"></a>작업 문제에 대한 데이터

1. [4034605에](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)나열된 항목에 대한 데이터를 수집합니다.

2. 문제가 있는 작업에 대한 작업 ID를 수집합니다.

   1. Azure 포털에서 자동화 **계정으로**이동합니다.
   2. 문제 해결 중인 자동화 계정을 선택하고 이름을 기록합니다.
   3. **작업**을 선택합니다.
   4. 문제 해결 중인 작업을 선택합니다.
   5. 작업 요약 창에서 **작업 ID에서**GUID 값을 찾습니다.

   ![작업 요약 창 내의 작업 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 실행 중인 스크립트의 샘플을 수집합니다.

4. 로그 파일 수집:

   1. Azure 포털에서 자동화 **계정으로**이동합니다.
   2. 문제 해결 중인 자동화 계정을 선택합니다.
   3. **작업**을 선택합니다.
   4. 문제 해결 중인 작업을 선택합니다.
   5. **모든 로그를 선택합니다.**
   6. 결과 창에서 데이터를 수집합니다.

   ![모든 로그 아래에 나열된 데이터](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>모듈 문제에 대한 데이터

기본 데이터 [항목](#basic-data)외에도 다음 정보를 수집합니다.

* 문제를 재현할 수 있도록 수행한 단계입니다.
* 오류 메시지의 스크린샷입니다.
* 현재 모듈 및 해당 버전 번호의 스크린샷입니다.

## <a name="next-steps"></a>다음 단계

도움이 더 필요한 경우:

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
