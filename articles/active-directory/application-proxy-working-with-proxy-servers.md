---
title: "기존 온-프레미스 프록시 서버 및 Azure AD 작업 | Microsoft Docs"
description: "기존 온-프레미스 프록시 서버로 작업하는 방법을 다룹니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>기존 온-프레미스 프록시 서버 작업

이 문서에서는 아웃바운드 프록시 서버로 작업하도록 응용 프로그램 프록시 커넥터를 구성하는 방법을 설명합니다. 네트워크 환경에 기존 프록시가 있는 고객을 위한 것입니다.

이러한 주요 배포 시나리오를 살펴보는 것으로 시작합니다.
* 온-프레미스 아웃바운드 프록시를 바이패스하도록 커넥터 구성
* 아웃바운드 프록시를 사용하여 Azure AD 응용 프로그램 프록시에 액세스하도록 커넥터 구성

커넥터가 작동하는 방법에 대한 자세한 내용은 [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started)을 참조하세요.

## <a name="configure-your-connectors"></a>커넥터 구성

코어 커넥터 서비스는 Azure Service Bus를 사용하여 Azure AD 응용 프로그램 프록시 서비스에 대한 기본 통신을 처리합니다. Service Bus는 추가 구성 요구 사항을 수반합니다.

Azure AD 연결 문제를 해결하는 방법은 [Azure AD 응용 프로그램 프록시 연결 문제 해결 방법](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems)을 참조하세요. 

## <a name="configure-the-outbound-proxy"></a>아웃바운드 프록시 구성

사용자 환경에 아웃바운드 프록시가 있는 경우 설치를 수행하는 계정이 아웃바운드 프록시를 사용하도록 적절하게 구성되어야 합니다. 설치 프로그램은 설치를 수행하는 사용자의 컨텍스트에서 실행되므로 Microsoft Edge 또는 다른 인터넷 브라우저를 사용하여 구성할 수 있습니다. 

Microsoft Edge를 사용하여 프록시 설정을 구성하려면 [설정], [고급 설정 보기], [프록시 설정 열기], [수동 프록시 설정]으로 이동합니다. [프록시 서버 사용]을 켜기로 설정하고 [로컬 인트라넷 주소에 프록시 서버 사용 안 함]을 선택한 후 주소 및 포트를 변경하여 로컬 프록시 서버를 반영합니다.

아래 옵션 상자에 표시된 대로 필요한 프록시 설정을 입력합니다.

 ![AzureAD에서 로컬 주소 바이패스](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>아웃바운드 프록시 바이패스

기본적으로 아웃바운드 요청을 만들기 위해 커넥터에 사용된 기본 OS 구성 요소는 WPAD(웹 프록시 자동 검색)를 사용하여 네트워크에서 프록시 서버를 자동으로 찾습니다(환경에서 사용하도록 설정된 경우).

일반적으로 wpad.domainsuffix에 대해 DNS 조회를 수행하여 작동합니다. DNS에서 확인되면, 사용자 환경에서 프록시 구성 스크립트가 될 wpad.dat에 대한 IP 주소에 HTTP 요청이 생성됩니다. 그러면 커넥터는 이 요청을 사용하여 아웃바운드 프록시 서버를 선택합니다. 하지만 커넥터 트래픽은 프록시에서 필요한 추가 구성 설정 때문에 여전히 통과하지 못할 수 있습니다. 

다음 섹션에서는 트래픽이 흐를 수 있도록 하기 위해 아웃바운드 프록시에서 필요한 구성 단계를 다룹니다. 하지만 먼저 온-프레미스 프록시를 바이패스하여 Azure 서비스로 직접 연결을 사용하도록 커넥터를 구성하는 방법을 살펴보겠습니다. 이렇게 하면 유지할 구성이 줄어들므로 네트워크 정책에서 허용하기만 한다면 권장되는 방법입니다.

커넥터에 대한 아웃바운드 프록시 사용을 사용하지 않도록 하려면 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 파일을 편집하여 아래 예제 코드에 표시된 [system.net] 섹션을 추가합니다.

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
커넥터 업데이터 서비스도 프록시를 바이패스하도록 하려면 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 위치의 ApplicationProxyConnectorUpdaterService.exe.config 파일을 유사하게 변경합니다.

기본 .config 파일로 되돌려야 할 때를 대비해 원본 파일을 복사해 두는 것을 잊지 마세요. 

## <a name="use-the-outbound-proxy-server"></a>아웃바운드 프록시 서버 사용

위에서 설명했듯이, 일부 고객 환경에는 예외 없이 아웃바운드 프록시를 통과하기 위해 모든 아웃바운드 트래픽에 대한 요구 사항이 있습니다. 따라서 프록시를 바이패스하는 것은 옵션이 아닙니다.

아래와 같이 커넥터 트래픽이 아웃바운드 프록시를 통과하도록 구성할 수 있습니다.

 ![AzureAD에서 로컬 주소 바이패스](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

아웃바운드 트래픽만 포함하므로 커넥터 간의 부하 분산을 설정하거나 방화벽을 통과하는 인바운드 액세스를 구성할 필요가 없습니다.

이 경우 다음 단계를 수행해야 합니다.
1. 아웃바운드 프록시를 통과하도록 커넥터 및 업데이터 서비스를 구성합니다.
2. Azure AD 앱 프록시 서비스에 대한 연결을 허용하도록 프록시 설정을 구성합니다.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1단계: 아웃바운드 프록시를 통과하도록 커넥터 및 관련 서비스 구성

위에서 설명한 것처럼 환경에 WPAD가 사용되고 적절히 구성된 경우 커넥터는 아웃바운드 프록시 서버를 자동으로 검색하고 사용하려고 합니다. 하지만 트래픽이 아웃바운드 프록시를 통과하도록 명시적으로 커넥터를 구성할 수 있습니다. 

이렇게 하려면 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 파일을 편집하여 아래 예제 코드에 표시된 [system.net] 섹션을 추가합니다.

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>로컬 프록시 서버 이름 또는 IP 주소와 수신 대기 중인 포트를 반영하도록 _proxyserver:8080_을 변경합니다.
>

그런 다음 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.confi 위치의 파일을 유사하게 변경하여 프록시를 사용하도록 커넥터 업데이터 서비스를 구성해야 합니다.

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2단계: 커넥터 및 관련 서비스에서 트래픽이 통과하여 흐를 수 있도록 프록시 구성

아웃바운드 프록시에서는 다음 4가지 측면을 고려해야 합니다.
* 프록시 아웃바운드 규칙
* 프록시 인증
* 프록시 포트
* SSL 조사

#### <a name="2a-proxy-outbound-rules"></a>2.A: 프록시 아웃바운드 규칙
커넥터 서비스 액세스를 위해 다음 끝점에 대한 액세스를 허용합니다.

* *.msappproxy.net 
* *.servicebus.microsoft.net 

초기 등록을 위해 다음 끝점에 대한 액세스를 허용합니다.

* login.windows.net 
* login.microsoftonline.com. 

커넥터 서비스에 사용된 기본 Service Bus 컨트롤 채널에는 특정 IP 주소에 대한 연결이 추가로 필요합니다. 전체 FQDN으로 변환하기 위해 Service Bus 팀과 작업 중입니다. 하지만 현재는 두 가지 옵션이 있습니다. 
 
* 모든 대상으로 커넥터 아웃바운드 액세스 허용
* https://www.microsoft.com/en-gb/download/details.aspx?id=41653(영문)에 나와 있는 Azure 데이터 센터 IP 범위로 커넥터 아웃바운드 액세스 허용

>[!NOTE]
>Azure 데이터 센터 IP 범위 목록 사용 시 목록이 매주 업데이트된다는 문제가 있으므로 그에 따라 액세스 규칙을 업데이트하는 과정이 필요합니다.
>

#### <a name="2b-proxy-authentication"></a>2.B: 프록시 인증

프록시 인증은 현재 지원되지 않습니다. 현재 권장 사항은 인터넷 대상에 대한 커넥터 익명 액세스를 허용하는 것입니다.

#### <a name="2c-proxy-ports"></a>2.C: 프록시 포트

커넥터는 CONNECT 메서드를 사용하여 아웃바운드 SSL 기반 연결을 만듭니다. 기본적으로 아웃바운드 프록시를 통해 터널을 설정합니다. 일부 프록시 서버는 기본적으로 표준 SSL 포트(예: 443)에 대한 아웃바운드 터널링만 허용합니다. 이 경우 추가 포트로 터널링을 허용하도록 프록시 서버를 구성해야 합니다.

비표준 SSL 포트 8080, 9090, 9091 및 10100-10120으로 터널링을 허용하도록 프록시 서버를 구성합니다.

>[!NOTE]
>Service Bus가 HTTPS를 초과하면 포트 443을 사용합니다. 하지만 기본적으로 Service Bus는 직접 TCP 연결을 시도하며 직접 연결이 실패할 때만 HTTPS를 대체합니다. 
> 

Service Bus 트래픽이 아웃바운드 프록시 서버를 통해 전송되도록 하려면 커넥터에서 포트 9350, 9352 및 5671로 Azure 서비스에 직접 연결할 수 없음을 확인해야 합니다.

#### <a name="2d-ssl-inspection"></a>2.D: SSL 조사
커넥터 트래픽에 대해 문제가 발생하므로 커넥터 트래픽에 대해 SSL 조사를 사용하지 마세요. 

이것으로 끝입니다. 이제 프록시를 통과하여 흐르는 모든 트래픽이 표시됩니다. 문제가 발생하는 경우 다음 문제 해결 단계가 도움이 됩니다.

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>커넥터 프록시 및 서비스 연결 문제 해결

커넥터 연결 문제를 식별하고 해결하는 한 가지 좋은 방법은 커넥터 서비스를 시작하는 동안 커넥터 서비스에서 네트워크 캡처를 사용하는 것입니다. 이는 어려운 작업일 수 있으므로 네트워크 추적을 캡처 및 필터링하는 빠른 팁을 살펴 보겠습니다.

원하는 모니터링 도구를 사용할 수 있습니다. 이 문서의 목적에 맞게 Microsoft 네트워크 모니터 3.4를 사용했으며 https://www.microsoft.com/ko-kr/download/details.aspx?id=4865에서 다운로드할 수 있습니다.

아래에서 사용하는 예제와 필터는 Network Monitor에 국한되지만 원리는 모든 분석 도구에 적용할 수 있습니다.

### <a name="take-a-capture-using-microsoft-network-monitor"></a>Microsoft 네트워크 모니터를 사용하여 캡처

캡처를 시작하려면 네트워크 모니터를 열고 [새 캡처]를 클릭합니다. [시작] 단추를 눌러 시작합니다.

 ![AzureAD 네트워크 모니터](./media/application-proxy-working-with-proxy-servers/network-capture.png)

캡처를 완료한 후에는 [중지] 단추를 클릭하여 캡처를 종료합니다.

### <a name="take-a-capture-of-connector-traffic"></a>커넥터 트래픽 캡처

초기 문제 해결의 경우 다음 단계를 수행합니다. 

1. services.msc에서 아래와 같이 Microsoft AAD 응용 프로그램 프록시 커넥터 서비스를 중지합니다.
2. 네트워크 캡처를 시작합니다.
3. Microsoft AAD 응용 프로그램 프록시 커넥터 서비스를 시작합니다.
4. 네트워크 캡처를 중지합니다.

 ![AzureAD 네트워크 모니터](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>프록시 서버 요청에 대한 커넥터 살펴보기

이제 네트워크 캡처를 얻었고 필터링할 준비가 되었습니다. 추적을 살펴보기 위해서는 캡처를 필터링하는 방법을 이해하는 것이 중요합니다.

이에 대한 한 가지 필터는 다음과 같습니다(여기서 8080은 프록시 서비스 포트). 

(http.Request 또는 http.Response) 및 tcp.port==8080

표시 필터 창에 이 필터를 입력하고 [적용]을 선택하면 필터를 토대로 캡처한 트래픽을 필터링합니다.

위의 필터는 프록시 포트로 보내거나 받는 HTTP 요청 및 응답만 보여 줍니다. 프록시 서버를 사용하도록 구성된 커넥터를 시작하면 다음과 같은 내용이 표시됩니다.

 ![AzureAD 네트워크 모니터](./media/application-proxy-working-with-proxy-servers/http-requests.png)

구체적으로, 프록시 서버와 통신 중임을 보여 주는 CONNECT 요청을 찾는 중입니다. 성공하면 HTTP OK(200) 응답을 받게 됩니다.

다른 응답 코드(예: 407 또는 502)가 표시되면 프록시에서 인증을 요청 중이고 다른 이유로 트래픽을 허용하지 않음을 나타냅니다. 이 때에는 프록시 서버 지원 팀이 관여합니다.

### <a name="identify-failed-tcp-connection-attempts"></a>실패한 TCP 연결 시도 식별

사용자는 커넥터가 직접 연결을 시도하지만 실패하는 다른 일반적인 시나리오도 파악하고 싶을 수 있습니다. 

이 경우를 쉽게 식별하는 데 도움이 되는 다른 네트워크 모니터 필터는 다음과 같습니다.

property.TCPSynRetransmit

SYN 패킷은 TCP 연결을 설정하기 위해 전송된 첫 번째 패킷입니다. 응답을 반환하지 않으면 SYN이 다시 시도됩니다. 위의 필터를 통해 다시 전송된 SYN을 볼 수 있습니다. 그런 다음 이 내용이 커넥터와 관련된 트래픽에 해당하는지를 확인할 수 있습니다. 

다음 예제에서는 Service Bus 포트 9352에 대해 실패한 연결 시도를 보여 줍니다.

 ![AzureAD 네트워크 모니터](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

위와 같은 응답이 표시되면 커넥터에서 Azure Service Bus 서비스와의 통신을 직접 시도하는 것입니다. 커넥터가 Azure 서비스에 직접 연결한다고 예상한 경우 네트워크/방화벽 문제가 있음을 확실히 알 수 있습니다.

>[!NOTE]
>프록시 서버를 사용하도록 구성한 경우 HTTPS를 통한 연결로 전환하기 전에 Service Bus에서 직접 TCP 연결을 시도할 수 있으므로 주의하세요.
>

네트워크 추적 분석은 만능 도구는 아닙니다. 하지만 네트워크에서 어떤 일이 발생하는지에 대한 정보를 빨리 알아낼 수 있는 매우 유용한 도구일 수 있습니다. 

커넥터 연결 문제로 어려움을 겪고 있다면 문제를 해결하는 데 기꺼이 도와 줄 수 있는 지원 팀의 도움을 받으세요.

응용 프로그램 프록시 커넥터 오류 해결에 대한 자세한 내용은 [응용 프로그램 프록시 문제 해결](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD 응용 프로그램 프록시 커넥터 이해](application-proxy-understand-connectors.md)<br>
[Azure AD 응용 프로그램 프록시 커넥터를 자동으로 설치하는 방법](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


