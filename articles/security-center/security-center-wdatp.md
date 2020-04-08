---
title: 마이크로소프트 디펜더 고급 위협 보호 - Azure 보안 센터
description: 이 문서에서는 Azure 보안 센터와 Microsoft Defender 고급 위협 보호 간의 통합을 소개합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: a502541a5a01ec3304338054239fc3b50f4d3c61
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810301"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Azure 보안 센터를 갖춘 Microsoft Defender 고급 위협 보호

Azure 보안 센터는 [Microsoft Defender 고급 위협](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) 보호(ATP)와 통합하여 클라우드 워크로드 보호 플랫폼을 확장하고 있습니다.
이러한 확장을 통해 제품이 변경되면 포괄적인 EDR(엔드포인트 검색 및 대응) 기능이 제공됩니다. Microsoft Defender ATP 통합을 사용하면 이상을 발견할 수 있습니다. 그리고 Azure Security Center에서 모니터링하는 서버 엔드포인트에 대한 고급 공격을 검색하고 적절하게 대응할 수 있습니다.

## <a name="microsoft-defender-atp-features-in-security-center"></a>보안 센터에서 마이크로 소프트 디펜더 ATP 기능

마이크로소프트 디펜더 ATP를 사용 하는 경우, 당신은 얻을:

- **고급 위반 후 감지 센서**: Windows 서버용 Microsoft Defender ATP 센서는 방대한 양의 행동 신호를 수집합니다.

- **분석 기반의 클라우드 기반 사후 침해 감지**: Microsoft Defender ATP는 변화하는 위협에 신속하게 적응합니다. 고급 분석 기능과 빅 데이터를 활용합니다. Microsoft Defender ATP는 Windows, Azure 및 Office에서 알 수 없는 위협을 탐지하는 신호가 있는 지능형 보안 그래프의 강력한 힘으로 증폭됩니다. 신속하게 대응할 수 있도록 조치 가능한 경고를 제공합니다.

- **위협 인텔리전스**: Microsoft Defender ATP는 공격자 도구, 기술 및 절차를 식별할 때 경고를 생성합니다. 이 과정에서는 Microsoft 위협 확인 담당자와 보안 팀에서 생성하며 파트너가 제공한 정보를 통해 강화된 데이터를 활용합니다.

이제 Azure 보안 센터에서 다음 기능을 사용할 수 있습니다.

- **자동 온보딩**: Azure 보안 센터에 온보딩된 Windows 서버(Windows Server 2019를 실행하는 서버 제외)에 대해 Microsoft Defender ATP 센서가 자동으로 활성화됩니다.

- **유리의 단일 창**: Azure 보안 센터 콘솔에는 Microsoft Defender ATP 경고가 표시됩니다.

- **자세한 시스템 조사**: Azure 보안 센터 고객은 Microsoft Defender ATP 콘솔을 사용하여 자세한 조사를 수행하여 위반 범위를 파악할 수 있습니다.

![경고 목록 및 각 경고 관련 일반 정보가 표시된 Azure Security Center](media/security-center-wdatp/image1.png)

자세한 조사를 위해 Microsoft 수비수 ATP를 사용합니다. Microsoft Defender ATP는 경고 프로세스 트리 및 인시던트 그래프와 같은 추가 정보를 제공합니다. 그리고 이전 기간(최대 6개월 전)의 모든 동작이 표시되는 세부 컴퓨터 타임라인도 확인할 수 있습니다.

![경고에 대한 자세한 정보가 있는 Microsoft Defender ATP 페이지](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>플랫폼 지원

보안 센터의 Microsoft Defender ATP는 Azure VM의 경우 표준 계층 구독이 필요하고 Azure VM이 아닌 경우 작업 영역 수준에서만 표준 계층이 필요한 Windows 서버 2016, 2012 R2 및 2008 R2 SP1에서 검색을 지원합니다.

> [!NOTE]
> Azure 보안 센터를 사용하여 서버를 모니터링하면 Microsoft Defender ATP 테넌트가 자동으로 만들어지고 Microsoft Defender ATP 데이터가 기본적으로 유럽에 저장됩니다. 데이터를 다른 위치로 이동해야 하는 경우 Microsoft 지원에 문의하여 테넌트를 재설정해야 합니다. Office 365 GCC 고객의 경우 이러한 통합을 활용하는 서버 엔드포인트 모니터링이 비활성화되었습니다.

## <a name="onboarding-servers-to-security-center"></a>Security Center에 서버 등록 

서버를 보안 센터에 온보딩하려면 Azure 보안 센터로 이동하여 Microsoft Defender ATP 서버 온보딩에서 **온보딩 서버를 온보딩합니다.**

1. **온보딩** 영역에서 데이터를 저장할 작업 영역을 선택하거나 만듭니다. <br>
2. 모든 작업 영역을 볼 수 없는 경우 사용 권한이 부족하여 작업 영역이 Azure 보안 표준 계층으로 설정되어 있는지 확인합니다. 자세한 내용은 [보안 강화를 위해 보안 센터의 표준 계층으로 업그레이드를](security-center-pricing.md)참조하십시오.
    
3. 로그 분석 에이전트를 설치하는 방법에 대한 지침을 보려면 **서버 추가를** 선택합니다. 

4. 온보딩 후 **Compute 및 앱에서**컴퓨터를 모니터링할 수 있습니다.

   ![컴퓨터 등록](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>마이크로소프트 디펜더 ATP 통합 사용

Microsoft Defender ATP 통합이 활성화되어 있는지 확인하려면 **보안 센터** > 가격 & 설정을 선택하면 구독을 클릭할 >**있습니다.**
여기서 현재 사용하도록 설정된 통합을 확인할 수 있습니다.

  ![Microsoft Defender ATP 통합을 사용하도록 설정한 Azure 보안 센터 위협 감지 설정 페이지](media/security-center-wdatp/enable-integrations.png)

- Azure Security Center 표준 계층에 서버를 이미 온보딩한 경우에는 추가로 작업을 수행할 필요가 없습니다. Azure 보안 센터는 자동으로 Microsoft 수비수 ATP에 서버를 온보딩합니다. 온보딩은 최대 24시간이 소요될 수 있습니다.

- Azure Security Center 표준 계층에 서버를 온보딩하지 않았다면 일반적인 방법으로 Azure Security Center에 서버를 온보딩합니다.

- Microsoft Defender ATP를 통해 서버를 온보딩한 경우 다음을 수행합니다.
  - [서버 컴퓨터를 온보딩하는 방법](https://go.microsoft.com/fwlink/p/?linkid=852906)의 설명서에서 지침을 참조합니다.
  - Azure Security Center에 해당 서버를 온보딩합니다.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>마이크로소프트 수비수 ATP 포털에 대 한 액세스

[포털에 대한 사용자 액세스 권한 할당](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)의 지침을 따릅니다.

## <a name="set-the-firewall-configuration"></a>방화벽 구성 설정

Microsoft Defender ATP 센서가 시스템 컨텍스트에서 연결하므로 익명 트래픽을 차단하는 프록시 또는 방화벽이 있는 경우 익명 트래픽이 허용되는지 확인합니다. [프록시 서버에서 Microsoft Defender ATP 서비스 URL에 대한 액세스 사용의](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)지침을 따릅니다.

## <a name="test-the-feature"></a>기능 테스트

양성 Microsoft Defender ATP 테스트 경고를 생성하려면 다음을 수행하십시오.

1. 폴더 'C:\테스트-MDATP 테스트'를 만듭니다.

1. 원격 데스크톱을 사용하여 Windows Server 2012 R2 VM 또는 Windows Server 2016 VM에 액세스합니다. 명령줄 창을 엽니다.

1. 다음 명령을 복사하여 프롬프트에서 실행합니다. 명령 프롬프트 창이 자동으로 닫힙니다.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![위의 명령을 입력한 명령 프롬프트 창](media/security-center-wdatp/image4.jpeg)

1. 명령이 성공하면 Azure 보안 센터 대시보드 및 Microsoft Defender ATP 포털에 새 경고가 표시됩니다. 이 경고가 표시되려면 몇 분 정도 걸릴 수 있습니다.

1. 보안 센터에서 경고를 검토하려면 **보안 경고** > **의심스러운 PowerShell CommandLine로**이동합니다.

1. 조사 창에서 링크를 선택하여 Microsoft Defender ATP 포털로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center에서 지원하는 기능 및 플랫폼](security-center-os-coverage.md)
- [Azure 보안 센터에서 보안 정책 설정](tutorial-security-policy.md): Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md): 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
- [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.