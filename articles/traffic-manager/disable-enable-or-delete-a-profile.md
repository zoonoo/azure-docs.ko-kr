<properties
   pageTitle="트래픽 관리자 프로필 사용 안 함, 사용 또는 삭제 | Microsoft Azure"
   description="이 문서는 트래픽 관리자 프로필 작업에 도움이 됩니다."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# 프로필 사용 안 함, 사용 또는 삭제


구성된 끝점에 대한 사용자 요청을 참조하지 않도록 기존 트래픽 관리자 프로필을 사용하지 않도록 설정할 수 있습니다. 트래픽 관리자 프로필을 사용하지 않도록 설정하면 프로필 자체 및 프로필에 포함된 정보는 그대로 유지되며 트래픽 관리자 인터페이스에서 편집할 수 있습니다. 프로필을 다시 사용하도록 설정하려는 경우, 관리 포털에서 쉽게 설정할 수 있으며 조회가 다시 시작됩니다. 관리 포털에서 트래픽 관리자 프로필을 만들면 자동으로 활성화됩니다.

## 프로필을 사용하지 않도록 설정하려면

1. 인터넷에서 특정 위치의 IP 주소나 다른 이름에 대한 적절한 레코드 유형 및 포인터를 사용하도록 인터넷 DNS 서버의 DNS 리소스 레코드를 수정합니다. 즉, 트래픽 관리자 프로필의 도메인 이름을 가리키는 CNAME 리소스 레코드를 사용하지 않도록 인터넷 DNS 서버의 DNS 리소스 레코드를 변경 합니다.
1. 트래픽 관리자 프로필 설정을 통해 끝점에 보내는 트래픽이 중지됩니다.
1. 사용하지 않도록 설정할 프로필을 선택합니다. 트래픽 관리자 페이지에서 프로필을 선택하려면 프로필 이름 옆에 있는 열을 클릭하여 프로필을 강조표시합니다. 프로필 이름 또는 이름 옆의 화살표를 클릭하지 마세요. 클릭하면 프로필 설정 페이지로 이동합니다.
1. 프로필을 선택한 후 페이지의 맨 아래에서 사용하지 않도록 설정을 클릭합니다.

## 프로필을 사용하려면

1. 사용하도록 설정하려는 프로필을 선택합니다. 트래픽 관리자 페이지에서 프로필을 선택하려면 프로필 이름 옆에 있는 열을 클릭하여 프로필을 강조표시합니다. 프로필 이름 또는 이름 옆의 화살표를 클릭하지 마세요. 클릭하면 프로필 설정 페이지로 이동합니다.
1. 프로필을 선택한 후 페이지의 맨 아래에서 사용을 클릭합니다.
1. 회사 도메인 이름이 트래픽 관리자 프로필의 도메인 이름으로 매핑하는 CNAME 레코드 종류를 사용하여 인터넷 DNS 서버의 DNS 리소스 레코드를 수정합니다. 자세한 내용은 [회사 인터넷 도메인이 트래픽 관리자 도메인을 가리키도록 설정](traffic-manager-point-internet-domain.md)을 참조하세요.
1. 트래픽은 끝점에 전달되어 다시 시작됩니다.

## 프로필 삭제


1. 인터넷 DNS 서버의 DNS 리소스 레코드에서 트래픽 관리자 프로필의 도메인 이름을 가리키는 CNAME 리소스 레코드를 더 이상 사용하지 않는지 확인합니다.
1. 삭제할 프로필을 선택합니다. 프로필을 선택하려면 트래픽 관리자 페이지에서 
1. 프로필 옆에 있는 열을 클릭하여 프로필을 강조 표시합니다. 프로필 이름 또는 이름 옆의 화살표를 클릭하지 마세요. 클릭하면 프로필 설정 페이지로 이동합니다.
1. 프로필을 선택한 후 페이지의 맨 아래에서 삭제를 클릭합니다.

## 다음 단계

[트래픽 관리자 - 끝점 사용 안 함 또는 사용](disable-or-enable-an-endpoint.md)

[장애 조치(Failover) 라우팅 방법 구성](traffic-manager-configure-failover-routing-method.md)

[라운드 로빈 라우팅 방법 구성](traffic-manager-configure-round-robin-routing-method.md)

[성능 라우팅 방법 구성](traffic-manager-configure-performance-routing-method.md)

[트래픽 관리자 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)

<!---HONumber=Nov15_HO4-->