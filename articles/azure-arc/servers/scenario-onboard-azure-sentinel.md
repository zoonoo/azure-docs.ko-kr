---
title: Azure Arc 사용 서버를 Azure 센티널에 등록
description: Azure Arc 사용 서버를 Azure 센티널에 추가 하 고 해당 보안 상태를 사전에 모니터링 하는 방법에 대해 알아봅니다.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 60018e710613a27bcb5c00f38ee90be6ff46c61d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94811053"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 사용 서버를 Azure 센티널에 등록

이 문서는 azure Arc 사용 서버를 [Azure 센티널](../../sentinel/overview.md) 에 등록 하 고 보안 관련 이벤트 수집을 시작 하는 데 도움을 주기 위해 작성 되었습니다. Azure 센티널은 엔터프라이즈 전체에서 경고 검색, 위협 가시성, 자동 관리 구하기 및 위협 대응을 위한 단일 솔루션을 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음 요구 사항을 충족 하는지 확인 합니다.

- [Log Analytics 작업 영역](../../azure-monitor/platform/data-platform-logs.md) Log Analytics 작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../../azure-monitor/platform/design-logs-deployment.md)을 참조하세요.

- [구독에서 사용할 수 있는](../../sentinel/quickstart-onboard.md)Azure 센티널.

- 사용자의 컴퓨터 또는 서버가 Azure Arc 사용 서버에 연결 되어 있습니다.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 사용 서버를 Azure 센티널에 등록

Azure 센티널은 Microsoft 솔루션에 대 한 다양 한 커넥터와 함께 제공 되며, 기본적으로 제공 되며 실시간 통합을 제공 합니다. 실제 및 가상 머신의 경우 로그를 수집하고 Azure Sentinel에 전달하는 Log Analytics 에이전트를 설치할 수 있습니다. Arc 사용 서버는 다음 방법을 사용 하 여 Log Analytics 에이전트 배포를 지원 합니다.

- VM 확장 프레임 워크 사용.

    Azure Arc 사용 서버에서이 기능을 사용 하면 Log Analytics 에이전트 VM 확장을 비 Azure Windows 및/또는 Linux 서버에 배포할 수 있습니다. 가상 컴퓨터 또는 Arc 사용 서버에서 관리 하는 서버에서 다음 방법을 사용 하 여 VM 확장을 관리할 수 있습니다.

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [리소스 관리자 템플릿](manage-vm-extensions-template.md)

- Azure Policy 사용.

    이 방법을 사용 하 여 [Log Analytics 에이전트를 Linux 또는 Windows Azure Arc 컴퓨터에 배포](../../governance/policy/samples/built-in-policies.md#monitoring) Azure Policy 기본 제공 정책을 사용 하 여 Arc 사용 서버에 Log Analytics 에이전트가 설치 되어 있는 경우 감사 합니다. 에이전트가 설치 되지 않은 경우에는 재구성 작업을 사용 하 여 에이전트를 자동으로 배포 합니다. 또는 VM용 Azure Monitor를 사용 하 여 컴퓨터를 모니터링 하려는 경우에는 VM용 Azure Monitor 이니셔티브 [사용](../../governance/policy/samples/built-in-initiatives.md#monitoring) 을 대신 사용 하 여 Log Analytics 에이전트를 설치 하 고 구성 합니다.

Azure Policy를 사용 하 여 Windows 또는 Linux 용 Log Analytics 에이전트를 설치 하는 것이 좋습니다.

Arc 사용 서버가 연결 된 후 데이터는 Azure 센티널로의 스트리밍을 시작 하 고 작업을 시작할 준비가 된 것입니다. [기본 제공 통합 문서](../../sentinel/quickstart-get-visibility.md)에서 로그를 확인하고 Log Analytics에서 쿼리를 작성하여 [데이터를 조사](../../sentinel/tutorial-investigate-cases.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Sentinel을 사용하여 위협 검색](../../sentinel/tutorial-detect-threats-built-in.md)을 시작합니다.