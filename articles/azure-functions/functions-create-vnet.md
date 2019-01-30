---
title: Azure Virtual Network에 Azure Functions 통합
description: Azure Virtual Network에 함수를 연결하는 방법을 보여주는 단계별 자습서
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 1140c23a0aa5344119c35434316ec73cc3918f90
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198374"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Azure Virtual Network에 함수 앱 통합
이 단계별 자습서는 Azure Functions를 사용하여 Azure VNET에서 리소스에 연결하는 방법을 보여줍니다. 

이 자습서에서는 인터넷 액세스가 안 되는 사설 VNET의 VM에 wordpress 사이트를 배포하려고 합니다. 그런 다음, 인터넷과 VNET 모두에 대한 액세스로 함수를 배포합니다. 이 함수를 사용하여 VNET 내에 배포된 wordpress 사이트에서 리소스에 액세스합니다.

시스템 작동 방식, 문제 해결 및 고급 구성에 대한 자세한 내용은 [Azure Virtual Network와 앱 통합](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) 문서를 참조하세요. 전용 요금제에서 Azure Functions는 웹앱과 호스팅 기능이 동일하므로, 이 문서에 있는 모든 기능과 제한 사항이 Functions에도 적용됩니다.

## <a name="topology"></a>토폴로지
 ![VNet 통합 UI][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>VNET 내에서 VM 만들기

먼저 VNET 내에서 Wordpress를 실행하는 미리 구성된 VM을 만들겠습니다. 

VNET 내에서 배포할 수 있는 가장 저렴한 리소스 중 하나이므로 VM에서 Wordpress가 선택되었습니다. 또한 이 시나리오는 REST API, 기타 Azure Services, App Service 환경 등을 포함한 VNET의 어떠한 리소스에도 작동할 수 있습니다.

1.  Azure Portal로 이동
2.  "리소스 만들기" 블레이드를 열어 새 리소스 추가
3.  "[Wordpress LEMP7 Max Performance on CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)"를 검색하고 만들기 블레이드 열기 
4.  만들기 블레이드에서 다음 정보를 사용하여 VM을 구성합니다.
    1.  자습서의 끝에서 더 쉽게 리소스를 정리하도록 이 VM에 대한 새 리소스 그룹을 만듭니다. 내 리소스 그룹의 이름을 “Function-VNET-Tutorial”로 지정함
    1.  가상 머신에 고유한 이름을 지정합니다. 내 리소스 그룹의 이름을 "VNET-Wordpress"로 지정함
    1.  가장 가까운 지역을 선택합니다.
    1.  B1s로 크기 선택(1vCPU, 1GB 메모리)
    1.  관리자 계정에 대해 암호 인증을 선택하고 고유한 사용자 이름과 암호를 입력합니다. 이 자습서에서는 문제 해결이 필요하지 않다면 VM에 로그인할 필요가 없습니다.
    
        <img src="./media/functions-create-vnet/create-VM-1.png" width="700">

1. 네트워킹 탭으로 이동 하고 다음 정보를 입력합니다.
    1.  새 가상 네트워크 만들기
    1.  해당 주소 범위와 해당 주소 범위 내의 서브넷을 입력합니다. 서브넷 크기는 App Service 계획에서 사용할 수 있는 VM 수를 결정합니다. 처음으로 IP 주소 및 서브넷을 지정하는 경우 [기본 사항에 대해 소개하는 문서](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)가 있습니다. 이 시나리오에서 IP 주소 및 서브넷 지정이 중요하므로 이해가 될 때까지 새 문서를 읽고 온라인으로 비디오도 보는 것이 좋습니다. 
        1. 이 예에서는 서브넷이 10.10.1.0/24인 10.10.0.0/16 네트워크를 사용하기로 선택했습니다. 10.10.0.0/16 네트워크에서 사용 가능한 서브넷을 계산하기 쉬우므로 /16 서브넷을 오버프로비전하고 사용하기로 했습니다.
        
        <img src="./media/functions-create-vnet/create-VM-2.png" width="700">

1. 네트워킹 탭으로 돌아와 공용 IP를 “없음”으로 설정합니다. VNET 액세스를 통해서만 VM이 배포됩니다.
       
    <img src="./media/functions-create-vnet/create-VM-2.1.png" width="700">

7. VM을 만듭니다. 약 5분이 소요됩니다.
8. VM이 만들어지면 해당 네트워킹 탭으로 가서 나중을 위해 사설 IP 주소를 기록해 두세요. VM은 공용 IP가 없어야 합니다.

    ![14]

이제 가상 네트워크 내에 wordpress 사이트가 완전하게 배포되었으며, 이 사이트는 공용 인터넷에서 액세스할 수 없습니다.

## <a name="create-a-dedicated-function-app"></a>전용 함수 앱 만들기

다음 단계는 표준 이상의 App Service 계획 내에 함수 앱을 만드는 것입니다. 단, 사용 계획 함수 앱에서는 VNET 통합을 지원하지 않습니다.

1. Azure Portal로 이동
2. "리소스 만들기" 블레이드를 열어 새 리소스 추가
3. “서버리스 함수 앱” 선택
4. 사용자의 모든 기본 정보를 만들기 블레이드에 입력합니다. 단 한 가지 예외가 있습니다.
    1. 표준 이상의 App Service 계획 수준이 필요합니다.

        <img src="./media/functions-create-vnet/Create-App-Service-Plan.PNG" width="300">
    
1. 내 완료된 만들기 블레이드는 다음과 같이 보입니다.

    <img src="./media/functions-create-vnet/Create-Function-App.png" width="300">


## <a name="connect-your-function-app-to-your-vnet"></a>함수 앱을 VNET에 연결

이제 VNET 내에 여러 파일을 호스팅하는 wordpress 사이트가 있으며, 이제 함수 앱을 이 VNET에 연결해야 합니다.

1.  포털에서 이전 단계의 함수 앱에 대해 **플랫폼 기능**을 선택한 다음, **네트워킹**을 선택합니다.
1.  VNet 통합에서 **구성하려면 클릭**을 선택합니다.

    <img src="./media/functions-create-vnet/Networking-1.png" width="450">

1. VNET 통합 페이지에서 **VNet 추가(미리 보기)** 를 선택합니다.

    <img src="./media/functions-create-vnet/Networking-2.png" width="600"> 
    
1.  사용할 함수 및 App Service 계획에 대한 새 서브넷을 만듭니다. 단, 서브넷 크기는 사용자가 App Service 계획에 추가할 수 있는 총 VM 수를 제한합니다. VNET은 VNET에서 서브넷 간 트래픽을 자동으로 라우팅하므로, 사용자의 함수가 VM의 서브넷과 달라도 중요하지 않습니다. 
    
    <img src="./media/functions-create-vnet/Networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>VNET의 리소스에 액세스하는 함수 만들기

이제 함수 앱은 wordpress 사이트를 통해 VNET에 액세스할 수 있습니다. 그래서 이 함수를 사용하여 해당 파일에 액세스하고 사용자에게 다시 제공해 보려고 합니다. 이 예에서는 wordpress 사이트를 API로 사용하고 함수 프록시를 호출 함수로 사용하겠습니다. 둘 다 설정하여 시각화하기 쉽기 때문입니다. VNET 내에서 배포된 다른 API는 물론, VNET 내에 배포된 해당 API를 호출하는 코드로 다른 함수도 쉽게 사용할 수 있습니다. VNET 내에 배포된 SQL Server는 완벽한 예입니다.

1. 포털에서 이전 단계의 함수 앱을 엽니다.
1. **프록시** > **+** 를 선택하여 프록시를 만듭니다.

    <img src="./media/functions-create-vnet/New-Proxy.png" width="250">

1. 프록시 이름 및 경로를 구성합니다. 저는 내 경로로 /plant를 선택했습니다.
1. 앞서 wordpress 사이트의 IP를 입력하고 백엔드 URL을 `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`로 설정합니다.
    
    <img src="./media/functions-create-vnet/Create-Proxy.png" width="900">

이제 새 브라우저 탭에 붙여넣어 백엔드 URL에 바로 방문하려는 경우 페이지에 시간 제한이 적용됩니다. wordpress 사이트가 인터넷이 아닌 사용자의 VNET에만 연결되어 있으므로 예상할 수 있는 상황입니다. 브라우저에 프록시 URL을 붙여넣으면 VNET 내의 Wordpress 사이트에서 가져온 아름다운 식물 사진이 보입니다. 

사용자의 함수 앱이 인터넷과 VNET에 모두 연결되었습니다. 프록시는 공용 인터넷을 통해 요청을 받은 다음, 단순한 HTTP 프록시로서 이 요청을 가상 네트워크에 전달합니다. 그런 다음, 프록시는 공용 인터넷을 통해 이 응답을 다시 사용자에게 중계해 줍니다. 

<img src="./media/functions-create-vnet/Plant.png" width="900">

## <a name="next-steps"></a>다음 단계

App Service 계획에서 실행 중인 Azure Functions는 웹앱과 동일한 서비스에서 실행되므로 모든 Web Apps용 설명서가 전용 Functions에 적용됩니다.

1. [App Service와 VNET 통합/함수에 대해 여기서 자세히 알아보기](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [Azure의 VNET에 대해 자세히 알아보기](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [App Service Environment로 제어 및 네트워킹 기능 활성화](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [하이브리드 연결을 사용하여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [함수 프록시에 대해 자세히 알아보기](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/Create-Function-App.png
[3]: ./media/functions-create-vnet/Create-App-Service-Plan.PNG
[4]: ./media/functions-create-vnet/configure-VNET.png
[5]: ./media/functions-create-vnet/create-VM-1.png
[6]: ./media/functions-create-vnet/create-VM-2.png
[7]: ./media/functions-create-vnet/create-VM-2.1.png
[8]: ./media/functions-create-vnet/Networking-1.png
[9]: ./media/functions-create-vnet/Networking-2.png
[10]: ./media/functions-create-vnet/Networking-3.png
[11]: ./media/functions-create-vnet/New-Proxy.png
[12]: ./media/functions-create-vnet/Create-Proxy.png
[14]: ./media/functions-create-vnet/VM-Networking.png
