---
title: Microsoft Azure 자동화에 대한 사례를 열 때 수집할 데이터| 마이크로 소프트 문서
description: 이 문서에서는 Microsoft Azure 지원을 통해 Azure 자동화에 대한 사례를 열기 전에 수집해야 하는 몇 가지 기본 정보를 설명합니다.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849380"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Automation에 대한 사례를 열 때 수집할 데이터

이 문서에서는 Microsoft Azure 지원을 통해 Azure 자동화에 대한 사례를 열기 전에 수집해야 하는 몇 가지 기본 정보를 설명합니다. 이 정보는 케이스를 열 필요가 없습니다. 그러나 Microsoft가 문제를 보다 신속하게 해결하는 데 도움이 될 수 있습니다. 또한 서비스 케이스를 연 후 지원 엔지니어가 이 데이터를 요청받을 수도 있습니다.

## <a name="collect-more-information"></a>추가 정보 수집

Microsoft Azure 자동화 지원에 대한 서비스 케이스를 열기 전에 다음 정보를 수집하는 것이 좋습니다.

### <a name="basic-data-collection"></a>기본 데이터 수집

다음 기술 자료 문서에 설명된 데이터를 수집합니다.

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Azure 자동화 스크립트 진단을 캡처하는 방법

## <a name="collect-data-depending-on-issue"></a>문제에 따라 데이터 수집
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Linux의 업데이트 관리에 영향을 주는 문제에 대한 정보

1. KB [4034605에](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)나열된 항목 외에도 다음 로그 수집 도구를 실행합니다.

   [OMS 리눅스 에이전트 로그 수집기](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 다음 폴더의 내용을 압축한 다음 압축된 파일을 Azure 지원으로 보냅니다.

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. OMS Linux 에이전트가 보고하는 작업 영역 ID가 업데이트에 대해 모니터링되는 작업 영역과 동일한지 확인합니다.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Windows에서 업데이트 관리에 영향을 주는 문제의 경우

[4034605에](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)나열된 항목 외에도 다음을 수행합니다.

1. 다음 이벤트 로그를 EVTX 형식으로 내보내기:

   * 시스템
   * 애플리케이션
   * 보안
   * Operations Manager
   * 마이크로소프트-SMA/운영

2. 에이전트가 보고하는 작업 영역 ID가 Windows 업데이트에서 모니터링하는 작업 영역과 동일한지 확인합니다.

### <a name="for-issues-that-affect-a-job"></a>작업에 영향을 주는 문제의 경우

[4034605에](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)나열된 항목 외에도 다음 정보를 수집합니다.

1. **JobID** 번호(문제가 발생한 작업의 경우)를 수집합니다.

   1. Azure 포털에서 **자동화 계정** 블레이드로 이동합니다.
   2. 문제 해결 중인 **자동화 계정을** 선택합니다.
   3. **작업**을 선택합니다.
   4. 문제 해결 중인 작업을 선택합니다.
   5. **작업 요약에서**작업 ID(GUID)를 찾습니다. **Job ID**

   ![작업 요약 창 내의 작업 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. 계정 이름 수집:

   1. Azure 포털에서 **자동화 계정** 블레이드로 이동합니다.
   2. 문제 해결 중이되는 **자동화 계정의** 이름을 가져옵니다.

3. 실행 중인 스크립트의 샘플을 수집합니다.

4. 로그 파일 수집:

   1. Azure 포털에서 **자동화 계정** 블레이드로 이동합니다.
   2. 문제 해결 중인 **자동화 계정을** 선택합니다.
   3. **작업**을 선택합니다.
   4. 문제 해결 중인 작업을 선택합니다.
   5. **모든 로그를 선택합니다.**
   6. 결과 블레이드에서 데이터를 수집합니다.

   ![모든 로그 아래에 나열된 데이터](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>모듈에 영향을 주는 문제의 경우

"기본 데이터 수집"의 항목 외에도 다음 정보를 수집합니다.

* 문제를 재현할 수 있도록 수행한 단계입니다.
* 오류 메시지의 스크린샷입니다.
* 현재 모듈 및 해당 버전 번호의 스크린샷입니다.

## <a name="next-steps"></a>다음 단계

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의 Azure 전문가에게 문의하십시오.](https://azure.microsoft.com/support/forums/)

또는 Azure 지원 인시던트를 파일로 제출합니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
