---
title: Windows 관리 센터에서 Azure에 하이브리드 컴퓨터 연결
description: 이 문서에서는 Windows 관리 센터에서 Azure Arc 사용 서버를 사용 하 여 에이전트를 설치 하 고 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: d47e3dd4434d719f890b64e4cdfb12a189c0632a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133709"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Windows 관리 센터에서 Azure에 하이브리드 컴퓨터 연결

일련의 단계를 수동으로 수행 하 여 사용자 환경에서 하나 이상의 Windows 컴퓨터에 대해 Azure Arc 사용 서버를 사용 하도록 설정할 수 있습니다. 또는 [Windows 관리 센터](/windows-server/manage/windows-admin-center/understand/what-is) 를 사용 하 여 연결 된 컴퓨터 에이전트를 배포 하 고이 도구 외부에서 단계를 수행 하지 않고도 온-프레미스 서버를 등록할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Arc 사용 서버- [필수 구성 요소](agent-overview.md#prerequisites) 를 검토 하 고 구독, Azure 계정 및 리소스가 요구 사항을 충족 하는지 확인 합니다.

* Windows 관리 센터- [Azure 통합 ](/windows-server/manage/windows-admin-center/azure/azure-integration)을 배포 및 구성 하기 위한 [환경을 준비](/windows-server/manage/windows-admin-center/deploy/prepare-environment) 하기 위한 요구 사항을 검토 합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

* 관리 하려는 대상 Windows 서버는 Azure에 액세스할 수 있도록 인터넷에 연결 되어 있어야 합니다.

### <a name="security"></a>보안

이 배포 방법을 사용 하려면 에이전트를 설치 하 고 구성 하려면 대상 Windows 컴퓨터 또는 서버에 대 한 관리자 권한이 있어야 합니다. 또한 [**게이트웨이 사용자**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) 역할의 구성원 이어야 합니다.

## <a name="deploy"></a>배포

Arc 사용 서버를 사용 하 여 Windows server를 구성 하려면 다음 단계를 수행 합니다.

1. Windows 관리 센터에 로그인 합니다.

1. **개요** 페이지의 연결 목록에 있는 연결 된 Windows 서버 목록에서 목록에 연결할 서버를 선택 합니다.

1. 왼쪽 창에서 **Azure hybrid services** 를 선택 합니다.

1. **Azure hybrid services** 페이지에서 **azure 서비스 검색** 을 선택 합니다.

1. Azure **서비스 검색** 페이지의 azure **정책 및 솔루션을 활용 하 여 azure Arc로 서버 관리** 에서 **설정** 을 선택 합니다.

1. **서버에 대 한 Settings\Azure Arc** 페이지에서 메시지가 표시 되 면 Azure에 인증 한 다음 **시작** 을 선택 합니다.

1. **Azure에 서버 연결** 페이지에서 다음을 제공 합니다.

    1. **Azure 구독** 드롭다운 목록에서 azure 구독을 선택 합니다.
    1. **리소스 그룹** 에 대해 **새로** 만들기를 선택 하 여 새 리소스 그룹을 만들거나 **리소스 그룹** 드롭다운 목록에서 컴퓨터를 등록 하 고 관리 하는 기존 리소스 그룹을 선택 합니다.
    1. **지역** 드롭다운 목록에서 서버 메타 데이터를 저장할 Azure 지역을 선택 합니다.
    1. 컴퓨터 또는 서버가 인터넷에 연결 하기 위해 프록시 서버를 통해 통신 하는 경우 **프록시 서버 사용** 옵션을 선택 합니다. 컴퓨터에서 프록시 서버와 통신 하는 데 사용할 프록시 서버 IP 주소, 이름 및 포트 번호를 지정 합니다.

1. **설정** 을 선택 하 여 Azure Arc 사용 서버를 사용 하는 Windows server 구성을 진행 합니다.

Windows server는 Azure에 연결 하 고, 연결 된 컴퓨터 에이전트를 다운로드 하 고, 설치 하 고, Azure Arc 사용 서버에 등록 합니다. 진행률을 추적 하려면 메뉴에서 **알림** 을 선택 합니다.

연결 된 컴퓨터 에이전트의 설치를 확인 하려면 Windows 관리 센터의 왼쪽 창에서 [**이벤트**](/windows-server/manage/windows-admin-center/use/manage-servers#events) 를 선택 하 여 응용 프로그램 이벤트 로그의 *msiinstaller* 이벤트를 검토 합니다.

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 사용 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://portal.azure.com)에서 머신을 확인합니다.

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="머신 연결 성공" border="false":::

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [연결 된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

* [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. Windows 및 Linux 용 Log Analytics 에이전트는 운영 체제 및 워크 로드 모니터링 데이터를 수집 하거나, 자동화 runbook 또는 업데이트 관리 같은 기능을 사용 하 여 관리 하거나, [Azure Security Center](../../security-center/security-center-intro.md)같은 다른 Azure 서비스를 사용 하려는 경우에 필요 합니다.