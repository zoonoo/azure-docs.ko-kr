<properties
   pageTitle="Azure 트래픽 관리자의 성능 저하 상태 문제해결"
   description="성능 저하 상태를으로 표시할 때 트래픽 관리자 프로필 문제를 해결하는 방법입니다."
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="joaoma" />
# Azure 트래픽 관리자의 성능 저하 상태 문제해결
이 페이지에서는 성능 저하 상태를 보여주는 Azure 트래픽 관리자 프로필의 문제를 해결하는 방법을 설명하고 트래픽 관리자 검색을 이해하는 데 있어 중요한 점을 제공합니다.


사용자의 일부 .cloudapp.net 호스티드 서비스를 나타내는 트래픽 관리자 프로필을 구성했으며 몇 초 후에 Status as Degraded(저하 상태)를 볼 수 습니다.

![성능 저하 상태](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

해당 프로필의 끝점 탭으로 이동하는 경우 오프라인 상태에서 하나 이상의 끝점이 표시됩니다.

![오프라인](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## 트래픽 관리자 검색에 대한 중요 참고 사항

- 검색에서 검색 경로로부터 200을 얻는 경우 트래픽 관리자는 끝점을 온라인 상태로만 고려합니다.
- 30x 리디렉션(또는 200 외의 다른 응답)은 리디렉션된 URL이 200을 반환하는 경우에도 실패합니다.

- HTTP 검색의 경우 인증서 오류는 무시됩니다.
 
- 200가 반환되면 검색 경로의 실제 콘텐츠는 문제가 되지 않습니다. 실제 웹 사이트 콘텐츠가 200을 반환하는 경우(예: ASP 페이지가 ACS 로그인 페이지 또는 기타 CNAME URL로 리디렉션되는 경우) 일반적인 방법은 “/favicon.ico”와 같은 경로를 설정하는 것입니다.
 
- 모범 사례는 사이트 가동 또는 중지를 결정하기 위한 충분한 논리가 있는 경로로 검색 경로를 설정하는 것입니다. 위의 예시에서 경로를 “/favicon.ico”로 설정하는 것은 w3wp.exe가 응답 중이지만 웹 사이트는 정상 상태가 아닌 경우에만 테스트합니다. 더 나은 옵션은 “/Probe.aspx”와 같은 경로로 설정하고 사이트가 정상 상태인지 결정하기 위한 충분한 논리를 포함하는 Probe.aspx 내에서 경로를 설정하는 것입니다(예: 100% CPU 상태가 아닌지 확인하거나 다수의 실패한 요청을 받을 때 성능 카운터 검사, 응용 프로그램의 논리가 동작하는지 확인하기 위해 데이터베이스 또는 세션 상태와 같은 리스소에 액세스 시도 등).
 
- 프로필의 모든 끝점이 성능 저하된 경우 트래픽 관리자는 모든 끝점을 정상으로 처리하고 트래픽을 모든 끝점으로 라우팅합니다. 올바르지 않게 실패한 검색 결과를 야기하는 검색 메커니즘에 있는 잠재적인 문제로 인해 서비스가 완전히 중단되지 않도록 하기 위함입니다.

  

## 문제 해결

트래픽 관리자 검색 오류 문제 해결을 위한 한 가지 도구는 wget입니다. [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)에서 이진 파일 및 종속성 패키지를 가져올 수 있습니다. wget 대신 Fiddler 또는 curl과 같은 다른 프로그램을 사용할 수 있습니다. 기본적으로 원시 HTTP 응답을 보여주는 프로그램만 필요합니다.

wget을 설치하면 명령 프롬프트로 이동하여 트래픽 관리자에서 구성된 URL + 검색 포트 및 경로에 대해 wget을 실행합니다. 이 예는 http://watestsdp2008r2.cloudapp.net:80/Probe입니다.

![문제 해결](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Wget 사용:

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

wget은 http://watestsdp2008r2.cloudapp.net/Default.aspx(으)로 리디렉션되는 301을 반환하는 URL을 표시합니다. 위의 “트래픽 관리자 검색에 대한 중요 참고 사항"에서 알 수 있듯이, 30x 리디렉션은 트래픽 관리자 검색에 의해 실패한 것으로 간주되며 이로 인해 검색을 오프라인으로 보고하게 됩니다. 이 시점에서 웹 사이트 구성을 확인하고 200이 /Probe 경로에서 반환되었음을 확인하는 것은 (또는 200을 반환하는 경로를 나타내도록 트래픽 관리자 검색을 다시 구성) 간단한 문제입니다.

 

검색에 HTTP 프로토콜을 사용하는 경우 “--no-check-certificate” 매개 변수를 wget에 추가하여 cloudapp.net URL의 인증서 불일치를 무시하고자 합니다.


## 다음 단계


[트래픽 관리자 부하 분산 방법 정보](traffic-manager-load-balancing-methods.md)

[트래픽 관리자 개요](../traffic-manmager-overview.md)

[클라우드 서비스](http://go.microsoft.com/fwlink/?LinkId=314074)

[웹사이트](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[트래픽 관리자 작업(REST API 참조)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 트래픽 관리자 cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=August15_HO6-->