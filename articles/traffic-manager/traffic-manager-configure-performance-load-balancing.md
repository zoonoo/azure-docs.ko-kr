<properties 
   pageTitle="성능 부하 분산 구성"
   description="이 문서는 트래픽 관리자에서 성능 부하 분산을 구성하는 데 도움이 됩니다."
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
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# 성능 부하 분산 구성

전 세계 여러 데이터 센터(지역이라고도 함)에 있는 클라우드 서비스와 웹 사이트에 부하를 분산하기 위해 들어오는 트래픽을 요청하는 클라이언트에서 대기 시간이 가장 짧은 끝점으로 보낼 수 있습니다. 일반적으로 대기 시간이 가장 짧은 데이터 센터는 지리적 거리가 가장 가까운 데이터 센터에 해당합니다. 성능 부하 분산 방법을 사용하면 가장 짧은 대기 시간을 기준으로 분산할 수 있지만 네트워크 구성이나 부하에서 발생하는 실시간 변경 내용은 고려할 수 없습니다. Azure 트래픽 관리자가 제공하는 다양한 부하 분산 방법에 대한 자세한 내용은 [트래픽 관리자 부하 분산 방법 정보](traffic-manager-load-balancing-methods.md)를 참조하세요.

## 가장 짧은 대기 시간을 기준으로 일련의 끝점에 트래픽 부하 분산:

1. 관리 포털의 왼쪽 창에서 **트래픽 관리자** 아이콘을 클릭하여 트래픽 관리자 창을 엽니다. 트래픽 관리자 프로필을 아직 만들지 않은 경우 기본 트래픽 관리자 프로필을 만드는 단계는 [트래픽 관리자 프로필 관리](traffic-manager-manage-profiles.md)를 참조하세요.
2. 관리 포털의 트래픽 관리자 창에서 수정할 설정이 포함된 트래픽 관리자 프로필을 찾은 다음 프로필 이름 옆에 있는 화살표를 클릭합니다. 프로필에 대한 설정 페이지가 열립니다.
3. 프로필 페이지에서 페이지 맨 위의 **끝점**을 클릭하고 구성에 포함할 서비스 끝점이 있는지 확인합니다. 프로필에서 끝점을 추가하거나 제거하는 단계는 [트래픽 관리자에서 끝점 관리](traffic-manager-endpoints.md)를 참조하세요.
4. 프로필 페이지에서 맨 위의 **구성**을 클릭하여 구성 페이지를 엽니다.
5. **부하 분산 방법** 설정에서 부하 분산 방법이 **성능*인지 확인합니다. 아닌 경우 드롭다운 목록에서 **성능**을 클릭합니다.
6. **모니터링 설정**이 적절하게 구성되었는지 확인합니다. 모니터링은 오프라인 상태인 끝점으로 트래픽이 전송되지 않도록 합니다. 끝점을 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다. 모니터링에 대한 자세한 내용은 [트래픽 관리자 모니터링 정보](traffic-manager-monitoring.md)를 참조하세요.
7. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장**을 클릭합니다.
8. 구성 변경 내용을 테스트합니다. 자세한 내용은 [트래픽 관리자 설정 테스트](traffic-manager-testing-settings.md)를 참조하세요.
9. 트래픽 관리자 프로필이 설정되어 작동하면 회사 도메인 이름이 트래픽 관리자 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다. 작업 방법에 대한 자세한 내용은 [회사 인터넷 도메인에서 트래픽 관리자 도메인 가리키기](traffic-manager-point-internet-domain.md)를 참조하세요.

## 참고 항목

[트래픽 관리자 부하 분산 방법 정보](traffic-manager-load-balancing-methods.md)

[트래픽 관리자 구성 작업](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[트래픽 관리자 개요](../traffic-manmager-overview.md)

[클라우드 서비스](http://go.microsoft.com/fwlink/?LinkId=314074)

[웹사이트](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[트래픽 관리자 작업(REST API 참조)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 트래픽 관리자 cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=August15_HO6-->