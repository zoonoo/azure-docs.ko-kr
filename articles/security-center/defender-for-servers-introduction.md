---
title: 서버에 대 한 Azure Defender-혜택 및 기능
description: 서버에 대 한 Azure Defender의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 29f15aa7d1cbcd260ce44f6083138681ceb9fd79
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940312"
---
# <a name="introduction-to-azure-defender-for-servers"></a>서버용 Azure Defender 소개

서버에 대 한 Azure Defender는 Windows 및 Linux 컴퓨터에 대 한 위협 검색 및 고급 방어를 추가 합니다.

Windows의 경우 Azure Defender는 Windows 기반 컴퓨터를 모니터링 하 고 보호 하기 위해 Azure 서비스와 통합 됩니다. Security Center는 이러한 모든 서비스의 경고 및 수정 제안을 사용하기 쉬운 형식으로 제공합니다.

Linux의 경우 Azure Defender는 가장 일반적인 Linux 감사 프레임 워크 중 하나인 **auditd**를 사용 하 여 linux 컴퓨터에서 감사 레코드를 수집 합니다. auditd는 주요 커널에 상주합니다. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>서버에 대 한 Azure Defender의 이점은 무엇 인가요?

서버에 대 한 Azure Defender에서 제공 되는 위협 검색 및 보호 기능에는 다음이 포함 됩니다.

- **Vm에 대 한 취약성 평가 검사** -Azure Security Center에 포함 된 취약성 검사 프로그램은 Qualys에 의해 구동 됩니다. 

    Qualys의 스캐너는 Azure Virtual Machines의 취약점에 대 한 실시간 식별을 위한 최고의 도구 중 하나입니다. Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 항목이 Security Center 내에서 원활하게 처리됩니다. [자세히 알아보기](deploy-vulnerability-assessment-vm.md).

- **JIT (just-in-time) VM 액세스**  -위협 행위자는 RDP 또는 SSH와 같은 개방형 관리 포트를 사용 하 여 액세스 가능한 컴퓨터를 적극적으로 사냥 합니다. 모든 가상 머신은 공격의 대상이 될 수 있습니다. VM이 성공적으로 손상 되 면 환경 내에서 추가 리소스를 공격 하는 진입점으로 사용 됩니다.

    서버에 대해 Azure Defender를 사용 하도록 설정 하는 경우 just-in-time VM 액세스를 사용 하 여 Vm에 대 한 인바운드 트래픽을 잠글 수 있으며, 필요에 따라 Vm에 연결 하는 데 편리한 액세스를 제공 하는 동시에 공격에 대 한 노출을 줄일 수 있습니다. [자세히 알아보기](just-in-time-explained.md).

- **Fim (파일 무결성** 모니터링)-변경 모니터링이 라고도 하는 fim (파일 무결성 모니터링)은 운영 체제, 응용 프로그램 소프트웨어 및 기타 공격을 나타낼 수 있는 변경 내용에 대 한 파일 및 레지스트리를 검사 합니다. 비교 메서드는 파일의 현재 상태가 파일의 마지막 검사와 다른지 확인하는 데 사용됩니다. 이 비교를 활용하여 파일에 대해 유효하거나 의심스러운 수정 사항이 있는지 확인할 수 있습니다.

    서버에 대해 Azure Defender를 사용 하도록 설정 하는 경우 FIM을 사용 하 여 Windows 파일, Windows 레지스트리 및 Linux 파일의 무결성에 대 한 유효성을 검사할 수 있습니다. [자세히 알아보기](security-center-file-integrity-monitoring.md).

- **적응 응용 프로그램 컨트롤 (AAC)** -적응 응용 프로그램 컨트롤은 컴퓨터에 대해 알려진 안전한 응용 프로그램의 허용 목록을 정의 하기 위한 인텔리전트 및 자동화 된 솔루션입니다.

    적응 응용 프로그램 제어를 사용 하도록 설정 하 고 구성 하면 안전한 것으로 정의한 응용 프로그램이 아닌 다른 응용 프로그램을 실행 하는 경우 보안 경고를 받게 됩니다. [자세히 알아보기](security-center-adaptive-network-hardening.md).

- **ANH (적응 네트워크 강화** )-nsg (네트워크 보안 그룹)를 적용 하 여 리소스로 들어오고 나가는 트래픽을 필터링 하 고 네트워크 보안 상태를 향상 시킵니다. 그러나 NSG를 통과 하는 실제 트래픽이 정의 된 NSG 규칙의 하위 집합인 경우도 있습니다. 이러한 경우 실제 트래픽 패턴에 따라 NSG 규칙을 강화 하 여 보안 상태를 더욱 향상 시킬 수 있습니다.

    적응 네트워크 강화는 NSG 규칙을 강화 하기 위한 권장 사항을 제공 합니다. 실제 트래픽, 알려진 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상에 대 한 요소에 해당 하는 기계 학습 알고리즘을 사용 하 고 특정 IP/포트 튜플의 트래픽만 허용 하는 권장 사항을 제공 합니다. [자세히 알아보기](security-center-adaptive-network-hardening.md).

- **Microsoft DEFENDER atp (Advanced Threat protection)와의 통합 (Windows에만 해당)** -Azure Defender는 MICROSOFT Defender Atp (Advanced threat protection)와 통합 됩니다. 또한 포괄적인 EDR(엔드포인트 검색 및 응답) 기능을 제공합니다. [자세히 알아보기](security-center-wdatp.md).

    > [!IMPORTANT]
    > Microsoft Defender ATP 센서는 Security Center를 사용하는 Windows 서버에서 자동으로 사용하도록 설정됩니다.

    Microsoft Defender ATP는 위협을 탐지하면 경고를 트리거합니다. 이 경고는 Security Center 표시 됩니다. Security Center에서 Microsoft Defender ATP 콘솔로 피벗 하 고 자세한 조사를 수행 하 여 공격의 범위를 확인할 수도 있습니다. Microsoft Defender ATP에 대한 자세한 내용은 [Microsoft Defender ATP 서비스에 서버 온보딩](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)을 참조하세요.

- **Docker 호스트 강화** -Azure Security Center IaaS Linux Vm 또는 Docker 컨테이너를 실행 하는 다른 Linux 컴퓨터에서 호스트 되는 관리 되지 않는 컨테이너를 식별 합니다. Security Center은 이러한 컨테이너의 구성을 지속적으로 평가 합니다. 그런 다음 이러한 항목을 CIS (Internet Security) Docker 벤치 마크와 비교 합니다. Security Center에는 CIS Docker 벤치 마크의 전체 규칙 집합이 포함 되며 컨테이너가 컨트롤을 충족 하지 않는 경우 경고를 표시 합니다. [자세히 알아보기](harden-docker-hosts.md).

- **Fileless 공격 검색 (Windows만 해당)** -Fileless 공격은 디스크 기반 검사 기술에서 검색 하지 않도록 악의적인 페이로드를 메모리에 삽입 합니다. 그러면 공격자의 페이로드가 손상 된 프로세스의 메모리 내에 유지 되 고 다양 한 악의적인 작업을 수행 합니다.

  파일리스 공격 탐지를 통해 자동화된 메모리 포렌식 기술은 파일리스 공격 도구 키트, 기술 및 동작을 식별합니다. 이 솔루션은 런타임에 컴퓨터를 정기적으로 검색 하 고 프로세스의 메모리에서 직접 정보를 추출 합니다. Linux에 대 한 특정 정보에는 다음이 포함 됩니다. 

  - 잘 알려진 도구 키트 및 암호화 마이닝 소프트웨어 

  - Shellcode-일반적으로 소프트웨어 취약성을 악용 하는 데 일반적으로 사용 되는 작은 코드 조각입니다.

  - 프로세스 메모리에 삽입 된 악성 실행 파일

  Fileless 공격 감지는 네트워크 활동 등의 추가 프로세스 메타 데이터를 사용 하 여 설명을 포함 하는 자세한 보안 경고를 생성 합니다. 이렇게 하면 경고 심사, 상관 관계 및 다운스트림 응답 시간이 가속화 됩니다. 이 방법은 이벤트 기반 EDR 솔루션을 보완 하 고 향상 된 검색 범위를 제공 합니다.

  파일리스 공격 탐지 경고에 대한 자세한 내용은 [경고 참조 표](alerts-reference.md#alerts-windows)를 참조하세요.

- **Linux auditd 경고 및 Log Analytics 에이전트 통합 (linux에만 해당)** -auditd 시스템은 시스템 호출 모니터링을 담당 하는 커널 수준 하위 시스템으로 구성 됩니다. 지정된 규칙 세트를 기준으로 필터링하고 이에 대한 메시지를 소켓에 씁니다. Security Center는 Log Analytics 에이전트 내에서 auditd 패키지의 기능을 통합합니다. 이러한 통합을 통해 사전 요구 사항 없이 지원되는 모든 Linux 배포에서 auditd 이벤트를 수집할 수 있습니다.

    auditd 레코드는 Linux 에이전트용 Log Analytics 에이전트를 사용하여 이벤트에 수집, 보강 및 집계됩니다. Security Center는 Linux 신호를 사용하여 클라우드 및 온-프레미스 Linux 컴퓨터에서 악의적인 동작을 탐지하는 새 분석을 지속적으로 추가합니다. Windows 기능과 비슷하게 이러한 분석은 의심스러운 프로세스, 의심스러운 로그인 시도, 커널 모듈 로드 및 기타 활동 전반에 걸쳐 있습니다. 이러한 활동은 컴퓨터가 공격을 받고 있거나 위반되었음을 나타낼 수 있습니다.  

    Linux 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-linux)를 참조하세요.


## <a name="simulating-alerts"></a>경고 시뮬레이션

다음 플레이 북 중 하나를 다운로드 하 여 경고를 시뮬레이션할 수 있습니다.

- Windows: [Azure Security Center 플레이 북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

- Linux: [Azure Security Center 플레이 북: Linux 탐지](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)




## <a name="next-steps"></a>다음 단계

이 문서에서는 서버에 대 한 Azure Defender에 대해 알아보았습니다. 

관련 자료는 다음 문서를 참조하세요. 

- Security Center에서 경고를 생성하든 Security Center에서 다른 보안 제품으로부터 경고를 받든 이를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.

- > [!div class="nextstepaction"]
    > [Azure Defender 사용](security-center-pricing.md)