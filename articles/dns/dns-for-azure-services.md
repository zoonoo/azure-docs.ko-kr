<properties
  pageTitle="다른 Azure 서비스와 함께 Azure DNS 사용 | Microsoft Azure"
  description="Azure DNS를 사용하여 다른 Azure 서비스에 대한 이름을 확인하는 방법 이해"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>


# <a name="using-azure-dns-with-other-azure-services"></a>다른 Azure 서비스와 함께 Azure DNS 사용

Azure DNS는 호스팅된 DNS 관리 및 이름 확인 서비스입니다. 이를 통해 Azure에서 배포한 다른 응용 프로그램 및 서비스에 대한 공용 DNS 이름을 만들 수 있습니다. 사용자 지정 도메인에서 Azure 서비스에 대한 이름 만들기는 서비스에 대해 올바른 형식의 레코드를 추가하는 것 만큼 간단합니다.

* 동적으로 할당된 IP 주소의 경우 Azure가 서비스에 대해 만든 DNS 이름에 매핑하는 DNS CNAME 레코드를 만들어야 합니다. DNS 표준은 영역 루트에 대해 CNAME 레코드를 사용하지 못하도록 합니다.
* 정적으로 할당된 IP 주소의 경우 영역 루트의 _naked 도메인_ 이름을 포함한 모든 이름을 사용하여 DNS A 레코드를 만들 수 있습니다.

다음 테이블은 다양한 Azure 서비스에 사용할 수 있는 지원되는 레코드 형식을 설명합니다. 이 테이블에서 볼 수 있듯이 Azure DNS는 인터넷 연결 네트워크 리소스에 대한 DNS 레코드만을 지원합니다. Azure DNS는 내부, 개인 주소의 이름 확인에 사용할 수 없습니다.

| Azure 서비스 | 네트워크 인터페이스 | 설명 |
|---------------|-------------------|-------------|
| 응용 프로그램 게이트웨이 | 프런트 엔드 공용 IP | DNS A 또는 CNAME 레코드를 만들 수 있습니다. |
| 부하 분산 장치 | 프런트 엔드 공용 IP | DNS A 또는 CNAME 레코드를 만들 수 있습니다. 부하 분산 장치는 동적으로 할당된 IPv6 공용 IP 주소를 가질 수 있습니다. 따라서 IPv6 주소에 대한 CNAME 레코드를 만들어야 합니다. |
| 트래픽 관리자 | 공용 이름 | Traffic Manager 프로필에 할당된 trafficmanager.net 이름에 매핑하는 CNAME을 만들 수 있습니다. 자세한 내용은 [Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example)을 참조하세요. |
| 클라우드 서비스 | 공용 IP | 정적으로 할당된 IP 주소의 경우 DNS A 레코드를 만들 수 있습니다. 동적으로 할당된 IP 주소의 경우 _cloudapp.net_ 이름에 매핑하는 CNAME 레코드를 만들어야 합니다. 이 규칙은 클라우드 서비스로 배포되므로 클래식 포털에서 만든 VM에 적용됩니다. 자세한 내용은 [클라우드 서비스에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요. |
| 앱 서비스 | 외부 IP | 외부 IP 주소의 경우 DNS A 레코드를 만들 수 있습니다. 그렇지 않으면 azurewebsites.net 이름에 매핑하는 CNAME 레코드를 만들어야 합니다. 자세한 내용은 [Azure 앱에 사용자 지정 도메인 이름 매핑](../app-service-web/web-sites-custom-domain-name.md) |
| 리소스 관리자 VM | 공용 IP | 리소스 관리자 VM은 공용 IP 주소를 가질 수 있습니다. 공용 IP 주소가 있는 VM은 부하 분산 장치 뒤에 올 수도 있습니다. 공용 주소에 대한 DNS A 또는 CNAME 레코드를 만들 수 있습니다. 이 사용자 지정 이름은 부하 분산 장치의 VIP를 우회하는 데 사용할 수 있습니다. |
| 클래식 VM | 공용 IP | PowerShell 또는 CLI를 사용하여 만든 클래식 VM은 동적 또는 정적(예약된) 가상 주소로 구성될 수 있습니다. DNS CNAME 또는 A 레코드를 각각 만들 수 있습니다. |



<!--HONumber=Oct16_HO2-->


