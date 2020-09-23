---
title: Microsoft Defender Advanced Threat Protection-Azure Security Center
description: 이 문서에서는 Azure Security Center와 Microsoft Defender Advanced Threat Protection 간의 통합을 소개 합니다.
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
ms.openlocfilehash: 28242341ddd21adea33e56c3e1f35f0677e5921a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907365"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Azure Security Center으로 Microsoft Defender Advanced Threat Protection

Azure Security Center는 [Microsoft DEFENDER ATP (Advanced Threat Protection)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) 와 통합 되어 포괄적인 Edr (끝점 검색 및 응답) 기능을 제공 합니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|[Azure Defender](security-center-pricing.md) 필요|
|지원 되는 컴퓨터:|![예](./media/icons/yes-icon.png) Windows를 실행 하는 Azure 컴퓨터<br>![예](./media/icons/yes-icon.png) Windows를 실행 하는 Azure Arc 컴퓨터|
|필요한 역할 및 사용 권한:|통합을 사용 하거나 사용 하지 않도록 설정 하려면 **보안 관리자** 또는 **소유자** 입니다.<br>Security Center에서 MDATP 경고를 보려면 **보안 읽기 권한자**, **읽기 권한자**, **리소스 그룹 참가자**, **리소스 그룹 소유자**, **보안 관리자**, **구독 소유자**또는 **구독 참가자** 입니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드.<br>![아니요](./media/icons/no-icon.png) 공용 Azure 클라우드에서 워크 로드를 실행 하는 GCC 고객<br>![예](./media/icons/yes-icon.png) US Gov<br>![아니요](./media/icons/no-icon.png) 중국 .Gov, 기타 .Gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Security Center의 Microsoft Defender ATP 기능

Microsoft Defender ATP는 다음을 제공 합니다.

- **고급 위반 검색 센서**: Windows server 용 MICROSOFT Defender ATP 센서는 수많은 동작 신호를 수집 합니다.

- **분석 기반 클라우드 기반 사후 위반 검색**: MICROSOFT Defender ATP는 위협 변화에 빠르게 적응 합니다. 고급 분석 기능과 빅 데이터를 활용합니다. Microsoft Defender ATP는 Windows, Azure 및 Office에서 신호를 통해 Intelligent Security Graph 기능을 활용 하 여 알 수 없는 위협을 감지 합니다. 신속하게 대응할 수 있도록 조치 가능한 경고를 제공합니다.

- **위협 인텔리전스**: MICROSOFT Defender ATP는 공격자 도구, 기술 및 절차를 식별할 때 경고를 생성 합니다. 이 과정에서는 Microsoft 위협 확인 담당자와 보안 팀에서 생성하며 파트너가 제공한 정보를 통해 강화된 데이터를 활용합니다.


Azure Security Center와 Defender ATP를 통합 하 여 다음과 같은 추가 기능을 이용할 수도 있습니다.

- **자동 온 보 딩**: 통합은 Azure Security Center에서 모니터링 하는 windows server에 대해 MICROSOFT Defender ATP 센서를 자동으로 사용 하도록 설정 합니다 (windows Server 2019를 실행 하는 경우 제외).

- **단일 창**: Azure Security Center 콘솔에 MICROSOFT Defender ATP 경고가 표시 됩니다. 추가로 조사 하려면 Microsoft Defender ATP를 사용 합니다. Microsoft Defender ATP는 경고 프로세스 트리 및 인시던트 그래프와 같은 추가 정보를 제공 합니다. 그리고 이전 기간(최대 6개월 전)의 모든 동작이 표시되는 세부 컴퓨터 타임라인도 확인할 수 있습니다.

    ![경고에 대 한 자세한 정보가 포함 된 Microsoft Defender ATP 페이지](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>플랫폼 지원

Security Center의 Microsoft Defender ATP는 Windows Server 2016, 2012 R2 및 2008 R2 s p 1에 대 한 검색을 지원 합니다. Azure Vm의 경우 구독에서 Azure Defender를 사용 하도록 설정 하 고, 비 Azure Vm의 경우 작업 영역 수준 에서만 Azure Defender를 사용 하도록 설정 해야 합니다.

이 통합을 사용한 서버 끝점 모니터링은 Office 365 GCC 고객에 대해 사용 하지 않도록 설정 되었습니다.

## <a name="data-storage-location"></a>데이터 저장소 위치

Azure Security Center를 사용 하 여 서버를 모니터링 하는 경우 Microsoft Defender ATP 테 넌 트가 자동으로 만들어집니다. Microsoft Defender ATP에 의해 수집 된 데이터는 프로 비전 중에 식별 된 테 넌 트의 지리적 위치에 저장 됩니다. Pseudonymized 양식의 고객 데이터는 미국의 중앙 저장소 및 처리 시스템에도 저장 될 수 있습니다. 

데이터를 저장할 위치가 구성되면 변경할 수 없습니다. 데이터를 다른 위치로 이동 해야 하는 경우 Microsoft 지원에 문의 하 여 테 넌 트를 다시 설정 합니다.


## <a name="onboard-servers-to-security-center"></a>Security Center에 서버 등록 

Security Center에 서버를 등록 하려면 이동을 클릭 하 여 Microsoft Defender ATP 서버 온 보 딩 서버에서 등록 **Azure Security Center** 합니다.

1. **온 보 딩** 영역에서 데이터를 저장할 작업 영역을 선택 하거나 만듭니다.

2. 모든 작업 영역을 볼 수 없는 경우 권한이 부족 하기 때문일 수 있습니다. Azure Defender에서 작업 영역을 보호 하 고 있는지 확인 하세요.
    
3. Log Analytics 에이전트를 설치 하는 방법에 대 한 지침을 보려면 **서버 추가** 를 선택 합니다. 

4. 온 보 딩 후에 [asset inventory](asset-inventory.md)에서 컴퓨터를 모니터링할 수 있습니다.

   ![컴퓨터 등록](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Microsoft Defender ATP 통합 사용

Microsoft Defender ATP 통합이 사용 하도록 설정 되어 있는지 확인 하려면 **Security center**  >  **가격 책정 & 설정** > 선택 하 여 구독을 선택 합니다.

여기서 현재 사용하도록 설정된 통합을 확인할 수 있습니다.

  ![Microsoft Defender ATP 통합을 사용 하도록 설정 된 Azure Security Center 위협 검색 설정 페이지](media/security-center-wdatp/enable-integrations.png)

- Azure Defender를 이미 사용 하도록 설정한 경우에는 추가 작업이 필요 하지 않습니다. Azure Security Center은 자동으로 Microsoft Defender ATP에 서버를 등록 합니다. 온 보 딩은 최대 24 시간이 걸릴 수 있습니다.

- Azure Security Center 서버를 등록 하지 않은 경우에는 Azure Security Center로 등록 하 고 평소와 같이 Azure Defender를 사용 하도록 설정 합니다.

- Microsoft Defender ATP를 통해 서버를 등록 경우:
  - [서버 컴퓨터를 온보딩하는 방법](https://go.microsoft.com/fwlink/p/?linkid=852906)의 설명서에서 지침을 참조합니다.
  - Azure Security Center에 해당 서버를 온보딩합니다.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Microsoft Defender ATP 포털에 대 한 액세스

1. [포털에 대한 사용자 액세스 권한 할당](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)의 지침을 따릅니다.

1. 익명 트래픽을 차단 하는 프록시 또는 방화벽이 있는지 확인 합니다. Defender ATP 센서는 시스템 컨텍스트에서 연결 되므로 익명 트래픽이 허용 되어야 합니다. Microsoft Defender ATP 포털에 대 한 되어야 액세스를 보장 하려면 [프록시 서버에서 Microsoft DEFENDER atp 서비스 url에 대 한 액세스 사용](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)의 지침을 따르세요.

## <a name="test-the-feature"></a>기능 테스트

무해 한 Microsoft Defender ATP 테스트 경고를 생성 하려면:

1. ' C:\test-MDATP-test ' 폴더를 만듭니다.

1. 원격 데스크톱을 사용하여 Windows Server 2012 R2 VM 또는 Windows Server 2016 VM에 액세스합니다. 명령줄 창을 엽니다.

1. 다음 명령을 복사하여 프롬프트에서 실행합니다. 명령 프롬프트 창이 자동으로 닫힙니다.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![위의 명령을 입력한 명령 프롬프트 창](media/security-center-wdatp/image4.jpeg)

1. 명령이 성공 하면 Azure Security Center 대시보드 및 Microsoft Defender ATP 포털에 새 경고가 표시 됩니다. 이 경고가 표시되려면 몇 분 정도 걸릴 수 있습니다.

1. Security Center에서 경고를 검토 하려면 **보안 경고**  >  **의심 스러운 PowerShell 명령줄**로 이동 합니다.

1. 조사 창에서 링크를 선택 하 여 Microsoft Defender ATP 포털로 이동 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center에서 지원하는 기능 및 플랫폼](security-center-os-coverage.md)
- [Azure Security Center 보안 정책 설정](tutorial-security-policy.md): Azure 구독 및 리소스 그룹에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md): 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
- [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.