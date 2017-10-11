---
title: "Azure 로그 통합 시작 | Microsoft Docs"
description: "Azure 로그 통합 서비스를 설치하고 Azure 저장소, Azure 감사 로그 및 Azure 보안 센터 경고의 로그를 통합하는 방법에 대해 배웁니다."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9d39ecd513386b75b4b640721f80991caaf9ade8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure 로그 통합과 Azure 진단 로깅 및 Windows 이벤트 전달
AzLog(Azure 로그 통합)은 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있도록 합니다. 이 통합을 통해 응용 프로그램과 관련된 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 보안 대시보드를 사용할 수 있습니다.
>[!NOTE]
Azure 로그 통합에 대한 자세한 내용은 [Azure 로그 통합 개요](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview)를 참조하세요.

이 문서를 통해 Azlog 서비스의 설치와 Azure 진단과의 통합에 초점을 맞춰 Azure 로그 통합을 시작할 수 있습니다. Azure 로그 통합 서비스를 사용하면 Azure IaaS에 배포된 가상 컴퓨터에서 Windows 보안 이벤트 채널의 Windows 이벤트 로그 정보를 수집할 수 있습니다. 이 서비스는 온-프레미스에서 사용되는 "이벤트 전달"과 매우 비슷합니다.

>[!NOTE]
>Azure 로그 통합의 출력을 SIEM에 가져오는 기능은 SIEM 자체에서 제공됩니다. 자세한 내용은 [Integrating Azure Log Integration with your On-premises SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)(Azure 로그 통합과 온-프레미스 SIEM 통합)을 참조하세요.

Azure 로그 통합 서비스는 Windows Server 2008 R2 이상의 운영 체제(Windows Server 2012 R2 또는 Windows Server 2016 권장)를 사용하는 물리적 또는 가상 컴퓨터에서 실행됩니다.

물리적 컴퓨터는 온-프레미스(또는 호스팅 사이트)에서 실행할 수 있습니다. 가상 컴퓨터에서 Azure 로그 통합 서비스를 실행하도록 선택한 경우 해당 가상 컴퓨터는 온-프레미스 또는 Microsoft Azure와 같은 공용 클라우드에 위치할 수 있습니다.

물리적 또는 가상 컴퓨터에서 Azure 로그 통합 서비스를 실행하려면 Azure 공용 클라우드에 대한 네트워크 연결이 필요합니다. 이 문서의 단계에서는 구성에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건
AzLog를 설치하려면 최소한 다음 항목이 필요합니다.
* **Azure 구독**. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
* Microsoft Azure 진단 로깅에 사용할 수 있는 **저장소 계정**(사전 구성된 저장소 계정을 사용하거나 새 저장소 계정을 만들 수 있습니다. 저장소 계정을 구성하는 방법은 이 문서 뒷부분에서 설명합니다.)
  >[!NOTE]
  시나리오에 따라 저장소 계정이 필요하지 않을 수도 있습니다. 이 문서에서 다루는 Azure 진단 시나리오에는 저장소 계정이 필요합니다.
* **두 대의 시스템**: Azure 로그 통합 서비스를 실행하는 컴퓨터와, 모니터링되어 로깅 정보가 Azlog 서비스 컴퓨터로 전송되는 컴퓨터입니다.
   * 모니터링할 컴퓨터 – [Azure 가상 컴퓨터](../virtual-machines/virtual-machines-windows-overview.md)로 실행 중인 VM
   * Azure 로그 통합 서비스를 실행할 컴퓨터 - 이 컴퓨터는 나중에 SIEM으로 가져올 모든 로그 정보를 수집합니다.
    * 이 시스템은 온-프레미스 또는 Microsoft Azure에 위치할 수 있습니다.  
    * Windows Server 2008 R2 SP1 이상의 x64 버전을 실행하고 .NET 4.5.1이 설치되어 있어야 합니다. [방법: 설치된 .NET Framework 버전 확인](https://msdn.microsoft.com/library/hh925568) 문서에 따라 설치된 .NET 버전을 확인할 수 있습니다.  
    Azure 진단 로깅에 사용되는 Azure Storage 계정에 대한 연결이 있어야 합니다. 이 문서의 뒷부분에서 이 연결을 확인하는 방법에 대한 지침을 제공합니다.

Azure Portal을 사용하여 가상 컴퓨터를 만드는 프로세스의 빠른 데모를 보려면 아래 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>배포 고려 사항
Azure 로그 통합을 테스트하는 동안 최소 운영 체제 요구 사항을 충족하는 시스템을 사용할 수 있습니다. 그러나 프로덕션 환경의 경우 로드 시 확장 계획을 세워야 할 수도 있습니다.

이벤트 양이 많으면 Azure 로그 통합 서비스의 여러 인스턴스(물리적 또는 가상 컴퓨터당 하나의 인스턴스)를 실행할 수 있습니다. 또한 WAD(Windows용 Azure 진단) 저장소 계정의 부하를 분산할 수 있으며 인스턴스에 제공할 구독의 수는 용량을 기반으로 해야 합니다.
>[!NOTE]
현재 Azure 로그 통합 컴퓨터(즉, Azure 로그 통합 서비스를 실행하는 컴퓨터)의 인스턴스를 확장할 시기 또는 저장소 계정이나 구독에 대한 구체적인 권장 사항은 없습니다. 확장 결정은 이러한 각 영역에서의 성능 관찰을 기반으로 해야 합니다.

Azure 로그 통합 서비스를 확장하여 성능을 향상시킬 수도 있습니다. 다음 성능 메트릭은 Azure 로그 통합 서비스를 실행하기 위해 선택한 컴퓨터의 크기를 조정하는 데 도움이 될 수 있습니다.
* 8-프로세서(코어) 컴퓨터에서는 Azlog 통합자의 인스턴스 하나가 하루에 약 2400만 이벤트를 처리할 수 있습니다(~1M/시간).

* 4-프로세서(코어) 컴퓨터에서는 Azlog 통합자의 인스턴스 하나가 하루에 약 150만 이벤트를 처리할 수 있습니다(~62.5K/시간).

## <a name="install-azure-log-integration"></a>Azure 로그 통합 설치
Azure 로그 통합을 설치하려면 [Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324) 설치 파일을 다운로드해야 합니다. 설치 루틴을 실행하고 Microsoft에 원격 분석 정보를 제공할지 결정합니다.  

![원격 분석 확인란이 선택된 설치 화면](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> Microsoft가 원격 분석 데이터를 수집하도록 허용하는 것이 좋습니다. 이 옵션을 선택 취소하여 원격 분석 데이터의 컬렉션을 해제할 수 있습니다.
>


Azure 로그 통합 서비스는 서비스가 설치된 컴퓨터에서 원격 분석 데이터를 수집합니다.  

수집된 원격 분석 데이터는 다음과 같습니다.

* Azure 로그 통합 실행 중에 발생하는 예외
* 처리된 쿼리 및 이벤트 수에 대한 메트릭
* 어떤 Azlog.exe 명령줄 옵션이 사용되었는지에 대한 통계

설치 프로세스는 아래 비디오에 나와 있습니다.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>사후 설치 및 유효성 검사 단계
기본 설치 루틴을 완료한 후에는 사후 설치 및 유효성 검사 단계를 수행할 수 있습니다.
1. 관리자 권한 PowerShell 창을 열고 **c:\Program Files\Microsoft Azure Log Integration**으로 이동합니다.
2. 제일 먼저 수행할 단계는 AzLog Cmdlet을 가져오는 것입니다. AzLog Cmdlet을 가져오려면 **LoadAzlogModule.ps1**(다음 명령의 ".\" 주의) 스크립트를 실행합니다. **.\LoadAzlogModule.ps1**을 입력하고 **Enter** 키를 누릅니다.  
아래 그림과 같이 표시됩니다. </br></br>
![원격 분석 확인란이 선택된 설치 화면](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. 이제 특정 Azure 환경을 사용하도록 AzLog를 구성해야 합니다. "Azure 환경"은 작업하려는 Azure 클라우드 데이터 센터의 "유형"입니다. Azure 환경이 여러 개 있지만 현재 관련 옵션은 **AzureCloud** 또는 **AzureUSGovernment**입니다.   관리자 권한 PowerShell 환경에서 현재 **c:\program files\Microsoft Azure Log Integration\** 위치인지 확인합니다. </br></br>
    위치를 확인한 후에는 다음 명령을 실행합니다. </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud``(Azure Commercial의 경우)

      >[!NOTE]
      명령이 성공하면 알림이 나타나지 않습니다.  US Government Azure 클라우드를 사용하려면 미국 정부 클라우드에 대해 **AzureUSGovernment**(-Name 변수의 경우)를 사용합니다. 현재 다른 Azure 클라우드는 지원되지 않습니다.  
4. 시스템을 모니터링하려면 Azure 진단에 사용 중인 저장소 계정의 이름이 필요합니다.  Azure Portal에서 **가상 컴퓨터**로 이동하여 모니터링할 가상 컴퓨터를 찾습니다. **속성** 섹션에서 **진단 설정**을 선택합니다.  **에이전트**를 클릭하고 지정된 저장소 계정 이름을 기록합니다. 이후 단계에서 이 계정 이름이 필요합니다.
![Azure 진단 설정](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Azure 진단 설정](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      가상 컴퓨터를 만드는 동안 모니터링 기능이 사용하지 않도록 설정된 경우 위와 같이 모니터링을 사용하도록 설정하는 옵션이 제공됩니다.
5. 이제 Azure 로그 통합 컴퓨터를 다시 살펴보겠습니다. Azure 로그 통합을 설치한 시스템에서 저장소 계정에 연결되어 있는지 확인해야 합니다. Azure 로그 통합 서비스를 실행하는 물리적 컴퓨터 또는 가상 컴퓨터에서 모니터링되는 각 시스템에 구성된 대로 Azure 진단이 기록한 정보를 검색하려면 저장소 계정에 대한 액세스 권한이 필요합니다.  
  1. [여기](http://storageexplorer.com/)에서 Azure Storage 탐색기를 다운로드할 수 있습니다.
  2. 설치 루틴을 통해 실행
  3. 설치가 완료되면 **다음**을 클릭하고 **Launch Microsoft Azure Storage Explorer**(Microsoft Azure Storage 탐색기 시작) 확인란을 선택합니다.  
  4. Azure에 로그인합니다.
  5. Azure 진단을 위해 구성된 저장소 계정이 표시되는지 확인합니다.  
![저장소 계정](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. 저장소 계정에는 몇 가지 옵션이 있습니다. 그중 하나가 **테이블**입니다. **테이블**에 **WADWindowsEventLogsTable**이 표시되어야 합니다. </br></br>
   ![저장소 계정](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Azure 진단 로깅 통합
이 단계에서는 로그 파일이 포함된 저장소 계정에 연결하도록 Azure 로그 통합 서비스를 실행하는 컴퓨터를 구성합니다.
이 단계를 완료하려면 몇 가지 항목이 필요합니다.  
* **FriendlyNameForSource:** Azure 진단의 정보를 저장하도록 가상 컴퓨터를 구성한 저장소 계정에 적용할 수 있는 친숙한 이름입니다.
* **StorageAccountName:** Azure 진단을 구성할 때 지정된 저장소 계정의 이름입니다.  
* **StorageKey:** Azure 진단 정보가 이 가상 컴퓨터에 저장되는 저장소 계정의 저장소 키입니다.  

저장소 키를 가져오려면 다음 단계를 수행합니다.
 1. [Azure Portal](http://portal.azure.com)로 이동합니다.
 2. Azure 콘솔의 탐색 창에서 아래쪽으로 스크롤하여 **추가 서비스**를 클릭합니다.

 ![추가 서비스](./media/security-azure-log-integration-get-started/more-services.png)
 3. **필터** 텍스트 상자에 **저장소**를 입력합니다. **저장소**를 입력한 뒤 나타나는 **저장소 계정**을 클릭합니다.

   ![필터 상자](./media/security-azure-log-integration-get-started/filter.png)
 4. 저장소 계정 목록이 나타납니다. 로그 저장소에 할당한 계정을 두 번 클릭합니다.

   ![저장소 계정 목록](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. **설정** 섹션에서 **액세스 키**를 클릭합니다.

  ![액세스 키](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. **key1**을 복사하여 다음 단계에서 액세스할 수 있는 안전한 위치에 저장합니다.

   ![두 개의 액세스 키](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Azure 로그 통합을 설치한 서버에서 관리자 권한 명령 프롬프트를 엽니다. 여기서는 PowerShell 콘솔이 아닌 명령 프롬프트 창을 사용하고 있음에 유의하세요.
 8. **c:\Program Files\Microsoft Azure Log Integration**으로 이동합니다.
 9. ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` 실행 </br> 예: ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` 이벤트XML에 구독 ID를 표시하려면 다음의 식별 이름에 구독 ID를 추가합니다. ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` 또 다른 예: ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
최대 60분 정도 기다렸다가 저장소 계정에서 가져온 이벤트를 확인합니다. 이벤트를 보려면 Azlog 통합자에서 **이벤트 뷰어 > Windows 로그 > 전달된 이벤트**를 엽니다.

여기에서 위에 설명된 단계를 진행하는 비디오를 시청할 수 있습니다.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>전달된 이벤트 폴더에 데이터가 나타나지 않으면 어떻게 하나요?
1시간이 지난 후에도 **전달된 이벤트** 폴더에 데이터가 나타나지 않으면 다음 조치를 수행합니다.

1. Azure 로그 통합 서비스를 실행 중인 컴퓨터를 확인하고 Azure에 액세스할 수 있는지 확인합니다. 연결을 테스트하려면 브라우저에서 [Azure 포털](http://portal.azure.com) 을 열어봅니다.
2. 사용자 계정 **Azlog**가 **users\Azlog** 폴더에 대한 쓰기 권한이 있어야 합니다.
  <ol type="a">
   <li>**Windows 탐색기** 를 엽니다.</li>
  <li> **c:\users** 로 이동합니다.</li>
  <li> 마우스 오른쪽 단추로 **c:\users\Azlog** 를 클릭합니다.</li>
  <li> **보안**  을 클릭합니다.</li>
  <li> **NT Service\Azlog**를 클릭하고 계정의 사용 권한을 확인합니다. 이 탭에서 계정이 누락되거나 현재 적절한 사용 권한이 표시되지 않은 경우 이 탭에서 계정 권한을 부여할 수 있습니다.</li>
  </ol>
3. **Azlog source list** 명령을 실행하면 **Azlog source add** 명령에서 추가된 저장소 계정이 나열되는지 확인합니다.
4. Azure 로그 통합에서 보고된 오류가 있는지 보려면 **이벤트 뷰어 > Windows 로그 > 응용 프로그램**으로 이동합니다.


설치 및 구성 중에 문제가 발생하면 [지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 열고 **로그 통합**을 지원을 요청하는 서비스로 선택합니다.

또 다른 지원 옵션은 [Azure 로그 통합 MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)입니다. 여기서 커뮤니티는 Azure 로그 통합을 최대한 활용하는 방법에 대한 질문, 답변, 팁, 요령 등을 통해 서로 지원할 수 있습니다. 또한 Azure 로그 통합 팀이 이 포럼을 모니터링하며 가능한 한 언제든지 도움을 드릴 것입니다.

## <a name="next-steps"></a>다음 단계
Azure 로그 통합에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 로그에 대한 Microsoft Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 다운로드할 수 있습니다.
* [Azure 로그 통합 소개](security-azure-log-integration-overview.md) - 이 문서에서는 Azure 로그 통합, 주요 기능 및 작동 원리를 소개합니다.
* [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다. 그리고 SIEM 구성 요소를 구성하는 방법에 대한 최신 지침을 제공합니다. 먼저 SIEM 공급업체에 추가 세부 정보를 확인하세요.
* [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
* [Security Center 경고를 Azure 로그 통합과 통합](../security-center/security-center-integrating-alerts-with-log-integration.md) - 이 문서에서는 Azure 진단 및 Azure 활동 로그에 수집된 가상 컴퓨터 보안 이벤트와 함께 Security Center 경고를 로그 분석 또는 SIEM 솔루션과 동기화하는 방법을 보여줍니다.
* [Azure 진단 및 Azure 감사 로그를 위한 새 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.
