---
title: 비 Azure 컴퓨터를 Azure Security Center에 연결
description: 비 Azure 컴퓨터를 Security Center에 연결하는 방법 알아보기
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6f2889c298f525e1babf80f86d4ae140ef2ce96f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448950"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>비 Azure 컴퓨터를 Security Center에 연결

Security Center에서 비 Azure 컴퓨터의 보안 태세를 모니터링할 수는 있지만, 그러려면 먼저 이러한 리소스를 온보딩해야 합니다. 아래와 같이 **시작** 또는 **인벤토리** 페이지에서 비 Azure 컴퓨터를 추가할 수 있습니다.

## <a name="add-non-azure-computers"></a>비 Azure 컴퓨터 추가 

1. Security Center의 메뉴에서 **시작** 페이지를 엽니다.
1. **시작하기** 탭을 선택합니다.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="[시작] 페이지의 [시작] 탭" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. **비Azure 서버 추가**에서 **구성**을 선택합니다.

    > [!TIP]
    > **인벤토리** 페이지의 **비 Azure 서버 추가** 단추에서 [컴퓨터 추가]를 열 수도 있습니다.

    Log Analytics 작업 영역 목록이 표시됩니다. 이 목록에는 자동 프로비저닝을 사용하는 경우 Security Center에서 자동으로 생성되는 기본 작업 영역이 포함됩니다(해당하는 경우). 이 작업 영역이나 사용할 다른 작업 영역을 선택합니다.

    컴퓨터를 기존 작업 영역에 추가하거나 새 작업 영역을 만들 수 있습니다. 

1. 선택 사항으로 새 작업 영역을 만들려면 **새 작업 영역 만들기**를 선택합니다.

1. 작업 영역 목록에서 관련 작업 영역에 대한 **서버 추가**를 선택합니다.
    **에이전트 관리** 페이지가 표시됩니다.

    이 페이지에서, 온보딩하려는 컴퓨터의 종류에 따라 아래의 관련 절차를 선택합니다.

    - [Azure Stack VM 온보딩](#onboard-your-azure-stack-vms)
    - [Linux 컴퓨터 온보딩](#onboard-your-linux-machines)
    - [Windows 컴퓨터 온보딩](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Azure Stack VM 온보딩
Azure Stack VM을 추가하려면 **에이전트 관리** 페이지의 정보가 필요하며, Azure Stack에서 실행되는 가상 머신에 **Azure Monitor, Update 및 Configuration Management** 가상 머신 확장을 구성해야 합니다.
1. **에이전트 관리** 페이지에서 **작업 영역 ID** 및 **기본 키**를 복사하여 메모장에 붙여넣습니다.
1. **Azure Stack** 포털에 로그인하여 **가상 머신** 페이지를 엽니다.
1. Security Center로 보호하려는 가상 머신을 선택합니다.
    >[!TIP]
    > Azure Stack에서 가상 머신을 만드는 방법에 대한 자세한 내용은 [Windows 가상 머신용 빠른 시작](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) 또는 [Linux 가상 머신용 빠른 시작](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)을 참조하세요.
1. **확장**을 섡택합니다. 이 가상 머신에 설치된 가상 머신 확장 목록이 표시됩니다.
1. **추가** 탭을 선택합니다. **새 리소스** 메뉴에 사용 가능한 가상 머신 확장 목록이 표시됩니다.
1. **Azure Monitor, Update 및 Configuration Management** 확장을 선택하고 **만들기**를 선택합니다. **확장 설치** 구성 페이지가 열립니다.
    >[!NOTE]
    > **Azure Monitor, Update 및 Configuration Management** 확장이 Marketplace에 나열되지 않으면 사용할 수 있도록 Azure Stack 운영자에게 연락하세요.
1. **확장 설치** 구성 페이지에서, 이전 단계에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키(기본 키)** 를 붙여넣습니다.
1. 구성이 완료되면 **확인**을 선택합니다. 확장의 상태가 **프로비전 성공**으로 표시됩니다. Security Center에 가상 머신이 나타날 때까지 최대 1시간이 걸릴 수 있습니다.


### <a name="onboard-your-linux-machines"></a>Linux 컴퓨터 온보딩
Linux 컴퓨터를 추가하려면 **에이전트 관리** 페이지의 WGET 명령이 필요합니다.
1. **에이전트 관리** 페이지에서 **WGET** 명령을 메모장에 복사합니다. Linux 컴퓨터에서 액세스할 수 있는 위치에 이 파일을 저장합니다.
1. Linux 컴퓨터에서 WGET 명령을 사용하여 이 파일을 엽니다. 모든 콘텐츠를 선택하고 복사하여 터미널 콘솔에 붙여넣습니다.
1. 설치가 완료되면 *pgrep* 명령을 실행하여 *omsagent*가 설치되었는지 확인할 수 있습니다. 이 명령은 *omsagent* PID를 반환합니다.
    에이전트의 로그는 */var/opt/microsoft/omsagent/\<workspace id>/log/* 에서 찾을 수 있습니다. 새 Linux 컴퓨터가 Security Center에 나타날 때까지 최대 30분이 걸릴 수 있습니다.


### <a name="onboard-your-windows-machines"></a>Windows 컴퓨터 온보딩
Windows 컴퓨터를 추가하려면 **에이전트 관리** 페이지의 정보가 필요하며, 적절한 에이전트 파일(32/64비트)을 다운로드해야 합니다.
1. 사용하는 컴퓨터 프로세서 유형에 해당하는 **Windows 에이전트 다운로드** 링크를 선택하여 설치 파일을 다운로드합니다.
1. **에이전트 관리** 페이지에서 **작업 영역 ID** 및 **기본 키**를 복사하여 메모장에 붙여넣습니다.
1. 다운로드한 설치 파일을 대상 컴퓨터에 복사하고 실행합니다.
1. 설치 마법사의 지시를 따릅니다(**다음**, **동의**, **다음**, **다음**).
    1. **Azure Log Analytics** 페이지에서, 앞에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키(기본 키)** 를 붙여넣습니다.
    1. 컴퓨터가 Azure Government 클라우드에서 Log Analytics 작업 영역에 보고해야 하는 경우 **Azure Cloud** 드롭다운 목록에서 **Azure 미국 정부**를 선택합니다.
    1. 컴퓨터가 프록시 서버를 통해 Log Analytics 서비스와 통신해야 하는 경우 **고급**을 선택하고 프록시 서버의 URL 및 포트 번호를 제공합니다.
    1. 구성 설정을 모두 입력했으면 **다음**을 선택합니다.
    1. **설치 준비 완료** 페이지에서 적용할 설정을 검토하고 **설치**를 선택합니다.
    1. **구성이 완료되었습니다** 페이지에서 **마침**을 선택합니다.

완료되면 **제어판**에 **Log Analytics 에이전트**가 나타납니다. 구성을 검토하고 에이전트에 연결되었는지 확인할 수 있습니다.

에이전트 설치 및 구성에 대한 자세한 내용은 [Windows 컴퓨터 연결](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)을 참조하세요.


## <a name="verifying"></a>확인 중
축하합니다! 이제 Azure 및 비 Azure 컴퓨터를 한 장소에서 모두 볼 수 있습니다. [자산 인벤토리 페이지](asset-inventory.md)를 열고 관련 리소스 종류로 필터링합니다. 이 두 아이콘은 다음과 같은 종류를 구분합니다.

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 비 Azure 컴퓨터

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM


## <a name="next-steps"></a>다음 단계

이 페이지에서는 비 Azure 컴퓨터를 Azure Security Center에 연결하는 방법을 설명했습니다. 상태를 모니터링하려면 다음 페이지에 설명된 대로 인벤토리 도구를 사용하세요.

- [자산 인벤토리 및 관리 도구를 사용하여 리소스 검색 및 관리](asset-inventory.md)