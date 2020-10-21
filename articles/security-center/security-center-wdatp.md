---
title: Azure Security Center 포함 된 끝점 라이선스에 Microsoft Defender 사용
description: 끝점에 대 한 Microsoft Defender에 대해 알아보고 Azure Security Center에서 배포 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 1574810de5b1a2df1b82ea9cc19c88be5c72fec6
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342147"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Security Center의 통합 EDR 솔루션을 사용 하 여 끝점 보호: 끝점에 대 한 Microsoft Defender

Microsoft Defender for Endpoint는 클라우드로 제공 되는 전체적인 끝점 보안 솔루션입니다. 주요 기능은 다음과 같습니다.

- 위험 기반 취약성 관리 및 평가 
- 공격 노출 영역 축소
- 동작 기반 및 클라우드 기반 보호
- EDR (끝점 검색 및 응답)
- 자동 조사 및 수정
- 관리 되는 구하기 서비스

> [!TIP]
> 원래 **Windows DEFENDER atp**로 시작 된 Edr (끝점 검색 및 응답) 제품은 2019에서 **Microsoft Defender atp**로 이름이 변경 되었습니다.
>
> Ignite 2020에서 [Microsoft DEFENDER XDR suite](https://www.microsoft.com/security/business/threat-protection) 를 시작 했 고이 edr 구성 요소의 이름이 **Microsoft defender for Endpoint**로 바뀌었습니다.


## <a name="availability"></a>가용성

| 양상                          | 세부 정보                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                  | GA(일반 공급)                                                                                                                                                                                                                                                                                      |
| 가격 책정:                        | [서버용 Azure Defender](security-center-pricing.md) 필요                                                                                                                                                                                                                                             |
| 지원되는 플랫폼:            | ![예](./media/icons/yes-icon.png) Windows를 실행 하는 Azure 컴퓨터<br>![예](./media/icons/yes-icon.png) Windows를 실행 하는 Azure Arc 컴퓨터|
| 지원 되는 Windows 버전:  | Defender for Endpoint는 Windows 10 1703 이상 및 Windows Server 2019에 기본 제공 됩니다.<br>Security Center는 Windows Server 2016, 2012 R2 및 2008 R2 s p 1에 대 한 검색을 지원 합니다.<br>이 통합을 사용한 서버 끝점 모니터링은 Office 365 GCC 고객에 대해 사용 하지 않도록 설정 되었습니다. |
| 필요한 역할 및 권한: | 통합을 사용 하거나 사용 하지 않도록 설정 하려면 **보안 관리자** 또는 **소유자** 입니다.<br>Security Center에서 MDATP 경고를 보려면 **보안 읽기 권한자**, **읽기 권한자**, **리소스 그룹 참가자**, **리소스 그룹 소유자**, **보안 관리자**, **구독 소유자**또는 **구독 참가자** 입니다.                         |
| 클라우드:                         | ![예](./media/icons/yes-icon.png) 상용 클라우드.<br>![아니요](./media/icons/no-icon.png) 글로벌 Azure 클라우드에서 워크 로드를 실행 하는 GCC 고객<br>![예](./media/icons/yes-icon.png) US Gov<br>![아니요](./media/icons/no-icon.png) 중국 정부, 기타 정부                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Security Center의 끝점 기능을 위한 Microsoft Defender

Microsoft Defender for Endpoint는 다음을 제공 합니다.

- **고급 위반 검색 센서**입니다. Defender for Endpoint의 Windows 컴퓨터에 대 한 센서는 수많은 동작 신호를 수집 합니다.

- **분석 기반, 클라우드 기반, 위반 후 검색** 끝점에 대 한 Defender는 위협 변화에 빠르게 적응 합니다. 고급 분석 기능과 빅 데이터를 활용합니다. Windows, Azure 및 Office에서 신호를 사용 하 여 알 수 없는 위협을 검색 하는 Intelligent Security Graph 기능으로 증폭 됩니다. 신속하게 대응할 수 있도록 조치 가능한 경고를 제공합니다.

- **위협 인텔리전스**. Defender for Endpoint는 공격자 도구, 기술 및 절차를 식별할 때 경고를 생성 합니다. 이 과정에서는 Microsoft 위협 확인 담당자와 보안 팀에서 생성하며 파트너가 제공한 정보를 통해 강화된 데이터를 활용합니다.

Defender for Endpoint를 Security Center와 통합 하 여 다음과 같은 추가 기능을 이용할 수 있습니다.

- **자동 온 보 딩**. Security Center는 Windows Server 2019를 실행 하는 경우를 제외 하 고 Security Center에서 모니터링 하는 모든 Windows server에 대해 Microsoft Defender for Endpoint 센서를 자동으로 사용 하도록 설정 합니다.

- **단일 창** Security Center 콘솔에는 Microsoft Defender에 대 한 끝점 경고가 표시 됩니다. 추가로 조사 하려면 끝점의 고유한 포털 페이지에 대해 Microsoft Defender를 사용 합니다 .이 페이지에는 경고 프로세스 트리 및 인시던트 그래프와 같은 추가 정보가 표시 됩니다. 그리고 이전 기간(최대 6개월 전)의 모든 동작이 표시되는 세부 컴퓨터 타임라인도 확인할 수 있습니다.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="끝점 자체 Security Center 용 Microsoft Defender" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender for Endpoint 테 넌 트 위치

Azure Security Center를 사용 하 여 서버를 모니터링 하는 경우에는 Microsoft Defender for Endpoint 테 넌 트가 자동으로 만들어집니다. Defender에서 끝점에 의해 수집 된 데이터는 프로 비전 중에 식별 된 테 넌 트의 지리적 위치에 저장 됩니다. 고객 데이터-pseudonymized 양식-미국의 중앙 저장소 및 처리 시스템에 저장 될 수도 있습니다. 

위치를 구성한 후에는 변경할 수 없습니다. 데이터를 다른 위치로 이동 해야 하는 경우 Microsoft 지원에 문의 하 여 테 넌 트를 다시 설정 합니다.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Microsoft Defender에서 끝점 통합 사용

1. **서버에 대해 Azure Defender를**사용 하도록 설정 합니다. [Azure Security Center 가격 책정을](security-center-pricing.md#enable-azure-defender)참조 하세요.

    > [!NOTE]
    > Azure Arc 사용 가능 컴퓨터를 보호 하려면 [빠른 시작: Azure arc 사용 서버와 하이브리드 컴퓨터 연결](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)의 지침을 사용 합니다.

1. 서버에서 끝점에 대해 Microsoft Defender를 이미 사용 허가 하 고 배포한 경우 [Offboard Windows servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)에 설명 된 절차를 사용 하 여 제거 합니다.
1. Security Center 메뉴에서 **가격 책정 및 설정**을 선택합니다.
1. 변경하려는 구독을 선택합니다.
1. **위협 탐지**를 선택합니다.
1. **Windows DEFENDER ATP가 내 데이터에 액세스 하도록 허용**을 선택 하 고 **저장**을 선택 합니다.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="끝점 자체 Security Center 용 Microsoft Defender":::

    Azure Security Center은 끝점에 대 한 Microsoft Defender에 서버를 자동으로 등록 합니다. 온 보 딩은 최대 24 시간이 걸릴 수 있습니다.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>끝점 포털에 대 한 Microsoft Defender 액세스

1. 사용자 계정에 필요한 권한이 있는지 확인 합니다. [자세히 알아보기](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. 익명 트래픽을 차단 하는 프록시 또는 방화벽이 있는지 확인 합니다. Defender for Endpoint 센서는 시스템 컨텍스트에서 연결 하므로 익명 트래픽이 허용 되어야 합니다. 되어야에 끝점 포털에 대 한 액세스를 보장 하려면 [프록시 서버에서 서비스 url에 대 한 액세스 사용](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)의 지침을 따르세요.

1. [Microsoft Defender Security Center 포털](https://securitycenter.windows.com/)을 엽니다. [Microsoft Defender Security Center 포털 개요](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)에서 포털의 기능 및 아이콘에 대해 자세히 알아보세요. 

## <a name="send-a-test-alert"></a>테스트 경고 보내기

무해 한 Microsoft Defender for Endpoint 테스트 경고를 생성 하려면:

1. ' C:\test-MDATP-test ' 폴더를 만듭니다.
1. 원격 데스크톱을 사용하여 Windows Server 2012 R2 VM 또는 Windows Server 2016 VM에 액세스합니다.
1. 명령줄 창을 엽니다.
1. 다음 명령을 복사하여 프롬프트에서 실행합니다. 명령 프롬프트 창이 자동으로 닫힙니다.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="끝점 자체 Security Center 용 Microsoft Defender":::

1. 명령이 성공 하면 Azure Security Center 대시보드와 끝점 포털에 대 한 Microsoft Defender에 새 경고가 표시 됩니다. 이 경고가 표시되려면 몇 분 정도 걸릴 수 있습니다.
1. Security Center에서 경고를 검토 하려면 **보안 경고**  >  **의심 스러운 PowerShell 명령줄**로 이동 합니다.
1. 조사 창에서 링크를 선택 하 여 끝점 포털에 대 한 Microsoft Defender로 이동 합니다.


## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Security Center의 끝점 통합 Microsoft Defender에 대 한 FAQ

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint에 대 한 라이선스 요구 사항은 무엇 인가요?

Defender for Endpoint는 **서버에 대 한 Azure defender**를 사용 하 여 추가 비용 없이 포함 됩니다. 또는 50 대의 컴퓨터에 대해 개별적으로 구매할 수 있습니다.


### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>타사 EDR 도구에서 전환할 어떻게 할까요? 있나요?

Microsoft 끝점 이외의 솔루션에서 전환 하는 방법에 대 한 전체 지침은 Microsoft Defender for Endpoint 설명서에서 제공 됩니다. [마이그레이션 개요](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>다음 단계

- [Azure Security Center에서 지원하는 기능 및 플랫폼](security-center-os-coverage.md)
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md): 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.