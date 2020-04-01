---
title: Azure 보안 센터 FAQ - 데이터 수집 및 에이전트
description: 위협을 방지, 탐지 및 대응하는 데 도움이 되는 제품인 Azure Security Center의 데이터 수집, 에이전트 및 작업 영역에 대해 자주 묻는 질문
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436192"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>FAQ - 데이터 수집, 에이전트 및 작업 영역에 대한 질문

보안 센터는 Azure 가상 시스템(VM), 가상 시스템 규모 집합, IaaS 컨테이너 및 비Azure 컴퓨터(온-프레미스 컴퓨터 포함)에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. 데이터는 컴퓨터에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 데이터를 작업 공간으로 복사하는 Log Analytics 에이전트를 사용하여 수집됩니다.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>보안 센터에서 만든 작업 영역에 Azure Monitor 로그에 대한 요금이 청구되나요?

아니요. 보안 센터에서 만든 작업 영역은 노드 청구당 Azure Monitor 로그에 대해 구성된 반면 Azure Monitor 로그 요금이 발생하지 않습니다. Security Center 청구는 항상 작업 영역에 설치된 Security Center 보안 정책 및 솔루션에 기반합니다.

- **무료 계층** – Security Center는 기본 작업 영역에서 'SecurityCenterFree' 솔루션을 사용하도록 설정합니다. 프리 티어에 대한 요금은 청구되지 않습니다.

- **표준 계층** – Security Center는 기본 작업 영역에서 'Security' 솔루션을 사용하도록 설정합니다.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.

> [!NOTE]
> 보안 센터에서 만든 작업 영역의 로그 분석 가격 책정 계층은 보안 센터 청구에 영향을 주지 않습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Log Analytics 에이전트 설치의 자동 프로비저닝을 위한 VM의 자격요건은 무엇입니까?

다음과 같은 경우 Windows 또는 Linux IaaS VM이 요건 충족됩니다.

- 로그 분석 에이전트 확장은 현재 VM에 설치되어 있지 않습니다.
- VM이 실행 중인 상태입니다.
- Windows 또는 Linux [Azure 가상 시스템 에이전트가](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 설치됩니다.
- VM이 웹 애플리케이션 방화벽이나 차세대 방화벽과 같은 어플라이언스로 사용되지 않습니다.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center에서 만든 기본 작업 영역을 삭제할 수 있나요?

**기본 작업 영역을 삭제하지 않는 것이 좋습니다.** Security Center는 기본 작업 영역을 사용하여 VM의 보안 데이터를 저장합니다. 작업 영역을 삭제하는 경우 Security Center는 이 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다.

복구하려면 삭제된 작업 영역에 연결된 VM에서 로그 분석 에이전트를 제거합니다. Security Center는 에이전트를 다시 설치하고 새 기본 작업 영역을 만듭니다.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?

기존 Log Analytics 작업 영역을 선택하여 Security Center에서 수집한 데이터를 저장할 수 있습니다. 기존 Log Analytics 작업 영역을 사용하려면 다음을 수행합니다.

- 작업 영역이 선택한 Azure 구독과 연결되어 있어야 합니다.
- 작업 영역에 액세스하려면 읽기 이상의 권한이 필요합니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **보안 정책 - 데이터 수집** 아래에서 **다른 작업 영역 사용**을 선택합니다.

    ![다른 작업 영역 사용][4]

1. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

    > [!NOTE]
    > 풀다운 메뉴에는 액세스 권한이 있으며 Azure 구독에 포함된 작업 영역만 표시됩니다.

1. **저장**을 선택합니다. 모니터링되는 VM을 다시 구성할지 묻는 메시지가 표시됩니다.

    - 새 작업 영역 설정을 **새 VM에만 적용**하려면 **아니요**를 선택합니다. 새 작업 영역 설정은 새 에이전트 설치에만 적용됩니다. Log Analytics 에이전트가 설치되어 있지 않은 새로 발견된 VM입니다.
    - 새 작업 영역 설정을 **모든 VM에 적용하려면** **예를** 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

    > [!NOTE]
    > **예를**선택하면 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지 보안 센터에서 만든 작업 영역을 삭제하지 마십시오. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.

    - 작업을 취소하려면 **취소를**선택합니다.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Log Analytics 에이전트가 VM의 확장으로 이미 설치된 경우 어떻게 해야 합니까?<a name="mmaextensioninstalled"></a>

모니터링 에이전트를 확장으로 설치하면 확장 구성을 통해 단일 작업 영역에대해서만 보고할 수 있습니다. Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. 보안 센터는 "보안" 또는 "SecurityCenterFree" 솔루션이 설치된 경우 이미 연결된 작업 영역에 VM의 보안 데이터를 저장합니다. 보안 센터는 이 프로세스의 확장 버전을 최신 버전으로 업그레이드할 수 있습니다.

자세한 내용은 [기존 에이전트 설치의 경우 자동 프로비저닝을](security-center-enable-data-collection.md#preexisting)참조하십시오.



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Log Analytics 에이전트가 컴퓨터에 직접 설치되지만 확장(직접 에이전트)으로 설치되지 않은 경우 어떻게 해야 합니까?<a name="directagentinstalled"></a>

Log Analytics 에이전트가 Azure 확장이 아닌 VM에 직접 설치되는 경우 보안 센터는 Log Analytics 에이전트 확장을 설치하고 Log Analytics 에이전트를 최신 버전으로 업그레이드할 수 있습니다.

설치된 에이전트는 이미 구성된 작업 영역에 계속 보고되며 보안 센터에서 구성된 작업 영역에 도보고됩니다(Windows 컴퓨터에서 다중 호밍이 지원됨).

구성된 작업 영역이 보안 센터의 기본 작업 영역이 아닌 사용자 작업 영역인 경우 보안 센터에서 해당 작업 영역으로 보고하는 VM 및 컴퓨터에서 이벤트 처리를 시작하려면 "보안/"SecurityCenterFree" 솔루션을 설치해야 합니다.

Linux 컴퓨터의 경우 에이전트 다중 호밍이 아직 지원되지 않으므로 기존 에이전트 설치가 감지되면 자동 프로비저닝이 발생하지 않고 컴퓨터의 구성이 변경되지 않습니다.

2019년 3월 17일 이전에 보안 센터에 온보온된 구독의 기존 컴퓨터의 경우 기존 에이전트가 검색되면 Log Analytics 에이전트 확장이 설치되지 않고 컴퓨터가 영향을 받지 않습니다. 이러한 컴퓨터의 경우 "컴퓨터에서 모니터링 에이전트 상태 해결" 권장 사항을 참조하여 이러한 컴퓨터의 에이전트 설치 문제를 해결합니다.

자세한 내용은 다음 섹션을 참조하여 [시스템 센터 운영 관리자 또는 OMS 직접 에이전트가 VM에 이미 설치되어 있는 경우 어떻게 됩니까?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>시스템 센터 운영 관리자 에이전트가 VM에 이미 설치되어 있는 경우 어떻게 해야 합니까?<a name="scomomsinstalled"></a>

보안 센터는 기존 시스템 센터 운영 관리자 에이전트에 Log Analytics 에이전트 확장을 나란히 설치합니다. 기존 에이전트는 시스템 센터 운영 관리자 서버에 정상적으로 계속 보고합니다. Operations Manager 에이전트와 Log Analytics 에이전트는 공통 런타임 라이브러리를 공유하며, 이 라이브러리는 이 프로세스 중에 최신 버전으로 업데이트됩니다. 참고 - Operations Manager 에이전트의 버전 2012가 설치된 경우 자동 프로비저닝을 켜지 마십시오(Operations Manager 서버가 버전 2012일 때 관리 기능이 손실될 수 있음).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>이러한 확장을 제거할 경우 어떤 영향이 있나요?

Microsoft Monitoring 확장을 제거하는 경우 Security Center는 VM의 보안 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다. 24시간 이내에 Security Center는 VM이 확장을 누락하고 확장을 다시 설치했는지 확인합니다.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?

보안 정책에서 구독에 대해 자동 프로비저닝을 해제할 수는 있지만, 해제하지 않는 것이 좋습니다. 자동 프로비저닝을 해제하면 Security Center 권장 사항 및 경고 기능이 제한됩니다. 자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 표준 계층에 대한 구독이 구성된 경우 해당 구독에 대한 보안 정책을 열고 **체험** 계층을 선택합니다.

   ![가격 책정 계층][1]

1. 다음으로 **보안 정책 - 데이터 수집** 페이지에서 **끄기(끄기)를** 선택하여 자동 프로비저닝을 끕니다.
   ![데이터 수집][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 옵트아웃해야 하나요?

> [!NOTE]
> 자동 프로비전을 옵트아웃하도록 선택하는 경우 [옵트아웃의 의미는 무엇인가요?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) 및 [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.

다음에 해당하는 경우 자동 프로비전을 옵트아웃하는 것이 좋습니다.

- Security Center에 의한 자동 에이전트 설치가 전체 구독에 적용됩니다. VM의 하위 집합에 자동 설치를 적용할 수 없습니다. Log Analytics 에이전트와 함께 설치할 수 없는 중요한 VM이 있는 경우 자동 프로비저닝을 옵트아웃해야 합니다.
- 로그 분석 에이전트 확장 프로그램을 설치하여 에이전트의 버전을 업데이트합니다. 이는 직접 에이전트 및 시스템 센터 운영 관리자 에이전트(후자의 경우 운영 관리자 및 Log Analytics 에이전트가 공통 런타임 라이브러리를 공유하며 프로세스에서 업데이트됩니다)에 적용됩니다. 설치된 Operations Manager 에이전트가 버전 2012이고 업그레이드된 경우 Operations Manager 서버가 버전 2012일 때 관리 기능이 손실될 수 있습니다. 설치된 Operations Manager 에이전트가 버전 2012인 경우 자동 프로비저닝을 선택 해제하는 것이 좋습니다.
- 구독 외부에 사용자 지정 작업 영역(중앙 집중식 작업 영역)이 있는 경우 자동 프로비저닝을 옵트아웃해야 합니다. 로그 분석 에이전트 확장을 수동으로 설치하고 보안 센터에서 연결을 재정의하지 않고 작업 공간을 연결할 수 있습니다.
- 구독 내에 자신의 사용자 지정 작업 영역이 있고 구독당 작업 영역을 여러 개 만들지 않으려는 경우 두 가지 옵션이 있습니다.

   1. 자동 프로비전을 옵트아웃할 수 있습니다. 마이그레이션 후 기본 작업 영역 설정을 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)의 설명에 따라 설정합니다.

   1. 또는 마이그레이션이 완료되도록 허용하고, Log Analytics 에이전트를 VM에 설치하고, 생성된 작업 영역에 연결된 VM을 허용할 수 있습니다. 그런 다음 이미 설치된 에이전트를 재구성하도록 선택하여 기본 작업 영역 설정을 설정하고 자신의 사용자 지정 작업 영역을 선택합니다. 자세한 내용은 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)를 참조하세요.


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 의미는 무엇인가요?

마이그레이션이 완료되면 보안 센터는 VM에서 보안 데이터를 수집할 수 없으며 일부 보안 권장 사항 및 경고를 사용할 수 없습니다. 옵트아웃하면 Log Analytics 에이전트를 수동으로 설치하십시오. [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 경우 권장되는 단계는 무엇인가요?

보안 센터가 VM에서 보안 데이터를 수집하고 권장 사항 및 경고를 제공할 수 있도록 Log Analytics 에이전트 확장을 수동으로 설치합니다. 설치 지침에 대해서는 [Windows VM용 에이전트 설치](../virtual-machines/extensions/oms-windows.md) 또는 [Linux VM용 에이전트 설치](../virtual-machines/extensions/oms-linux.md)를 참조하세요.

에이전트를 기존 사용자 지정 작업 영역이나 Security Center가 만든 작업 영역에 연결할 수 있습니다. 사용자 지정 작업 영역에 '보안' 또는 'SecurityCenterFree' 솔루션이 활성화되어 있지 않은 경우 솔루션을 적용해야 합니다. 적용하려면 사용자 지정 작업 영역 또는 구독을 선택하고 보안 정책 – 가격 책정 계층 페이지를 통해 가격 책정 **계층을 적용합니다.**

   ![가격 책정 계층][1]

Security Center는 선택한 가격 책정 계층을 기반으로 작업 영역에 올바른 솔루션을 사용하도록 설정합니다.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Security Center에서 설치한 OMS 확장을 제거하려면 어떻게 할까요?<a name="remove-oms"></a>

로그 분석 에이전트를 수동으로 제거할 수 있습니다. Security Center 권장 사항 및 경고를 제한하기 때문에 권장되지는 않습니다.

> [!NOTE]
> 데이터 수집을 사용하는 경우 Security Center는 에이전트를 제거한 후에 다시 설치합니다.  수동으로 에이전트를 제거하기 전에 데이터 수집을 사용하지 않도록 설정해야 합니다. 데이터 수집을 사용하지 않도록 설정하는 지침은 자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?를 참조하세요.

수동으로 에이전트를 제거하려면:

1.    포털에서 **Log Analytics**을 엽니다.

1.    로그 분석 페이지에서 작업 영역을 선택합니다.

1.    모니터링하지 않으려는 VM을 선택하고 연결 **끊을을**선택합니다.

   ![에이전트 제거][3]

> [!NOTE]
> Linux VM에 이미 비확장 OMS 에이전트가 있는 경우 확장을 제거하면 에이전트도 제거되고 다시 설치해야 합니다.


## <a name="how-do-i-disable-data-collection"></a>데이터 컬렉션을 사용하지 않도록 설정하려면 어떻게 해야 하나요?

자동 프로비전은 기본적으로 해제되어 있습니다. 보안 정책에서 자동 프로비저닝 설정을 해제하여 언제든지 리소스에서 자동 프로비저닝을 사용하지 않도록 설정할 수 있습니다. 시스템 업데이트, OS 취약성 및 엔드포인트 보호에 대한 보안 경고 및 권장 사항을 얻으려면 자동 프로비저닝을 권장합니다.

데이터 수집을 해제하려면 [Azure Portal에 로그인](https://portal.azure.com)하여 **찾아보기**, **Security Center**, **정책 선택**을 차례로 선택합니다. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 선택합니다. 구독을 선택하면 **보안 정책 - 데이터 수집**이 열립니다. **자동 프로비전**에서 **끔**을 선택합니다.


## <a name="how-do-i-enable-data-collection"></a>데이터 컬렉션을 사용하도록 설정하려면 어떻게 해야 하나요?

보안 정책에서 Azure 구독에 대한 데이터 수집을 사용하도록 설정할 수 있습니다. 데이터 수집을 사용하도록 설정하려면 [Azure Portal에 로그인](https://portal.azure.com)하여 **찾아보기**, **Security Center**, **보안 정책**을 차례로 선택합니다. 자동 프로비전을 사용할 구독을 선택합니다. 구독을 선택하면 **보안 정책 - 데이터 수집**이 열립니다. **자동 프로비전**에서 **켬**을 선택합니다.


## <a name="what-happens-when-data-collection-is-enabled"></a>데이터 수집을 사용하도록 설정하면 어떻게 될까요?

자동 프로비저닝이 활성화되면 보안 센터는 지원되는 모든 Azure VM 및 생성된 새 VM에 로그 분석 에이전트를 프로비전합니다. 자동 프로비저닝을 권장하지만 수동 에이전트 설치도 가능합니다. [로그 분석 에이전트 확장을 설치하는 방법에 대해 알아봅니다.](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) 

에이전트는 프로세스 생성 이벤트 4688 및 이벤트 4688 내의 *CommandLine* 필드를 활성화합니다. VM에서 생성된 새 프로세스는 EventLog에 의해 기록되고 보안 센터의 검색 서비스에서 모니터링됩니다. 각 새 프로세스에 대해 기록된 세부 정보에 대한 자세한 내용은 [4688의 설명 필드를](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)참조하십시오. 또한 에이전트는 VM에서 생성되는 4688 이벤트를 수집하고 검색에 저장합니다.

또한 에이전트는 [적응형 애플리케이션 제어](security-center-adaptive-application.md)에 데이터 수집을 사용하도록 설정하고, Security Center는 모든 애플리케이션을 허용하도록 감사 모드에서 로컬 AppLocker 정책을 구성합니다. 이 정책으로 인해 AppLocker는 이벤트를 생성한 다음 보안 센터에서 수집하고 활용합니다. 이 정책은 이미 AppLocker 정책이 구성된 컴퓨터에서는 구성할 수 없습니다. 

Security Center가 VM에서 의심스러운 작업을 감지하면 고객은 경우 [보안 연락처 정보](security-center-provide-security-contact-details.md)가 제공된 경우 전자 메일을 통해 알림을 받습니다. 보안 센터의 보안 경고 대시보드에도 경고가 표시됩니다.


## <a name="will-security-center-work-using-an-oms-gateway"></a>보안 센터가 OMS 게이트웨이를 사용하여 작동합니까?

예. Azure 보안 센터는 Azure 모니터를 사용하여 로그 분석 에이전트를 사용하여 Azure VM 및 서버에서 데이터를 수집합니다.
데이터를 수집하려면 각 VM과 서버가 HTTPS를 사용하여 인터넷에 연결해야 합니다. 연결은 프록시를 사용하거나 [OMS 게이트웨이를](../azure-monitor/platform/gateway.md)통해 직접 연결될 수 있습니다.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent가 서버의 성능에 미치는 영향

에이전트는 시스템 리소스의 명목 양을 소비하며 성능에 거의 영향을 미치지 않습니다. 성능 영향과 에이전트 및 확장에 대한 자세한 내용은 [계획 및 작업 가이드](security-center-planning-and-operations-guide.md#data-collection-and-storage)를 참조하세요.


## <a name="where-is-my-data-stored"></a>내 데이터는 어디에 저장되나요?

이 에이전트에서 수집된 데이터는 구독 또는 새 작업 영역에 연결된 기존 Log Analytics 작업 영역 중 하나에 저장됩니다. 자세한 내용은 [데이터 보안](security-center-data-security.md)을 참조하세요.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
