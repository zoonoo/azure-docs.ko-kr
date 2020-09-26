---
title: Azure Arc 사용 서버 VM 확장 문제 해결
description: 이 문서에서는 Azure Arc 사용 서버에서 발생 하는 Azure VM 확장 문제를 해결 하 고 문제를 해결 하는 방법을 설명 합니다.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: ffd7db5ff7da3d7f60762117f80d7b9b5af6f646
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347459"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Arc 사용 서버 VM 확장 문제 해결

이 문서에서는 Arc 사용 서버에서 Azure VM 확장을 배포 또는 제거 하는 동안 발생할 수 있는 문제를 해결 하 고 해결 하는 방법에 대 한 정보를 제공 합니다. 일반 정보는 [AZURE VM 확장 관리 및 사용](./manage-vm-extensions.md)을 참조 하세요.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

확장 배포 상태에 대 한 데이터는 Azure Portal에서 검색할 수 있습니다.

다음 문제 해결 단계는 모든 VM 확장에 적용됩니다.

1. 게스트 에이전트 로그를 확인 하려면 Windows 용으로 확장이 프로 비전 되었을 때의 작업 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` 및의 Linux에 대 한 작업을 확인 하세요 `/var/lib/GuestConfig/ext_mgr_logs` .

2. Windows에 대 한 자세한 내용은 특정 확장에 대 한 확장 로그를 확인 합니다 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . 확장 출력은에서 Linux에 설치 된 각 확장에 대 한 파일에 기록 됩니다 `/var/lib/GuestConfig/extension_logs` .

3. 오류 코드, 알려진 문제 등에 대 한 확장 관련 설명서 문제 해결 섹션을 확인 합니다. 각 확장에 대 한 추가 문제 해결 정보는 확장에 대 한 개요의 **문제 해결 및 지원** 섹션에서 찾을 수 있습니다. 여기에는 로그에 기록 된 오류 코드에 대 한 설명이 포함 됩니다. 확장 문서는 [extensions 테이블](manage-vm-extensions.md#extensions)에 연결 되어 있습니다.

4. 시스템 로그를 확인합니다. 단독 패키지 관리자 액세스가 필요한 다른 애플리케이션의 장기 실행 설치 등 확장을 방해할 수 있는 다른 작업을 확인합니다.

## <a name="troubleshooting-specific-extension-scenarios"></a>특정 확장 시나리오 문제 해결

### <a name="vm-insights"></a>VM 인사이트

- Azure Arc 사용 서버에 대해 VM Insights를 사용 하도록 설정 하면 종속성 및 Log Analytics 에이전트가 설치 됩니다. 저속 컴퓨터 또는 네트워크 연결 속도가 느려지는 경우 설치 프로세스 중 시간 초과가 표시 될 수 있습니다. Microsoft는이 문제를 개선 하는 데 도움이 되도록 연결 된 컴퓨터 에이전트에서이 문제를 해결 하는 단계를 수행 합니다. 중간에 설치를 다시 시도 하면 성공할 수 있습니다.

### <a name="log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트

- Log Analytics 에이전트 버전 1.13.9 (해당 확장 버전은 1.13.15)이 (가) Azure Arc 사용 서버의 리소스 ID로 업로드 된 데이터를 올바르게 표시 하지 않습니다. 로그가 서비스로 전송 되기는 하지만, **로그** 또는 **정보**를 선택한 후 선택한 사용 가능한 서버에서 데이터를 보려고 하면 데이터가 반환 되지 않습니다. Azure Monitor 로그 또는 작업 영역으로 범위가 지정 된 VM용 Azure Monitor에서 쿼리를 실행 하 여 해당 데이터를 볼 수 있습니다.

- 일부 배포판은 Linux 용 Log Analytics agent에서 현재 지원 되지 않습니다. 에이전트를 설치 하려면 Python 2를 포함 하 여 추가 종속성을 설치 해야 합니다. [여기](../../azure-monitor/platform/agents-overview.md#supported-operating-systems)에서 지원 매트릭스 및 필수 구성 요소를 검토 합니다.

- 상태 메시지의 오류 코드 52는 종속성이 누락 되었음을 나타냅니다. 누락 된 종속성에 대 한 자세한 내용은 출력 및 로그를 확인 하십시오.

- 설치에 실패 하는 경우 확장에 대 한 개요에서 **문제 해결 및 지원** 섹션을 검토 합니다. 대부분의 경우 상태 메시지에는 오류 코드가 포함 되어 있습니다. Linux 용 Log Analytics 에이전트의 경우이 VM 확장에 대 한 일반 문제 해결 정보와 함께 상태 메시지가 [여기](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)에 설명 되어 있습니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

- [Microsoft Q&A](/answers/topics/azure-arc.html)를 통해 Azure 전문가 로부터 답변을 받으세요.

- 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.

- Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기**를 선택합니다.
