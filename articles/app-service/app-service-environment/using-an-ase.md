---
title: "Azure App Service Environment 사용"
description: "Azure App Service Environment에서 앱을 만들고 게시하고 확장하는 방법"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>App Service Environment 사용 #

## <a name="overview"></a>개요 ##

ASE(App Service Environment)는 Azure App Service를 고객의 Azure VNet(Virtual Network)에 있는 서브넷에 배포한 것입니다. ASE는 다음으로 구성됩니다.

- 프런트 엔드 - ASE에서 HTTP/HTTPS가 종료되는 위치입니다.
- 작업자 - 앱을 호스트하는 리소스입니다.
- 데이터베이스 - 환경을 정의하는 정보가 들어 있습니다.
- 저장소 - 저장소는 고객이 게시한 앱을 호스트하는 데 사용됩니다.

> [!NOTE]
> App Service Environment는 두 가지 버전(ASEv1 및 ASEv2)이 있습니다. ASEv1에서는 리소스를 사용하기 전에 관리해야 합니다. ASEv1을 구성하고 관리하는 방법을 알아보려면 [App Service Environment v1 구성][ConfigureASEv1]을 참조하세요. 이 문서의 나머지 부분은 ASEv2에 초점이 맞춰져 있습니다.
>
>

앱 액세스를 위한 외부 VIP 또는 앱 액세스를 위한 내부 VIP로 ASE(ASEv1 및 ASEv2)를 배포할 수 있습니다. 외부 VIP를 사용한 배포는 일반적으로 외부 ASE라고 하고, 내부 버전은 ILB(내부 부하 분산 장치)를 사용하므로 ILB ASE라고 합니다. ILB ASE에 대한 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="create-a-web-app-in-an-ase"></a>ASE에서 웹앱 만들기 ##

ASE에서 웹앱을 만드는 프로세스는 일반적으로 웹앱을 만드는 프로세스와 같지만, 몇 가지 작은 차이점이 있습니다. 새 App Service 계획을 만드는 경우:

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만들어진 모든 App Service 계획은 격리 가격 책정 계층에 있어야 합니다.

ASE가 없는 경우 [App Service Environment 만들기][MakeExternalASE]의 지침에 따라 만들 수 있습니다.

ASE에서 웹앱을 만들려면:

1. **새로 만들기 &gt; 웹 및 모바일**을 클릭하고 **웹앱**을 선택합니다.
2. 웹앱의 이름을 제공합니다. ASE에서 이미 App Service 계획을 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영됩니다.

    ![][1]

1. 구독을 선택합니다.
2. 새 리소스 그룹의 이름을 제공하거나 **기존 항목 사용**을 선택하고 드롭다운에서 선택합니다.
3. ASE에서 기존 App Service 계획을 선택하거나 다음 단계를 통해 새 App Service 계획을 만듭니다.
    1. **새로 만들기**를 선택합니다.
    2. App Service 계획의 이름을 제공합니다.
    3. **위치** 드롭다운에서 해당 ASE를 선택합니다.
    4. **격리** 가격 책정 계층을 선택합니다. **선택**을 클릭합니다.
    5. **확인**을 클릭합니다.
    
    ![][2]
1. **만들기**를 클릭합니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식 ##

모든 App Service 앱은 App Service 계획에서 실행됩니다. 컨테이너 모델에서는 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱을 확장하면 App Service 계획이 확장되므로 같은 계획 내에 있는 모든 앱이 확장됩니다.

ASEv2에서는 App Service 계획을 확장하면 필요한 인프라가 자동으로 추가됩니다. 이 점이 App Service 계획을 만들거나 규모 확장하기 전에 필요한 인프라를 추가해야 하는 ASEv1과 다른 점입니다. 즉, ASEv2에서는 인프라가 추가됨에 따라 확장 작업에 대한 시간 지연이 있습니다.

다중 테넌트 App Service에서 확장은 일반적으로 즉각적인데, 규모 확장을 지원하는 데 사용할 수 있는 손쉽게 사용 가능한 리소스 풀이 있기 때문입니다. ASE에는 필요 시 할당되는 이러한 버퍼와 리소스가 없습니다.

ASE에서는 최대 100개의 인스턴스를 확장할 수 있습니다. 이러한 100개의 인스턴스는 모두 하나의 App Service 계획에 있거나 여러 App Service 계획에 분산될 수 있습니다.

## <a name="ip-addresses"></a>IP 주소 ##

App Service에는 앱에 전용 IP 주소를 할당하는 기능이 있습니다. 이 기능은 [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩][ConfigureSSL]에 설명된 대로 IP 기반 SSL을 구성하여 사용할 수 있습니다. 그러나 ASE에는 ILB ASE에서 IP 기반 SSL에 사용할 IP 주소를 더 추가할 수 없다는 중요한 예외 사항이 있습니다.

ASEv1에서는 IP 주소를 사용하기 전에 IP 주소를 리소스로 할당해야 합니다. ASEv2에서는 다중 테넌트 App Service에서 하는 것처럼 앱에서 IP 주소를 사용하면 됩니다. ASEv2에는 최대 30개의 IP 주소까지 항상 여분의 주소가 하나 있습니다. IP 주소를 사용할 때마다 항상 손쉽게 사용할 수 있는 주소가 하나 있도록 주소가 하나 더 추가됩니다. 빠르게 연속으로 IP 주소를 추가하지 못하도록 다른 IP 주소를 할당하는 데 필요한 시간 지연이 있습니다.

## <a name="front-end-scaling"></a>프런트 엔드 확장 ##

ASEv2에서 App Service 계획을 규모 확장할 때 이러한 계획을 지원하도록 작업자가 자동으로 추가됩니다. 모든 ASE가 만들어지는 두 개의 프런트 엔드 외에, App Service 계획의 인스턴스 15개마다 하나의 프런트 엔드 비율로 프런트 엔드가 자동으로 규모 확장되기도 합니다. 즉, 인스턴스가 15개 있으면 프런트 엔드가 세 개 있습니다. 인스턴스 30개로 확장하면 프런트 엔드가 네 개 있는 식입니다.

이는 대부분의 시나리오에서 충분한 것 이상이지만, 더 빠른 비율로 규모 확장해야 할 경우 App Service 계획에서 인스턴스 5개마다 하나의 인스턴스로 비율을 변경할 수 있습니다. [Azure App Service 가격][Pricing]에 설명된 대로 비율 변경에 대한 요금이 있습니다.

프런트 엔드 리소스는 ASE의 HTTP/HTTPS 끝점입니다. 기본 프런트 엔드 구성에서는 프런트 엔드당 메모리 사용량이 일관되게 약 60%입니다. 고객 워크로드는 프런트 엔드에서 실행되지 않습니다. 확장과 관련한 프런트 엔드의 주요 요인은 CPU입니다. CPU는 주로 HTTPS 트래픽에 의해 결정됩니다.

## <a name="app-access"></a>앱 액세스 ##

외부 ASE에서 앱을 만들 때 사용되는 도메인은 다중 테넌트 App Service와 다르며 ASE의 이름을 포함합니다. 외부 ASE를 만드는 방법에 대한 자세한 내용은 [App Service Environment 만들기][MakeExternalASE]를 참조하세요. 외부 ASE의 도메인 이름은 *.&lt;asename&gt;.p.azurewebsites.net*과 같은 형식입니다. 즉, ASE 이름이 _external-ase_이고 해당 ASE에서 _contoso_라는 앱을 호스트하는 경우 다음과 같은 URL에서 앱에 액세스하게 됩니다.

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL *contoso.scm.external-ase.p.azurewebsites.net*은 Kudu 콘솔에 액세스하거나 웹 배포를 사용하여 앱을 게시하는 데 사용됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

ILB ASE에서는 배포 시간에 도메인을 결정합니다. ILB ASE를 만드는 방법에 대한 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요. 도메인 이름 _ilb-ase.info_를 지정하면 해당 ASE의 앱은 앱을 만드는 동안 해당 도메인을 사용합니다. 이름이 _contoso_인 앱의 경우 URL은 다음과 같습니다.

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>게시 ##

다중 테넌트 App Service와 마찬가지로 ASE에서는 다음을 사용하여 게시할 수 있습니다.

- 웹 배포
- FTP
- 연속 통합
- Kudu 콘솔에서 끌어서 놓기
- IDE(예: Visual Studio, Eclipse 또는 Intellij IDEA)

외부 ASE에서 이러한 항목 모두 동일하게 동작합니다. 자세한 내용은 [Azure App Service의 배포][AppDeploy]를 참조하세요. 

게시에서 큰 차이점은 ILB ASE와 관련된 내용입니다. ILB ASE에서 게시 끝점은 모두 ILB를 통해서만 사용할 수 있습니다. ILB는 Virtual Network의 ASE 서브넷에 있는 개인 IP에 있습니다. ILB에 대한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다. [ILB ASE 만들기 및 사용][MakeILBASE]에 설명된 것처럼 시스템에서 앱에 대한 DNS를 구성해야 합니다. 여기에 SCM 끝점이 포함됩니다. 제대로 정의되지 않은 경우 게시할 수 없습니다. 또한 직접 ILB에 게시하려면 IDE에 ILB에 대한 네트워크 액세스 권한이 있어야 합니다.

인터넷 기반 CI 시스템(예: Github 및 VSTS)은 게시 끝점이 인터넷 액세스가 불가능하므로 ILB ASE와 작동하지 않습니다. 대신 끌어오기 모델을 사용하는 CI 시스템(예: Dropbox)을 사용해야 합니다.

ILB ASE의 앱에 대한 게시 끝점에서는 ILB ASE가 만들어진 도메인을 사용합니다. 이는 앱의 게시 프로필과 앱의 포털 블레이드(**개요** > **필수** 및 **속성**)에서 확인할 수 있습니다. 

## <a name="pricing"></a>가격 ##

ASEv2를 사용할 경우, ASEv2에만 사용되는 **격리**라는 새로운 요금제 SKU가 있습니다. ASEv2에서 호스트되는 모든 App Service 계획은 격리 요금제 SKU에 있습니다. App Service 계획에 대한 가격 외에 ASE 자체에 대한 고정 요금이 있습니다. 이 가격은 ASE 크기에 따라 변경되지 않습니다. 

다른 잠재적인 요금은 프런트 엔드 확장 비율 또는 프런트 엔드 크기 조정에 대한 것입니다. 프런트 엔드가 더 빠르게 추가되도록 확장 비율을 조정하면 시스템에 자동으로 추가되지 않은 모든 추가 코어에 대한 비용을 지불하게 됩니다. 마찬가지로, 프런트 엔드에 대해 더 큰 크기를 선택하면 자동으로 할당되지 않는 모든 코어에 대한 비용을 지불하게 됩니다. 예를 들어 확장 비율을 10으로 조정하면 App Service 계획에서 인스턴스 10개마다 프런트 엔드 하나가 추가됩니다. 고정 비용은 15개 인스턴스마다 프런트 엔드 1개의 확장 비율을 포함합니다. 확장 비율이 10일 경우 10개의 ASP 인스턴스에 대해 추가된 세 번째 프런트 엔드에 대한 비용을 지불하게 되지만, 인스턴스 15개에 도달할 때는 프런트 엔드가 자동으로 추가되므로 프런트 엔드에 대한 비용을 지불할 필요가 없습니다.

자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요.

## <a name="deleting-an-ase"></a>ASE 삭제 ##

App Service Environment를 삭제하려면 App Service Environment 블레이드의 위쪽에 있는 **삭제** 작업을 사용하면 됩니다. 이렇게 하면 이 작업을 수행할지 확인하기 위해 앱 서비스 환경의 이름을 입력하라는 메시지가 표시됩니다. 앱 서비스 환경을 삭제하는 경우 포함된 내용도 모두 삭제됩니다. 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

