---
title: "Azure App Service Environment에서 내부 부하 분산 장치 만들기 및 사용"
description: "인터넷에 연결되지 않은 Azure App Service Environment 만들기 및 사용 세부 정보"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Environment에서 내부 부하 분산 장치 만들기 및 사용 #

ASE(App Service Environment)는 Azure App Service를 사용자의 Azure VNet(Virtual Network)의 서브넷에 배포한 것입니다. 두 가지 방법으로 ASE를 배포할 수 있습니다. 

- _외부 ASE_라고도 하는 외부 IP 주소의 VIP 사용
- 내부 끝점이 ILB(내부 부하 분산 장치)이기 때문에 _ILB ASE_라고도 하는 내부 IP 주소의 VIP 사용 

이 문서에서는 _ILB ASE_를 만드는 방법을 보여 줍니다.  [App Service 환경 소개][Intro]를 시작할 수 있는 ASE의 개요 및 외부 ASE를 만드는 방법을 알아보려면 [외부 ASE 만들기][MakeExternalASE]를 시작합니다.

## <a name="overview"></a>개요 ##

ASE는 Virtual Network의 인터넷 액세스 가능 끝점 또는 IP 주소를 사용하여 배포할 수 있습니다. IP 주소를 가상 네트워크 주소로 설정하기 위해 ASE는 ILB를 사용하여 배포되어야 합니다. ILB를 사용하여 ASE를 배포하는 경우 다음과 같은 항목을 제공해야 합니다.

-   앱을 만든 고유한 도메인
-   HTTPS에 사용되는 인증서
-   도메인에 대한 DNS 관리

결과적으로 다음과 같은 작업을 수행할 수 있습니다.

-   사이트 간 또는 ExpressRoute VPN을 통해 액세스하는 클라우드에서 인트라넷 응용 프로그램을 안전하게 호스트
-   공용 DNS 서버에 나열되지 않은 클라우드에 앱 호스트
-   프런트 엔드 앱이 안전하게 통합될 수 있는 인터넷 격리 백 엔드 앱 만들기

***사용되지 않도록 설정된 기능***

다음을 비롯하여 ILB ASE를 사용하는 경우 수행할 수 없는 작업도 있습니다.

-   IP 기반 SSL 사용
-   특정 앱에 IP 주소 할당
-   포털을 통해 인증서를 구매하여 앱에 사용 Azure Portal을 통해서가 아니라 인증 기관에서 직접 인증서를 가져와서 앱에 사용할 수도 있습니다.

## <a name="create-an-ilb-ase"></a>ILB ASE 만들기 ##

ILB ASE를 만들려면

1.  Azure Portal에서 **새로 만들기 -&gt; 웹 + 모바일 -&gt; App Service 환경**을 선택합니다.
2.  사용 중인 구독을 선택합니다.
3.  리소스 그룹을 선택하거나 만듭니다.
4.  Virtual Network를 선택하거나 만듭니다.
5.  Virtual Network를 선택하는 경우 서브넷을 만듭니다. ASE의 향후 증가에 맞게 충분히 큰 서브넷 크기를 설정해야 합니다. 권장되는 크기는 `/25`입니다. 여기에는 128개의 주소가 있고 최대 크기의 ASE를 다룰 수 있습니다. `/29` 이하의 서브넷 크기는 사용할 수 없습니다.  인프라는 최소 5개의 주소를 사용해야 합니다.  `/28`의 경우에도 `/28` 서브넷에서 11개의 인스턴스로 최대한 조정할 수 있습니다. App Service 계획에서 향후 기본 최대 인스턴스 수인 100개를 넘을 것으로 예상되거나 100에 가까운 빠른 프런트 엔드 크기 조정이 필요한 경우 256개의 주소를 포함하는 /24를 사용합니다.
6.  **가상 네트워크/위치 -&gt; Virtual Network 구성**을 선택하고 **VIP 형식**을 **내부**로 설정합니다.
7.  도메인 이름을 제공합니다. 이 ASE에서 만든 앱에 사용되는 하위 도메인입니다. 몇 가지 제한 사항이 있습니다. 다음과 같은 하위 도메인을 사용할 수 없습니다.
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    또한 이 ASE를 사용하여 호스트되는 앱에 사용자 지정 도메인 이름을 사용하려는 경우 ASE에서 사용하는 사용자 지정 도메인 이름 및 도메인 이름 간에 중복되면 안됩니다. _contoso.com_이라는 도메인 이름의 ILB ASE의 경우 앱에 다음과 비슷한 사용자 지정 도메인 이름을 사용할 수 없습니다.
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    ILB ASE에 배포하는 앱에서 사용하려는 사용자 지정 도메인 이름을 알고 있는 경우 충돌이 없는 ILB ASE 생성 중에 ILB ASE의 도메인을 선택합니다. 이 예제에서는 _contoso internal.com_과 같이 설정할 수 있습니다.
8.  **확인** 및 **만들기**를 차례로 선택합니다.

    ![][1]

가상 네트워크 블레이드 내에는 **Virtual Network 구성** 옵션이 있습니다. 여기서 외부 VIP 또는 내부 VIP 중에서 선택할 수 있습니다. 기본값은 **외부**입니다. **외부**를 선택한 경우 ASE는 인터넷 액세스 가능 VIP를 사용합니다. **내부**를 선택하는 경우 ASE가 Virtual Network 내의 IP 주소에 있는 ILB로 구성됩니다.

**내부**를 선택하면 ASE에 더 많은 IP 주소를 추가하는 기능이 제거되고, 대신 ASE의 도메인을 제공해야 합니다. 외부 VIP가 있는 ASE에서 ASE의 이름은 해당 ASE에서 만든 앱에 대한 도메인에 사용됩니다.

**VIP 형식**을 **내부**로 설정한 경우 ASE 이름은 해당 ASE의 도메인에서 사용되지 않습니다. 도메인을 명시적으로 지정해야 합니다. 도메인이 ***contoso.corp.net***이고 해당 ASE에서 ***timereporting***이라는 앱을 만들었으면 해당 앱의 URL은 ***timereporting.contoso.corp.net***이 됩니다.

## <a name="apps-in-an-ilb-ase"></a>ILB ASE의 앱 ##

ILB ASE에서 앱을 만드는 과정은 일반적으로 ASE에서 앱을 만드는 과정과 같습니다.

1.  Azure Portal에서 **새로 만들기 &gt; 웹 + 모바일 &gt; 웹**을 선택하거나 **모바일** 또는 **API App**을 선택합니다.
2.  앱 이름을 입력합니다.
3.  구독을 선택합니다.
4.  리소스 그룹을 선택하거나 만듭니다.
5.  App Service 계획을 선택하거나 만듭니다. 새 App Service 계획을 만들려는 경우 위치로 ASE를 선택하고 App Service 계획을 생성할 작업자 풀을 선택합니다. App Service 계획을 만들 때 위치 및 작업자 풀로 ASE를 선택합니다. 앱의 이름을 지정하면 앱 이름 아래의 도메인이 ASE에 대한 도메인으로 바뀌는 것을 볼 수 있습니다.
6.  **만들기**를 선택합니다. 앱을 대시보드에 표시하려면 **대시보드에 고정** 확인란을 선택해야 합니다.

    ![][2]

앱 이름 아래의 도메인 이름이 ASE의 도메인을 반영하도록 업데이트됩니다.

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 만들기 유효성 검사 게시 ##

ILB ASE는 비 ILB ASE와는 약간 다릅니다. 앞서 명시한 것처럼 사용자 고유의 DNS를 관리해야 하므로 HTTPS 연결에 대 한 사용자 고유의 인증서를 제공해야 합니다.

ASE를 만든 후에는 해당 도메인 이름이 사용자가 지정한 도메인을 표시하며, **설정** 메뉴에는 **ILB 인증서**라는 새 항목이 나타납니다. ILB ASE 도메인을 지정하지 않는 인증서로 ASE가 만들어집니다. 해당 인증서로 ASE를 사용하는 경우 브라우저에서 올바르지 않음을 알려줍니다. 이 인증서를 사용하면 HTTPS를 쉽게 테스트할 수 있지만 자체 서명되었는지 아니면 CA(인증 기관)에서 획득했는지와 상관없이 ILB ASE 도메인에 연결된 고유한 인증서를 업로드해야 합니다.

![][3]

유효한 SSL 인증서를 구하는 방법에는 터미널 CA를 사용하거나, 외부 발급자로부터 인증서를 구입하거나, 자체 서명된 인증서를 사용하는 등 다양한 방법이 있습니다. SSL 인증서의 소스에 관계 없이 다음과 같은 인증서 특성을 올바르게 구성해야 합니다.

-   _주체_: 이 특성은 _\*.your-root-domain-here_으로 설정되어야 합니다.
-   _주체 대체 이름_: 이 특성에는 _\*.your-root-domain-here_ 및 _\*.scm.your-root-domain-here_ 둘 다 포함되어야 합니다. 두 번째 항목을 포함해야 하는 이유는 각 앱과 연결된 SCM/Kudu 사이트에 대한 SSL 연결이 _your-app-name.scm.your-root-domain-here_ 양식의 주소를 사용하여 만들어지기 때문입니다.

유효한 SSL 인증서가 있는 경우 두 가지 추가 준비 단계가 필요합니다. SSL 인증서를 .pfx 파일로 변환/저장해야 합니다. .pfx 파일에는 모든 중간 인증서 및 루트 인증서가 포함되어야 하며 암호로 보호되어야 합니다.

PowerShell을 사용하여 고유 인증서를 만들려는 경우 아래 표시된 명령을 사용할 수 있습니다.  *internal.contoso.com* 대신 ILB ASE 도메인 이름을 사용해야 합니다. 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

다음과 같은 PowerShell 명령을 생성하는 인증서가 브라우저에서 사용하는 인증서 체인에 있는 CA에서 만들어지지 않았기 때문에 브라우저에서 플래그 지정됩니다. 브라우저에서 문제가 되는 인증서를 가져오는 유일한 방법은 인증서의 브라우저 체인에 있는 상용 CA에서 조달하는 것입니다.  

![][4]

고유한 인증서를 업로드하고 액세스를 테스트하려면:

1.  ASE를 만든 후에 ASE UI로 이동합니다(**ASE > 설정 > ILB 인증서**).
2.  인증서 pfx 파일을 선택하여 ILB 인증서를 설정하고 암호를 제공합니다. 이 단계를 처리하는 데 잠시 시간이 소요되며, 업로드 작업이 진행 중이라는 메시지가 표시됩니다.
3.  ASE의 ILB 주소를 가져옵니다(**ASE > 속성 > 가상 IP 주소**).
4.  ASE를 만든 후에 ASE에서 웹앱을 만듭니다.
5.  VM이 Virtual Network에 없는 경우 VM을 만듭니다.

    > [!NOTE] 
    > ASE와 동일한 서브넷에서 이 VM을 만들지 않습니다. 그렇지 않은 경우 설치 프로그램이 중단됩니다.
    >
    >

6.  ASE 도메인에 DNS를 설정합니다. DNS에 도메인과 와일드카드를 사용할 수도 있고, 몇 가지 간단한 테스트를 수행하려는 경우 VM의 호스트 파일을 편집하여 웹앱 이름을 VIP IP 주소로 설정할 수 있습니다. ASE의 도메인 이름이 _.ilbase.com_이고 _mytestapp_이라는 웹앱을 만든 경우 _mytestapp.ilbase.com_으로 주소가 지정됩니다. 그런 다음 _mytestapp.ilbase.com_을 설정하여 ILB 주소로 확인할 수 있습니다. (Windows에서 호스트 파일은 _C:\Windows\System32\drivers\etc\_에 있습니다.) 고급 콘솔을 게시하거나 고급 콘솔에 액세스하는 웹 배포를 테스트하려는 경우 _mytestapp.scm.ilbase.com_에 레코드를 만들어야 합니다.
7.  해당 VM의 브라우저를 사용하여 ***http://mytestapp.ilbase.com***(또는 도메인이 포함된 웹앱 이름)으로 이동합니다.
8.  해당 VM에서 브라우저를 사용하고 ***https://mytestapp.ilbase.com***으로 이동합니다. 자체 서명된 인증서를 사용하는 경우 보안 부족에 동의해야 합니다.

ILB의 IP 주소가 **IP 주소**에 나열됩니다. 외부 VIP 및 인바운드 관리 트래픽에 사용하는 IP 주소도 있습니다.

![][5]

### <a name="functions-and-the-ilb-ase"></a>함수 및 ILB ASE

ILB ASE에서 함수를 사용할 때 다음과 같은 오류가 발생할 수 있습니다. *"함수를 지금 바로 검색할 수 없습니다.  나중에 다시 시도하세요."*  이 원인은 함수 UI가 HTTPS를 통해 SCM 사이트를 활용하기 때문입니다.  브라우저에 루트 인증서가 없는 ASE에 HTTP 인증서를 사용하는 경우에 발생할 수 있습니다.  또한 IE\Edge 브라우저는 탭 간에 *accept-invalid-cert* 설정을 공유하지 않습니다. 따라서 다음 중 하나를 수행할 수 있습니다.

- 신뢰할 수 있는 인증서 저장소에 인증서 추가 
- 또는 Chrome을 사용(단, SCM 사이트로 이동하고 신뢰할 수 없는 인증서를 수락한 다음 포털로 이동)

## <a name="dns-configuration"></a>DNS 구성 ##

외부 VIP를 사용하는 경우 DNS가 Azure에서 관리됩니다. ASE에서 만든 모든 앱은 공용 DNS에 해당하는 Azure DNS에 자동으로 추가됩니다. ILB ASE에서 자체 DNS를 관리해야 합니다. _contoso.net_과 지정된 도메인의 경우 다음에 대해 ILB 주소를 가리키는 DNS에서 DNS A 레코드를 만들어야 합니다.

- *.contoso.net
- *.scm.contoso.net

ILB ASE 도메인이 해당 ASE 외부에서 여러 작업에 사용되는 경우 앱 이름 기준으로 DNS에서 관리해야 합니다. 새 앱을 만들 때 각 앱 이름을 DNS에 추가하는 경우 훨씬 더 어렵습니다. 이러한 이유로 전용 도메인을 사용하는 것이 좋습니다.

## <a name="publishing-with-an-ilb-ase"></a>ILB ASE로 게시 ##

만든 모든 앱에는 두 개의 끝점이 있습니다. ILB ASE에는 *&lt;앱 이름>.&lt;ILB ASE 도메인>*과 *&lt;앱 이름>.scm.&lt; ILB ASE 도메인>*이 있습니다. 

SCM 사이트 이름은 Azure Portal 내에서 **고급 포털**이라고 하는 Kudu 콘솔로 이동합니다. Kudu 콘솔을 사용하면 환경 변수 확인, 디스크 탐색, 콘솔 사용 등과 같은 많은 작업을 수행할 수 있습니다. 자세한 내용은 [Azure App Service의 Kudu 콘솔][Kudu]을 참조하세요. 

다중 테넌트 App Service 및 외부 ASE에는 Azure Portal과 Kudu 콘솔 간의 Single Sign-On이 포함됩니다. 그러나 ILB ASE의 경우 대신 게시 자격 증명을 사용하여 Kudu 콘솔에 로그인해야 합니다.

게시 끝점이 인터넷에 액세스할 수 없는 경우 인터넷 기반 CI 시스템(예: Github 및 VSTS)은 ILB ASE와 작동하지 않습니다. 대신 끌어오기 모델을 사용하는 CI 시스템(예: Dropbox)을 사용해야 합니다.

ILB ASE의 앱에 대한 게시 끝점에서는 ILB ASE가 만들어진 도메인을 사용합니다. 이는 앱의 게시 프로필과 앱의 포털 블레이드(**개요** > **필수** 및 **속성**)에서 확인할 수 있습니다. 하위 도메인이 *contoso.net*인 ILB ASE 및 *mytest*라는 앱이 있는 경우 *mytest.contoso.net*으로 FTP하고 *mytest.scm.contoso.net*에 대한 웹 배포를 수행합니다.

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>WAF 장치로 ILB ASE 연결 ##

Azure App Service는 시스템을 보호하고 앱이 해킹되었는지 확인하는 많은 보안 조치를 제공합니다. 웹 응용 프로그램을 보호하기 위해 WAF(웹 응용 프로그램 방화벽)를 사용하여 Azure App Service와 같은 호스팅 플랫폼을 결합해야 합니다. ILB ASE에 네트워크 격리 응용 프로그램 끝점이 있기 때문에 사용하기에 적합합니다.  

WAF 장치를 사용하여 ILB ASE를 구성하는 방법에 대한 자세한 내용은 [App Service 환경을 사용하여 웹 응용 프로그램 방화벽 구성][ASEWAF]을 참조하세요. 이 문서에서는 ASE를 사용하여 Barracuda 가상 어플라이언스를 사용하는 방법을 보여줍니다. 다른 방법은 Azure Application Gateway를 사용하는 것입니다. App Gateway는 이후에 배치할 모든 응용 프로그램을 보호하기 위해 OWASP 핵심 규칙을 사용합니다. Azure App Gateway에 대한 자세한 내용은 [Azure 웹 응용 프로그램 방화벽 소개][AppGW]를 참조하세요.

### <a name="getting-started"></a>시작 ###

App Service 환경에 대한 모든 문서와 지침은 [응용 프로그램 서비스 환경의 추가 정보][ASEReadme]에 있습니다.

App Service 환경을 시작하려면 [App Service 환경 소개][Intro]를 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/)를 참조하세요.
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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

