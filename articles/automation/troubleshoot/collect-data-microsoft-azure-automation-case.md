---
title: Microsoft Azure Automation에 대한 사례를 열 때 수집할 데이터 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure 지원을 통해 Azure Automation 관련 사례를 열기 전에 수집할 정보에 대해 설명합니다.
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
ms.openlocfilehash: d2f97d0b889186324aef9613847e3eddbfe1eb02
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684851"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Automation에 대한 사례를 열 때 수집할 데이터

이 문서에서는 Microsoft Azure 지원을 통해 Azure Automation 관련 사례를 열기 전에 수집해야 할 정보에 대해 설명합니다. 이 정보가 사례를 여는 데는 필요하지 않습니다. 그러나 Microsoft에서 문제를 더 신속하게 해결하는 데 도움이 될 수 있습니다. 또한 사례를 연 후 지원 엔지니어가 이 데이터를 요청할 수도 있습니다.

## <a name="basic-data"></a>기본 데이터

기술 자료 문서 [4034605 - Azure Automation 스크립팅 진단을 캡처하는 방법](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 설명된 기본 데이터를 수집합니다.

## <a name="data-for-update-management-issues-on-linux"></a>Linux의 업데이트 관리 이슈 관련 데이터

1. KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열된 항목 외에 다음 로그 수집 도구를 실행합니다.

   [OMS Linux 에이전트 로그 수집기](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. **/var/opt/microsoft/omsagent/run/automationworker/** 폴더의 콘텐츠를 압축한 후 압축한 파일을 Azure 지원 서비스로 보냅니다.
 
3. Linux용 Log Analytics 에이전트가 보고하는 작업 영역의 ID가 업데이트를 모니터링하는 작업 영역의 ID와 동일한지 확인합니다.

## <a name="data-for-update-management-issues-on-windows"></a>Windows의 업데이트 관리 이슈 관련 데이터

1. [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열된 항목에 대한 데이터를 수집합니다.

2. 다음 이벤트 로그를 EVTX 형식으로 내보냅니다.

   * 시스템
   * 애플리케이션
   * 보안
   * Operations Manager
   * Microsoft SMA/Operational

3. 에이전트가 보고하는 작업 영역의 ID가 업데이트를 Windows 업데이트에서 모니터링하는 작업 영역의 ID와 동일한지 확인합니다.

## <a name="data-for-job-issues"></a>작업 이슈에 대한 데이터

1. [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열된 항목에 대한 데이터를 수집합니다.

2. 이슈가 있는 작업의 작업 ID를 수집합니다.

   1. Azure Portal에서 **Automation 계정**으로 이동합니다.
   2. 문제를 해결할 Automation 계정을 선택하고 이름을 적어 둡니다.
   3. **작업**을 선택합니다.
   4. 문제를 해결하려는 작업을 선택합니다.
   5. 작업 요약 창의 **작업 ID**에서 GUID 값을 찾습니다.

   ![작업 요약 창 내 작업 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 실행 중인 스크립트의 샘플을 수집합니다.

4. 다음과 같이 로그 파일을 수집합니다.

   1. Azure Portal에서 **Automation 계정**으로 이동합니다.
   2. 문제를 해결할 Automation 계정을 선택합니다.
   3. **작업**을 선택합니다.
   4. 문제를 해결하려는 작업을 선택합니다.
   5. **모든 로그**를 선택합니다.
   6. 결과 창에서 데이터를 수집합니다.

   ![모든 로그에 나열된 데이터](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>모듈 이슈에 대한 데이터

[기본 데이터 항목](#basic-data) 외에 다음 정보를 수집합니다.

* 수행한 단계(문제 재현에 필요)
* 모든 오류 메시지의 스크린샷
* 현재 모듈 및 해당 버전 번호의 스크린샷

## <a name="next-steps"></a>다음 단계

추가 도움이 필요하면 다음을 수행합니다.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
