<properties 
   pageTitle="트래픽 관리자 장애 조치(Failover) 트래픽 라우팅 방법 구성 | Microsoft Azure"
   description="이 문서는 트래픽 관리자에서 장애 조치(Failover) 트래픽 라우팅 방법을 구성하는 데 도움이 됩니다."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# 장애 조치(Failover) 라우팅 방법 구성

조직에서 서비스의 안정성을 제공하려는 경우가 많습니다. 이를 위해 기본 서비스가 작동을 중단하는 경우에 대비하여 백업 서비스를 제공합니다. 일반적인 서비스 장애 조치(Failover) 패턴은 하나 이상의 백업 서비스로 구성된 목록을 유지하는 동시에 일련의 동일한 서비스를 제공하고 기본 서비스로 트래픽을 전송하는 것입니다. 아래 절차에 따라 Azure 클라우드 서비스와 웹 사이트로 이 유형의 백업을 구성할 수 있습니다.

Azure 웹 사이트는 웹 사이트 모드에 관계없이 데이터 센터(지역이라고도 함) 내의 웹 사이트에 대해 이미 장애 조치(Failover) 트래픽 라우팅 방법 기능을 제공합니다. 트래픽 관리자를 통해 다른 데이터 센터의 웹 사이트에 대해 장애 조치(Failover) 트래픽 라우팅 방법을 지정할 수 있습니다.

## 장애 조치(Failover) 트래픽 라우팅 방법을 구성하려면

1. Azure 클래식 포털의 왼쪽 창에서 **트래픽 관리자** 아이콘을 클릭하여 트래픽 관리자 창을 엽니다. 트래픽 관리자 프로필을 아직 만들지 않은 경우 기본 트래픽 관리자 프로필을 만드는 단계는 [트래픽 관리자 프로필 관리](traffic-manager-manage-profiles.md)를 참조하세요.
2. Azure 클래식 포털의 트래픽 관리자 창에서 수정할 설정이 포함된 트래픽 관리자 프로필을 찾은 다음 프로필 이름 옆에 있는 화살표를 클릭합니다. 프로필에 대한 설정 페이지가 열립니다.
3. 프로필 페이지에서 페이지 맨 위의 **끝점**을 클릭하고 구성에 포함할 클라우드 서비스와 웹 사이트(끝점)가 둘 다 있는지 확인합니다. 끝점을 추가하거나 제거하는 단계는 [트래픽 관리자에서 끝점 관리](traffic-manager-endpoints.md)를 참조하세요.
4. 프로필 페이지에서 맨 위의 **구성**을 클릭하여 구성 페이지를 엽니다.
5. **트래픽 라우팅 방법 설정**에서 트래픽 라우팅 방법이 **장애 조치(Failover)**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **장애 조치(Failover)**를 클릭합니다.
6. **장애 조치(Failover) 우선 순위 목록**에서 끝점에 대한 장애 조치(Failover) 순서를 조정합니다. **장애 조치(Failover)** 트래픽 라우팅 방법을 선택하는 경우 선택한 끝점의 순서가 중요합니다. 기본 끝점이 맨 위에 있습니다. 위쪽 및 아래쪽 화살표를 사용하여 필요에 따라 순서를 변경합니다. Windows PowerShell을 사용하여 장애 조치(Failover) 우선 순위를 설정하는 방법에 대한 자세한 내용은 [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880)을 참조하세요.
7. **모니터링 설정**이 적절하게 구성되었는지 확인합니다. 모니터링은 오프라인 상태인 끝점으로 트래픽이 전송되지 않도록 합니다. 끝점을 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다. 모니터링에 대한 자세한 내용은 [트래픽 관리자 모니터링](traffic-manager-monitoring.md)을 참조하세요.
8. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장**을 클릭합니다.
9. 구성 변경 내용을 테스트합니다. 자세한 내용은 [트래픽 관리자 설정 테스트](traffic-manager-testing-settings.md)를 참조하세요.
10. 트래픽 관리자 프로필이 설정되어 작동하면 회사 도메인 이름이 트래픽 관리자 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다. 작업 방법에 대한 자세한 내용은 [회사 인터넷 도메인에서 트래픽 관리자 도메인 가리키기](traffic-manager-point-internet-domain.md)를 참조하세요.

## 다음 단계

[회사 인터넷 도메인이 트래픽 관리자 도메인을 가리키도록 설정](traffic-manager-point-internet-domain.md)

[트래픽 관리자 라우팅 방법](traffic-manager-routing-methods.md)

[라운드 로빈 라우팅 방법 구성](traffic-manager-configure-round-robin-routing-method.md)

[성능 라우팅 방법 구성](traffic-manager-configure-performance-routing-method.md)

[트래픽 관리자 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)

[트래픽 관리자 - 프로필 사용 안 함, 사용 또는 삭제](disable-enable-or-delete-a-profile.md)

[트래픽 관리자 - 끝점 사용 안 함 또는 사용](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_0323_2016-->