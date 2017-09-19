---
title: "Azure Application Gateway에서 여러 사이트 호스트 | Microsoft Docs"
description: "이 문서에서는 Azure 포털을 통해 동일한 게이트웨이에서 여러 웹 응용 프로그램을 호스트하는 기존 Azure 응용 프로그램 게이트웨이를 구성하는 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 84bd62ae17b7f7ba4cd815ef1f9880679607ebce
ms.contentlocale: ko-kr
ms.lasthandoff: 04/20/2017

---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>여러 웹 응용 프로그램을 호스트하는 기존 응용 프로그램 게이트웨이 구성

> [!div class="op_single_selector"]
> * [쉬운 테이블](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

다중 사이트 호스팅을 통해 둘 이상의 웹 응용 프로그램을 동일한 응용 프로그램 게이트웨이에 배포할 수 있습니다. 들어오는 HTTP 요청의 호스트 헤더 존재 여부에 따라 트래픽을 수신할 수신기가 결정됩니다. 그런 다음 수신기는 게이트웨이 규칙 정의에 구성된 대로 적절한 백 엔드 풀로 트래픽을 전달합니다. SSL 사용 웹 응용 프로그램에서 응용 프로그램 게이트웨이는 SNI(서버 이름 표시) 확장에 의존하여 웹 트래픽에 대한 올바른 수신기를 선택합니다. 다중 사이트 호스팅의 일반적인 용도는 여러 웹 도메인에 대한 요청의 부하를 여러 백 엔드 서버 풀에 분산하는 것입니다. 마찬가지로 같은 루트 도메인의 여러 하위 도메인을 동일한 응용 프로그램 게이트웨이에서 호스트할 수도 있습니다.

## <a name="scenario"></a>시나리오

다음 예제에서 응용 프로그램 게이트웨이는 두 개의 백 엔드 서버 풀(contoso 서버 풀 및 fabrikam 서버 풀)을 통해 contoso.com 및 fabrikam.com에 대한 트래픽을 처리합니다. 유사한 설정을 사용하여 app.contoso.com 및 blog.contoso.com과 같은 하위 도메인을 호스트할 수 있습니다.

![다중 사이트 시나리오][multisite]

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 기존 응용 프로그램 게이트웨이에 다중 사이트 지원을 추가합니다. 이 시나리오를 완료하려면 기존 Application Gateway를 구성할 수 있어야 합니다. [포털을 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-portal.md)를 참조하여 포털에서 기본 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.

응용 프로그램 게이트웨이를 만드는 데 필요한 단계는 다음과 같습니다.

1. 각 사이트에 사용할 백 엔드 풀을 만듭니다.
2. 각 사이트 Application Gateway에서 지원할 수신기를 만듭니다.
3. 각 수신기를 적절한 백 엔드에 매핑하는 규칙을 만듭니다.

## <a name="requirements"></a>요구 사항

* **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다. FQDN을 사용할 수도 있습니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
* **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다. 다중 사이트 지원 응용 프로그램 게이트웨이의 경우 호스트 이름 및 SNI 표시도 추가됩니다.
* **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 전달되어야 하는 백 엔드 서버 풀을 정의합니다. 규칙은 나열된 순서대로 처리되고 트래픽은 특이성에 관계없이 일치하는 첫 번째 규칙을 통해 전달됩니다. 예를 들어 기본 수신기를 사용하는 규칙과 다중 사이트 수신기를 사용하는 규칙이 둘 다 같은 포트에 있는 경우 다중 사이트 규칙이 예상대로 작동하려면 다중 사이트 수신기를 사용하는 규칙은 기본 수신기를 사용하는 규칙 앞에 나열되어야 합니다. 
* **인증서:** 각 수신기에는 고유한 인증서가 필요하며, 이 예에서는 다중 사이트의 수신기가 2개 만들어집니다. 이 수신기에 대해 2개의 .pfx 인증서와 해당 암호를 만들어야 합니다.

## <a name="create-back-end-pools-for-each-site"></a>각 사이트에 사용할 백 엔드 풀 만들기

Application Gateway에서 지원할 각 사이트에는 백 엔드 풀이 필요합니다. 이 예에서는 2개 백 엔드 풀, 즉 하나는 contoso11.com, 다른 하나는 fabrikam11.com의 백 엔드 풀이 만들어집니다.

### <a name="step-1"></a>1단계:

Azure 포털 ( https://portal.azure.com ) 에서 기존 응용 프로그램 게이트웨이로 이동합니다. **백엔드 풀**을 선택하고 **추가**를 클릭합니다.

![백 엔드 풀 추가][7]

### <a name="step-2"></a>2단계

**pool1** 백 엔드 풀에 대한 정보를 입력하고(백 엔드 서버의 경우 IP 주소 또는 FQDN을 추가함) **확인**을 클릭합니다.

![pool1 백엔드 풀 설정][8]

### <a name="step-3"></a>3단계

백 엔드 풀 블레이드에서 **추가**를 클릭하여 **pool2** 백 엔드 풀을 추가하고(백 엔드 서버의 경우 IP 주소 또는 FQDN을 추가함) **확인**을 클릭합니다.

![pool2 백엔드 풀 설정][9]

## <a name="create-listeners-for-each-back-end"></a>각 백 엔드 풀의 수신기 만들기

응용 프로그램 게이트웨이는 HTTP 1.1 호스트 헤더를 기반으로 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트합니다. 포털에서 만든 기본 수신기에는 이 속성이 포함되지 않습니다.

### <a name="step-1"></a>1단계

기존 응용 프로그램 게이트웨이에서 **수신기**를 클릭하고 **다중 사이트**를 클릭하여 첫 번째 수신기를 추가합니다.

![수신기 개요 블레이드][1]

### <a name="step-2"></a>2단계:

수신기의 정보를 입력합니다. 이 예에서는 SSL 종료를 구성하고 새 프런트 엔드 포트를 만듭니다. SSL 종료에 사용할 .pfx 인증서를 업로드 합니다. 이 블레이드와 표준 기본 수신기 블레이드 사이의 유일한 차이점은 호스트 이름입니다.

![수신기 속성 블레이드][2]

### <a name="step-3"></a>3단계

**다중 사이트**를 클릭하고 이전 단계에서 설명한 대로 두 번째 사이트의 다른 수신기를 만듭니다. 두 번째 수신기에 대해 별도의 인증서를 사용해야 합니다. 이 블레이드와 표준 기본 수신기 블레이드 사이의 유일한 차이점은 호스트 이름입니다. 수신기의 정보를 입력하고 **확인**을 클릭합니다.

![수신기 속성 블레이드][3]

> [!NOTE]
> Azure 포털에서 응용 프로그램 게이트웨이의 수신기를 만드는 것은 장기 실행 작업이므로 이 시나리오에서 두 수신기를 만드는 데 시간이 걸릴 수 있습니다. 수신기가 완료되면 포털에서 다음 이미지와 같이 보여 줍니다.

![수신기 개요][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>백 엔드 풀에 수신기를 매핑하는 규칙 만들기

### <a name="step-1"></a>1단계

Azure 포털 ( https://portal.azure.com ) 에서 기존 응용 프로그램 게이트웨이로 이동합니다. **규칙**을 선택하고 **rule1** 기존 기본 규칙을 선택한 다음 **편집**을 클릭합니다.

### <a name="step-2"></a>2단계

다음 그림과 같이 규칙 블레이드에서 입력 합니다. 첫 번째 수신기와 첫 번째 풀을 선택하고, 완료하는 경우 **저장**을 클릭합니다.

![기존 규칙 편집][6]

### <a name="step-3"></a>3단계:

**기본 규칙**을 클릭하여 두 번째 규칙을 만듭니다. 두 번째 수신기와 두 번째 백 엔드 풀로 양식을 작성하고 **확인**을 클릭하여 저장합니다.

![기본 규칙 추가 블레이드][10]

이 시나리오는 Azure 포털을 통해 다중 사이트 지원을 사용하도록 기존 Application Gateway의 구성을 완료합니다.

## <a name="next-steps"></a>다음 단계

[응용 프로그램 게이트웨이-웹 응용 프로그램 방화벽](application-gateway-webapplicationfirewall-overview.md)을 사용하여 웹 사이트를 보호하는 방법을 알아봅니다.

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png

