---
title: App Service 환경 사용
description: App Service Environment를 사용 하 여 격리 된 응용 프로그램을 호스트 하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3679bf9d55ddccefddb4bf3b2a96ec1b427315af
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663660"
---
# <a name="using-an-app-service-environment"></a>App Service Environment 사용

ASE (App Service Environment)는 사용자가 선택한 Azure Virtual Network (VNet)에 직접 삽입 하는 Azure App Service의 단일 테 넌 트 배포입니다. 한 고객 에게만 사용 되는 시스템입니다. ASE에 배포 된 앱은 ASE 서브넷에 적용 되는 네트워킹 기능을 적용 합니다. 이러한 네트워킹 기능을 적용 하려면 앱에서 사용 하도록 설정 해야 하는 추가 기능이 없습니다. 

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기

ASE에서 앱을 만들려면 일반적으로 앱을 만들 때와 동일한 프로세스를 사용 하지만 몇 가지 작은 차이점이 있습니다. 새 App Service 계획을 만들 때는 다음을 수행합니다.

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만든 모든 App Service 계획은 Isolated v2 가격 책정 계층에만 있을 수 있습니다.

ASE가 없는 경우 [App Service Environment 만들기][MakeASE]의 지침에 따라 만들 수 있습니다.

ASE에서 앱을 만들려면

1. **리소스 만들기**  >  **웹 + 모바일**  >  **웹 앱** 을 선택 합니다.

1. 구독을 선택합니다.

1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용** 을 선택하고 드롭다운 목록에서 이름을 선택합니다.

1. 앱의 이름을 입력합니다. ASE에서 App Service 계획을 이미 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영 됩니다.

    ![ASE에서 앱 만들기][1]

1. 게시 유형, 스택 및 운영 체제를 선택 합니다.

1.  지역을 선택 합니다. 여기에서 기존 App Service Environment v3을 선택 해야 합니다.  앱을 만드는 동안에는 ASEv3를 만들 수 없습니다. 

1. ASE에서 기존 App Service 계획을 선택 하거나 새 계획을 만듭니다. 새 앱을 만드는 경우 App Service 계획에 대해 원하는 크기를 선택 합니다. 앱에 대해 선택할 수 있는 유일한 SKU는 Isolated v2 가격 책정 SKU입니다.

    ![Isolated v2 가격 책정 계층][2]

    > [!NOTE]
    > Linux 앱 및 Windows 앱은 동일한 App Service 계획에 있을 수 없지만 동일한 App Service Environment에 있을 수 있습니다.
    >

1. * * 다음: 모니터링 * * 앱에서 앱 정보를 사용 하도록 설정 하려는 경우 만들기 흐름 중에 여기에서 수행할 수 있습니다. 

1.  **다음: 태그** 를 선택 합니다. 태그를 앱에 추가 합니다.  

1. **검토 + 만들기** 를 선택 하 고 정보가 올바른지 확인 한 다음 **만들기** 를 선택 합니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식

모든 App Service 앱은 App Service 계획에서 실행됩니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱의 크기를 조정 하는 경우 동일한 계획의 App Service 계획과 모든 앱의 크기를 조정 합니다.

App Service 계획의 크기를 조정 하는 경우 필요한 인프라가 자동으로 추가 됩니다. 인프라를 추가 하는 동안 작업 크기를 조정 하는 데 시간이 지연 됩니다. App Service 계획의 크기를 조정할 때 동일한 OS 및 크기를 요청 하는 다른 크기 조정 작업은 첫 번째 크기 조정 작업이 완료 될 때까지 대기 합니다. 차단 크기 조정 작업이 완료 된 후에는 대기 중인 모든 요청을 동시에 처리 합니다. 크기 및 OS의 크기 조정 작업은 크기 및 OS의 다른 조합에 대 한 크기 조정을 차단 하지 않습니다. 예를 들어 Windows I2v2 App Service 계획의 크기를 조정한 경우 해당 ASE에서 Windows I2v2 크기를 조정 하는 다른 모든 요청은이 완료 될 때까지 큐에 대기 됩니다.   

다중 테 넌 트 App Service에서는 리소스 풀을 쉽게 지원할 수 있기 때문에 크기 조정이 즉시 실행 됩니다. ASE에는 이러한 버퍼가 없으며 리소스가 필요에 따라 할당 됩니다.

## <a name="app-access"></a>앱 액세스

ASE에서 앱을 만드는 데 사용 되는 도메인 접미사는 *입니다. &lt; asename &gt; . appserviceenvironment.net*. ASE 이름이 _내_ ase이 고 해당 ase에서 _contoso_ 라는 앱을 호스트 하는 경우 다음 url에서 해당 앱에 연결 합니다.

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

ASE를 만드는 방법에 대 한 자세한 내용은 [App Service Environment 만들기][MakeASE]를 참조 하세요.

SCM URL은 Kudu 콘솔에 액세스 하거나 웹 배포를 사용 하 여 앱을 게시 하는 데 사용 됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

### <a name="dns-configuration"></a>DNS 구성 

ASE는 인바운드 트래픽에 대해 개인 끝점을 사용 하며 Azure DNS 개인 영역을 사용 하 여 자동으로 구성 됩니다. 자체 DNS 서버를 사용 하려면 다음 레코드를 추가 해야 합니다.

1. &lt;ASE 이름&gt;.appserviceenvironment.net 영역 만들기
1. ASE 개인 끝점에서 사용 하는 인바운드 IP 주소를 가리키는 해당 영역에 A 레코드를 만듭니다.
1. ASE 개인 끝점에서 사용 하는 인바운드 IP 주소를 가리키는 해당 영역에 A 레코드를 만듭니다.
1. &lt;ASE 이름&gt;.appserviceenvironment.net에 scm이라는 영역 만들기
1. ASE 개인 끝점에서 사용 하는 IP 주소를 가리키는 scm 영역에 A 레코드를 만듭니다.

ASE 기본 도메인 접미사에 대 한 DNS 설정은 앱이 해당 이름 으로만 액세스할 수 있도록 제한 하지 않습니다. ASE에서 앱에 대 한 유효성 검사 없이 사용자 지정 도메인 이름을 설정할 수 있습니다. 그런 다음 *contoso.net* 이라는 영역을 만들려는 경우이를 수행 하 고 인바운드 IP 주소를 가리킬 수 있습니다. 사용자 지정 도메인 이름은 앱 요청에 대해서는 작동하지만 scm 사이트의 경우에는 작동하지 않습니다. Scm 사이트는 *&lt; appname &gt; . scm &lt; 에서만 사용할 수 있습니다. asename &gt; . appserviceenvironment.net*. 

## <a name="publishing"></a>게시

다중 테 넌 트 App Service와 마찬가지로 ASE에서 다음 메서드를 사용 하 여 게시할 수 있습니다.

- 웹 배포
- CI(연속 통합)
- Kudu 콘솔에서 끌어서 놓기
- IDE (예: Visual Studio, Eclipse 또는 IntelliJ 아이디어)

ASE를 사용 하 여 게시 끝점은 개인 끝점에서 사용 하는 인바운드 주소를 통해서만 사용할 수 있습니다. 개인 끝점 주소에 대 한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다.  또한 사용자의 Ide는 ILB에 대 한 네트워크 액세스 권한이 있어야 해당 사용자에 게 직접 게시할 수 있습니다.

추가 변경 없이, 게시 끝점은 인터넷에 액세스할 수 없기 때문에 GitHub 및 Azure DevOps와 같은 인터넷 기반 CI 시스템은 ILB ASE와 작동 하지 않습니다. ILB ASE가 포함 된 가상 네트워크에 자체 호스트 된 릴리스 에이전트를 설치 하 여 Azure DevOps에서 ILB ASE에 게시를 사용 하도록 설정할 수 있습니다. 

## <a name="storage"></a>스토리지

ASE에는 ASE의 모든 앱에 대해 1TB의 저장소가 있습니다. 격리 가격 SKU의 App Service 요금제는 250 GB로 제한 됩니다. ASE에서 250 GB의 저장소는 1TB 제한까지 App Service 계획 마다 추가 됩니다. 4 개 보다 많은 App Service 요금제를 사용할 수 있지만 1TB 제한을 초과 하는 저장소는 더 이상 추가 되지 않습니다.

## <a name="logging"></a>로깅

ASE를 Azure Monitor와 통합 하 여 ASE에 대 한 로그를 Azure Storage, Azure Event Hubs 또는 Log Analytics에 보낼 수 있습니다. 이러한 항목은 오늘 기록 됩니다.

| 상황 | 메시지 |
|---------|----------|
| ASE가 비정상입니다. | 잘못 된 가상 네트워크 구성으로 인해 지정 된 ASE가 비정상 상태입니다. 비정상 상태가 지속 되 면 ASE가 일시 중단 됩니다. 여기에 정의 된 지침을 참조 하세요 https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE 서브넷의 공간이 거의 부족 합니다. | 지정 된 ASE가 공간을 거의 벗어난 서브넷에 있습니다. {0}남은 주소가 있습니다. 이러한 주소가 모두 사용 되 면 ASE는 크기를 조정할 수 없습니다.  |
| ASE가 총 인스턴스 제한에 도달 함 | 지정 된 ASE가 ASE의 총 인스턴스 제한에 도달 했습니다. 현재 {0} 최대 201 인스턴스의 App Service 계획 인스턴스를 포함 합니다. |
| ASE가 종속성에 도달할 수 없습니다. | 지정 된 ASE에 연결할 수 없습니다 {0} .  여기에 정의 된 지침을 참조 하세요 https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE가 일시 중지 되었습니다. | 지정한 ASE가 일시 중지 되었습니다. ASE 일시 중단은 계정 shortfall 또는 잘못 된 가상 네트워크 구성으로 인해 발생할 수 있습니다. 근본 원인을 해결 하 고 ASE를 다시 시작 하 여 트래픽을 계속 처리 합니다. |
| ASE 업그레이드가 시작 되었습니다. | 지정 된 ASE로의 플랫폼 업그레이드가 시작 되었습니다. 크기 조정 작업에서 지연이 발생 합니다. |
| ASE 업그레이드가 완료 되었습니다. | 지정 된 ASE로의 플랫폼 업그레이드가 완료 되었습니다. |
| 크기 조정 작업이 시작 되었습니다. | App Service 계획 ( {0} )의 확장이 시작 되었습니다. 원하는 상태: {1} I {2} 작업자.
| 크기 조정 작업이 완료 되었습니다. | App Service 계획 ( {0} )의 확장이 완료 되었습니다. 현재 상태: {1} I {2} 작업자. |
| 크기 조정 작업이 실패 했습니다. | App Service 계획 ( {0} )의 크기를 조정 하지 못했습니다. 현재 상태: {1} I {2} 작업자. |

ASE에서 로깅을 사용 하도록 설정 하려면:

1. 포털에서 **진단 설정** 으로 이동 합니다.
1. **진단 설정 추가** 를 선택 합니다.
1. 로그 통합에 대 한 이름을 제공 합니다.
1. 원하는 로그 대상을 선택 하 고 구성 합니다.
1. **Appservice환경 Platformlogs** 를 선택 합니다.

![ASE 진단 로그 설정][4]

Log Analytics와 통합 하는 경우 ASE 포털에서 **로그** 를 선택 하 고 **Appservice환경 platformlogs** 에 대 한 쿼리를 만들어 로그를 볼 수 있습니다. 로그는 ASE에 트리거를 트리거하는 이벤트가 있는 경우에만 내보내집니다. ASE에 이러한 이벤트가 없는 경우 로그가 없는 것입니다. Log Analytics 작업 영역에서 로그의 예제를 신속 하 게 보려면 ASE의 App Service 계획 중 하나를 사용 하 여 크기 조정 작업을 수행 합니다. 그런 다음 **Appservice환경 Platformlogs** 에 대해 쿼리를 실행 하 여 해당 로그를 볼 수 있습니다. 

**경고 만들기**

로그에 대 한 경고를 만들려면 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-log.md)의 지침을 따르세요. 개요:

* ASE 포털에서 경고 페이지를 엽니다.
* **새 경고 규칙** 선택
* Log Analytics 작업 영역으로 사용할 리소스를 선택 합니다.
* "Appservice환경 Platformlogs"와 같은 쿼리를 사용 하도록 사용자 지정 로그 검색을 사용 하 여 조건을 설정 합니다. 여기서 ResultDescription은 "크기 조정 시작" 또는 원하는 모든 항목을 포함 합니다. 임계값을 적절 하 게 설정 합니다. 
* 원하는 대로 작업 그룹을 추가 하거나 만듭니다. 작업 그룹은 전자 메일 또는 SMS 메시지 전송과 같은 경고에 대 한 응답을 정의 하는 위치입니다.
* 경고 이름을로 하 고 저장 합니다.

## <a name="internal-encryption"></a>내부 암호화

App Service Environment는 시스템 내에서 내부 구성 요소나 통신을 볼 수 없는 블랙 박스 시스템으로 작동합니다. 높은 처리량을 활성화하기 위해 내부 구성 요소 사이에는 기본적으로 암호화가 사용되지 않습니다. 시스템은 트래픽을 모니터링하거나 액세스하는 데 완전히 액세스할 수 없기 때문에 보안이 유지됩니다. 종단 간 암호화에서 데이터 경로를 완벽 하 게 암호화 해야 하지만 준수 요구 사항이 있는 경우 ASE **구성** UI에서이를 사용 하도록 설정할 수 있습니다.

![내부 암호화 사용][5]

이렇게 하면 프런트 엔드 및 작업자 간에 ASE에서 내부 네트워크 트래픽을 암호화하고, 페이지 파일을 암호화하고, 작업자 디스크도 암호화합니다. InternalEncryption clusterSetting을 활성화하면 시스템 성능에 영향을 줄 수 있습니다. InternalEncryption을 활성화하도록 변경하는 경우 변경이 완전히 전파될 때까지 ASE는 불안정한 상태가 됩니다. ASE에 있는 인스턴스의 수에 따라 변경 내용의 전체 전파를 완료하는 데 몇 시간이 걸릴 수 있습니다. ASE를 사용하는 동안에는 이를 사용하도록 설정하지 않는 것이 좋습니다. 현재 사용 중인 ASE에서 이를 사용하도록 설정해야 하는 경우 작업이 완료될 때까지 백업 환경으로 트래픽을 전환하는 것이 좋습니다.

## <a name="upgrade-preference"></a>업그레이드 기본 설정

Ase 여러 개 있는 경우 일부 Ase을 다른 사용자 보다 먼저 업그레이드 해야 할 수 있습니다. ASE **HostingEnvironment 리소스 관리자** 개체 내에서 **upgradepreference** 설정에 대 한 값을 설정할 수 있습니다. **Upgradepreference** 설정은 템플릿, ARMClient 또는를 사용 하 여 구성할 수 있습니다 https://resources.azure.com . 가능한 세 가지 값은 다음과 같습니다.

- **없음**: Azure는 특정 일괄 처리 없이 ASE를 업그레이드 합니다. 이 값은 기본값입니다.
- **초기**: ASE는 App Service 업그레이드의 처음 절반에서 업그레이드 됩니다.
- **후기**: ASE는 App Service 업그레이드의 후반부에서 업그레이드 됩니다.

업그레이드 기본 설정을 구성 하려면 ASE **구성** UI로 이동 합니다. 

업그레이드 **기본 설정** 기능을 사용 하면 "초기" Ase "Ase" ase 이전에 업그레이드 되기 때문에 여러 개의이 있는 경우에 가장 적합 합니다. 여러 Ase이 있는 경우 개발 및 테스트 Ase를 "초기"로 설정 하 고 프로덕션 Ase "후기"로 설정 해야 합니다.

## <a name="delete-an-ase"></a>ASE 삭제

ASE를 삭제하려면 다음을 수행합니다.

1. **App Service Environment** 창의 위쪽에서 **삭제** 를 선택 합니다.

1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제 하면 해당 ASE 내의 모든 콘텐츠도 삭제 됩니다.

    ![ASE 삭제][3]

1. **확인** 을 선택합니다.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
