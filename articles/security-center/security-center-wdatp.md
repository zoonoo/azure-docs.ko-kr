---
title: Azure Security Center에 포함된 엔드포인트용 Microsoft Defender 라이선스 사용
description: 엔드포인트용 Microsoft Defender에 대해 알아보고 Azure Security Center에서 배포합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/08/2021
ms.author: memildin
ms.openlocfilehash: 88d0a3dcd89ea678d77bc558fc680630bc0f2309
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168181"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Security Center의 통합 EDR 솔루션인 엔드포인트용 Microsoft Defender로 엔드포인트 보호

Microsoft Defender for Endpoint는 클라우드에서 제공하는 전체적인 엔드포인트 보안 솔루션입니다. 주요 기능은 다음과 같습니다.

- 위험 기반 취약성 관리 및 평가 
- 공격 노출 영역 축소
- 동작 기반 및 클라우드 기반 보호
- EDR(엔드포인트 검색 및 응답)
- 자동 조사 및 수정
- 관리되는 헌팅 서비스

> [!TIP]
> 원래 **Windows Defender ATP** 로 출시되었던 EDR(엔드포인트 검색 및 응답) 제품은 2019년에 이름이 **Microsoft Defender ATP** 로 변경되었습니다.
>
> Ignite 2020에서 [Microsoft Defender XDR 제품군](https://www.microsoft.com/security/business/threat-protection)을 출시했고 EDR 구성 요소의 이름은 **엔드포인트용 Microsoft Defender** 로 변경되었습니다.


## <a name="availability"></a>가용성

| 양상                          | 세부 정보                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                  | GA(일반 공급)                                                                                                                                                                                                                                                                                      |
| 가격 책정:                        | [서버용 Azure Defender](defender-for-servers-introduction.md) 필요                                                                                                                                                                                                                                             |
| 지원되는 플랫폼:            |  • Windows를 실행하는 Azure 머신<br> • Windows를 실행하는 Azure Arc 머신|
| 지원되는 Windows 버전:  |  • **GA(일반 공급) -** Windows Server 2016, 2012 R2, 2008 R2 SP1에서 검색<br> • **미리 보기 -** Windows Server 2019, [WVD(Windows Virtual Desktop)](../virtual-desktop/overview.md), [Windows 10 Enterprise 다중 세션](../virtual-desktop/windows-10-multisession-faq.yml)(이전 EVD(Enterprise for Virtual Desktops))|
| 지원되지 않는 운영 체제:  |  • Windows 10(EVD 또는 WVD 이외)<br> • Linux|
| 필요한 역할 및 권한: | 통합을 사용하거나 사용하지 않도록 설정하려는 경우: **보안 관리자** 또는 **소유자**<br>Security Center에서 MDATP 경고를 보려는 경우: **보안 읽기 권한자**, **읽기 권한자**, **리소스 그룹 기여자**, **리소스 그룹 소유자**, **보안 관리자**, **구독 소유자** 또는 **구독 기여자**|
| 클라우드:                         | ![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov<br>![아니요](./media/icons/no-icon.png) 중국 정부, 기타 정부                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Security Center의 엔드포인트용 Microsoft Defender 기능

엔드포인트용 Microsoft Defender는 다음을 제공합니다.

- **고급 위반 후 검색 센서**. Windows 머신에 대한 엔드포인트용 Defender의 센서는 수많은 동작 신호의 배열을 수집합니다.

- **분석 기반, 클라우드 기반, 위반 후 검색**. 엔드포인트용 Defender는 지속적으로 변화하는 위협에 빠르게 적응하며 고급 분석 기능과 빅 데이터를 활용합니다. 또한 Windows, Azure, Office 전반에 걸쳐 생성되는 신호를 통해 알려지지 않은 위협을 검색하는 Intelligent Security Graph의 기능을 통해 보안을 더욱 강화하며 신속하게 대응할 수 있도록 조치 가능한 경고를 제공합니다.

- **위협 인텔리전스**. 엔드포인트용 Defender는 공격자 도구, 기술, 절차를 식별할 때 경고를 생성합니다. 이 과정에서는 Microsoft 위협 확인 담당자와 보안 팀에서 생성하며 파트너가 제공한 정보를 통해 강화된 데이터를 활용합니다.

엔드포인트용 Defender를 Security Center와 통합하면 다음과 같은 추가 기능을 사용할 수 있습니다.

- **자동 온보딩**. Security Center는 Security Center에서 모니터링하는 모든 Windows 서버에 대해 엔드포인트용 Microsoft Defender 센서를 사용하도록 자동으로 설정합니다.

- **단일 창**. Security Center 콘솔에 엔드포인트용 Microsoft Defender 경고가 표시됩니다. 추가로 조사하려면 경고 프로세스 트리 및 인시던트 그래프와 같은 추가 정보가 표시되는 엔드포인트용 Microsoft Defender의 자체 포털 페이지를 사용합니다. 그리고 이전 기간(최대 6개월 전)의 모든 동작이 표시되는 세부 컴퓨터 타임라인도 확인할 수 있습니다.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="엔드포인트용 Microsoft Defender의 Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>엔드포인트용 Microsoft Defender 테넌트 위치

Azure Security Center를 사용하여 서버를 모니터링하는 경우 엔드포인트용 Microsoft Defender가 자동으로 만들어집니다. 엔드포인트용 Defender에서 수집한 데이터는 프로비저닝 중 식별되는 대로 테넌트의 지리적 위치에 저장됩니다. 고객 데이터 - 가명 양식 - 미국에 있는 중앙 스토리지 및 처리 저장소 및 처리 시스템에도 저장될 수 있습니다. 

위치를 구성한 후에는 변경할 수 없습니다. 엔드포인트용 Microsoft Defender에 대한 라이선스가 있고 다른 위치로 데이터를 이동해야 하는 경우 Microsoft 지원에 문의하여 테넌트를 다시 설정합니다.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>엔드포인트용 Microsoft Defender 통합 사용 설정

1. 머신이 엔드포인트용 Defender에 필요한 요구 사항을 충족하는지 확인합니다.

    - **모든 버전의 Windows의 경우**:
        - [디바이스 프록시 및 인터넷 연결 설정 구성](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)에 설명된 네트워크 설정을 구성합니다.
        - Defender to Endpoint를 온-프레미스 머신에 배포하는 경우 [Azure Arc 사용 설정 서버와 하이브리드 머신 연결](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)에 설명된 대로 Azure Arc에 연결합니다.
    - 또한 **Windows Server 2019 머신** 의 경우 유효한 에이전트를 실행하고 MicrosoftMonitoringAgent 확장이 있는지 확인합니다.

1. **서버용 Azure Defender** 를 사용하도록 설정합니다. [빠른 시작: Azure Defender 사용](enable-azure-defender.md)을 참조하세요.

1. 서버에 엔드포인트용 Microsoft Defender 라이선스가 있고 배포한 경우 [Windows 서버 오프보딩](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)에 설명된 절차를 사용하여 제거합니다.
1. Security Center 메뉴에서 **가격 책정 및 설정** 을 선택합니다.
1. 변경하려는 구독을 선택합니다.
1. **위협 탐지** 를 선택합니다.
1. **엔드포인트용 Microsoft Defender에서 데이터에 액세스하도록 허용** 을 선택하고 **저장** 을 선택합니다.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Azure Security Center와 Microsoft의 EDR 솔루션, 엔드포인트용 Microsoft Defender 간 통합 사용":::

    Azure Security Center는 엔드포인트용 Microsoft Defender에 서버를 자동으로 온보딩합니다. 온보딩은 최대 24시간까지 걸릴 수 있습니다.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>엔드포인트용 Microsoft Defender 액세스

1. 사용자 계정에 필요한 권한이 있는지 확인합니다. 자세한 내용은 [Microsoft Defender Security Center에 대한 사용자 액세스 할당](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)을 확인하세요.

1. 익명 트래픽을 차단하는 프록시 또는 방화벽이 있는지 확인합니다. 엔드포인트용 Defender 센서는 시스템 컨텍스트에서 연결하므로 익명 트래픽이 허용되어야 합니다. 엔드포인트용 Defender 포털에 대한 방해 없이 액세스하도록 보장하려면 [프록시 서버에서 서비스 URL에 대한 액세스 사용](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)의 지침을 따르세요.

1. [Microsoft Defender Security Center 포털](https://securitycenter.windows.com/)을 엽니다. [Microsoft Defender Security Center 포털 개요](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)에서 포털의 기능 및 아이콘에 대해 자세히 알아보세요. 

## <a name="send-a-test-alert"></a>테스트 경고 보내기

무해한 엔드포인트용 Microsoft Defender 테스트 경고를 생성하려면 다음을 수행하세요.

1. 'C:\test-MDATP-test' 폴더를 만듭니다.
1. 원격 데스크톱을 사용하여 머신에 액세스합니다.
1. 명령줄 창을 엽니다.
1. 다음 명령을 복사하여 프롬프트에서 실행합니다. 명령 프롬프트 창이 자동으로 닫힙니다.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="테스트 경고를 생성하는 명령을 포함하는 명령 프롬프트 창.":::

1. 명령이 정상적으로 실행되면 Azure Security Center 대시보드와 엔드포인트용 Microsoft Defender 포털에 새 경고가 표시됩니다. 이 경고가 표시되려면 몇 분 정도 걸릴 수 있습니다.
1. Security Center에서 경고를 검토하려면 **보안 경고** > **의심스러운 Powershell 명령줄** 로 이동합니다.
1. 조사 창에서 엔드포인트용 Microsoft Defender 포털로 이동하는 링크를 선택합니다.

    > [!TIP]
    > 경고는 **정보** 심각도로 트리거됩니다.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Security Center의 통합된 엔드포인트용 Microsoft Defender에 대한 FAQ

- [엔드포인트용 Microsoft Defender에 대한 라이선스 요구 사항은 무엇인가요?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Microsoft Defender for Endpoint에 대한 라이선스가 이미 있는 경우 Azure Defender 할인을 받을 수 있나요?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [타사 EDR 도구에서 전환하려면 어떻게 할까요?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>엔드포인트용 Microsoft Defender에 대한 라이선스 요구 사항은 무엇인가요?
엔드포인트용 Defender는 추가 비용 없이 **서버용 Azure Defender** 에 포함되어 있습니다. 또는 50대 이상의 머신에 대해 개별적으로 구매할 수 있습니다.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Microsoft Defender for Endpoint에 대한 라이선스가 이미 있는 경우 Azure Defender 할인을 받을 수 있나요?
Microsoft Defender for Endpoint에 대한 라이선스가 이미 있는 경우 Azure Defender 라이선스의 해당 부분에 대해 비용을 지불할 필요가 없습니다.

할인을 확인하려면 Security Center 지원 팀에 문의하고 관련 작업 영역 ID, 지역 및 각 관련 라이선스에 대한 라이선스 정보를 제공하세요.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>타사 EDR 도구에서 전환하려면 어떻게 할까요?
비 Microsoft 엔드포인트 솔루션에서 전환하는 방법에 대한 자세한 지침은 엔드포인트용 Microsoft Defender 설명서인 [마이그레이션 개요](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)에서 제공됩니다.
  


## <a name="next-steps"></a>다음 단계

- [Azure Security Center에서 지원하는 기능 및 플랫폼](security-center-os-coverage.md)
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md): 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.