---
title: 포털을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브 만들기 | Microsoft Docs
description: 포털을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브를 만드는 방법을 알아봅니다.
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2016
ms.author: gwallace

---
# 포털을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브 만들기
> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)
> 
> 

<BR>

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## 시나리오
다음 시나리오에서는 기존 응용 프로그램 게이트웨이에서 사용자 지정 상태 프로브를 만드는 과정을 살펴봅니다. 이 시나리오에서는 [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-portal.md) 단계를 이미 수행한 것으로 가정합니다.

## <a name="createprobe"></a>프로브 만들기
프로브는 포털을 통해 두 단계 프로세스로 구성됩니다. 첫 번째 단계에서는 프로브를 만들고, 두 번째 단계에서는 응용 프로그램 게이트웨이의 백 엔드 http 설정에 프로브를 추가합니다.

### 1단계
http://portal.azure.com으로 이동하여 기존 응용 프로그램 게이트웨이를 선택합니다.

![응용 프로그램 게이트웨이 개요][1]

### 2단계
**프로브**를 클릭한 다음 **추가** 단추를 클릭하여 새 프로브를 추가합니다.

![정보가 입력된 프로브 추가 블레이드][2]

### 3단계
프로브에 대한 필수 정보를 입력하고 완료되면 **확인**을 클릭합니다.

* **이름** - 포털에서 액세스할 수 있는 프로브의 이름입니다.
* **호스트** - 프로브에 사용되는 호스트 이름입니다.
* **경로** - 사용자 지정 프로브의 전체 url 중 나머지 부분입니다.
* **간격(초)** - 상태를 확인하기 위해 프로브를 실행할 주기입니다.
* **제한 시간(초)** - 시간 초과되기 전에 프로브가 대기할 시간입니다.
* **비정상 임계값** - 비정상으로 간주되는 실패한 시도 횟수입니다.

> [!IMPORTANT]
> 호스트 이름은 서버 이름이 아닙니다. 응용 프로그램 서버에서 실행 중인 가상 호스트의 이름입니다. 프로브는 http://(host name):(port from httpsetting)/urlPath로 전송됩니다.
> 
> 

![프로브 구성 설정][3]

## 게이트웨이에 프로브 추가
프로브를 만들었으므로 이제 게이트웨이에 추가해야 합니다. 프로브 설정은 응용 프로그램 게이트웨이의 백 엔드 http 설정에 지정됩니다.

### 1단계
응용 프로그램 게이트웨이의 **HTTP 설정**을 클릭한 다음 창에서 현재 백 엔드 http 설정을 클릭하여 구성 블레이드를 불러옵니다.

![https 설정 창][4]

### 2단계
**appGatewayBackEndHttp** 설정 블레이드에서 **사용자 지정 프로브 사용**을 클릭하고 [프로브 만들기](#createprobe) 섹션에 생성된 프로브를 선택합니다. 완료되면 **확인**을 클릭합니다. 그러면 설정이 적용됩니다.

![appgatewaybackend 설정 블레이드][5]

기본 프로브는 웹 응용 프로그램에 대한 기본 액세스를 확인합니다. 사용자 지정 프로브를 만들었으므로 이제 응용 프로그램 게이트웨이에서 정의된 사용자 지정 경로를 사용하여 선택된 백 엔드의 상태를 모니터링합니다. 정의된 기준에 따라, 응용 프로그램 게이트웨이는 프로브에 지정된 파일을 확인합니다. host:Port/path에 대한 호출에서 Http 200 OK 상태 응답이 반환되지 않으면 비정상 임계값에 도달한 후 서버가 순환에서 제외된 것입니다. 비정상 인스턴스에 대해 프로빙이 계속되며 다시 정상 상태가 되는지 확인합니다. 인스턴스가 정상 서버 풀에 다시 추가되면 트래픽이 다시 흐르기 시작하고 평소처럼 사용자가 지정한 간격으로 인스턴스에 대한 프로빙이 계속됩니다.

## 다음 단계
Azure 응용 프로그램 게이트웨이를 사용하여 SSL 오프로드를 구성하는 방법은 [SSL 오프로드 구성](application-gateway-ssl-portal.md)을 참조하세요.

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->