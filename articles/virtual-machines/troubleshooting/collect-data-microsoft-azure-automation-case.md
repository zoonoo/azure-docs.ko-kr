---
title: Microsoft Azure Automation의 사례를 열 때 수집할 데이터 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure 지원 Azure Automation 대 한 사례를 열기 전에 수집 해야 하는 기본적인 정보 중 일부에 대해 설명 합니다.
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
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846681"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Microsoft Azure 자동화를 위해 사례를 열 때 수집할 데이터

이 문서에서는 Microsoft Azure 지원 Azure Automation 대 한 사례를 열기 전에 수집 해야 하는 기본적인 정보 중 일부에 대해 설명 합니다. 이 정보는 사례를 여는 데 필요 하지 않습니다. 그러나 Microsoft에서 문제를 더 신속 하 게 해결 하는 데 도움이 될 수 있습니다. 또한 사례를 연 후 지원 엔지니어가이 데이터를 묻는 메시지가 표시 될 수 있습니다.

## <a name="collect-more-information"></a>자세한 정보 수집

Microsoft Azure Automation 지원을 위한 사례를 열기 전에 다음 정보를 수집 하는 것이 좋습니다.

### <a name="basic-data-collection"></a>기본 데이터 수집

다음 기술 자료 문서에서 설명 하는 데이터를 수집 합니다.

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Azure Automation 스크립팅된 진단을 캡처하는 방법

## <a name="collect-data-depending-on-issue"></a>문제에 따라 데이터 수집
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Linux에서 업데이트 관리에 영향을 주는 문제

1. KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열 된 항목 외에 다음 로그 수집 도구를 실행 합니다.

   [OMS Linux 에이전트 로그 수집기](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 다음 폴더의 콘텐츠를 압축 한 다음 압축 된 파일을 Azure 지원으로 보냅니다.

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. OMS Linux 에이전트가 보고 하는 작업 영역 ID가 업데이트를 위해 모니터링 하는 작업 영역과 동일한 지 확인 합니다.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Windows의 업데이트 관리에 영향을 주는 문제

[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열 된 항목 외에 다음을 수행 합니다.

1. 다음 이벤트 로그를 .EVTX 형식으로 내보냅니다.

   * 시스템
   * 애플리케이션
   * 보안
   * Operations Manager
   * Microsoft SMA/Operational

2. 에이전트가 보고 하는 작업 영역 ID가 Windows 업데이트에 의해 모니터링 되는 작업 영역과 동일한 지 확인 합니다.

### <a name="for-issues-that-affect-a-job"></a>작업에 영향을 주는 문제

[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)에 나열 된 항목 외에 다음 정보를 수집 합니다.

1. 문제가 발생 한 작업에 대 한 **JobID** 번호를 수집 합니다.

   1. Azure Portal에서 **Automation 계정** 블레이드로 이동 합니다.
   2. 문제를 해결할 **Automation 계정을** 선택 합니다.
   3. **작업**을 선택 합니다.
   4. 문제를 해결 하려는 작업을 선택 합니다.
   5. **작업 요약**에서 **작업 ID** (GUID)를 찾습니다.

   ![작업 요약 창 내 작업 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. 계정 이름 수집:

   1. Azure Portal에서 **Automation 계정** 블레이드로 이동 합니다.
   2. 문제를 해결 하는 **Automation 계정의** 이름을 가져옵니다.

3. 실행 중인 스크립트의 샘플을 수집 합니다.

4. 로그 파일을 수집 합니다.

   1. Azure Portal에서 **Automation 계정** 블레이드로 이동 합니다.
   2. 문제를 해결할 **Automation 계정을** 선택 합니다.
   3. **작업**을 선택 합니다.
   4. 문제를 해결 하려는 작업을 선택 합니다.
   5. **모든 로그**를 선택 합니다.
   6. 결과 블레이드에서 데이터를 수집 합니다.

   ![모든 로그에 나열 된 데이터](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>모듈에 영향을 주는 문제

"기본 데이터 수집"에 있는 항목 외에 다음 정보를 수집 합니다.

* 문제를 재현할 수 있도록 다음 단계를 수행 합니다.
* 모든 오류 메시지의 스크린샷
* 현재 모듈 및 해당 버전 번호의 스크린샷

## <a name="next-steps"></a>다음 단계

이 문서의 어느 시점에서 든 도움이 필요한 경우 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에 게 문의 하세요.

또는 Azure 지원 인시던트를 파일에 제공 합니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.