<properties
   pageTitle="트래픽 관리자 라운드 로빈 부하 분산 구성"
   description="이 문서는 트래픽 관리자 끝점에 대해 라운드 로빈 부하 분산을 구성하는 데 도움이 됩니다."
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# 라운드 로빈 부하 분산 구성

일반적인 부하 분산 패턴은 클라우드 서비스와 웹 사이트를 포함하는 일련의 동일한 끝점을 제공하고 각 끝점에 라운드 로빈 방식으로 트래픽을 전송하는 것입니다. 아래 단계에서는 이 유형의 부하 분산을 수행하기 위해 트래픽 관리자를 구성하는 방법을 설명합니다. 다양한 부하 분산 방법에 대한 자세한 내용은 [트래픽 관리자 부하 분산 방법 정보](traffic-manager-load-balancing-methods.md)를 참조하세요.

>[AZURE.NOTE] Azure 웹 사이트는 데이터 센터(지역이라고도 함) 내의 웹 사이트에 대해 이미 라운드 로빈 부하 분산 기능을 제공합니다. 트래픽 관리자를 통해 다른 데이터 센터의 웹 사이트에 대해 라운드 로빈 부하 분산을 지정할 수 있습니다.

## 일련의 끝점에 균등하게(라운드 로빈) 트래픽 부하 분산:

1. 관리 포털의 왼쪽 창에서 **트래픽 관리자** 아이콘을 클릭하여 트래픽 관리자 창을 엽니다. 트래픽 관리자 프로필을 아직 만들지 않은 경우 기본 트래픽 관리자 프로필을 만드는 단계는 [트래픽 관리자 프로필 관리](traffic-manager-manage-profiles.md)를 참조하세요.
2. 관리 포털의 트래픽 관리자 창에서 수정할 설정이 포함된 트래픽 관리자 프로필을 찾은 다음 프로필 이름 옆에 있는 화살표를 클릭합니다. 프로필에 대한 설정 페이지가 열립니다.
3. 프로필 페이지에서 페이지 맨 위의 **끝점**을 클릭하고 구성에 포함할 서비스 끝점이 있는지 확인합니다. 끝점을 추가하거나 제거하는 단계는 [트래픽 관리자에서 끝점 관리](traffic-manager-endpoints.md).를 참조하세요.
4. 프로필 페이지에서 맨 위의 **구성**을 클릭하여 구성 페이지를 엽니다.
5. **부하 분산 방법 설정**에서 부하 분산 방법이 **라운드 로빈**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **라운드 로빈**을 클릭합니다.
6. **모니터링 설정**이 적절하게 구성되었는지 확인합니다. 모니터링은 오프라인 상태인 끝점으로 트래픽이 전송되지 않도록 합니다. 끝점을 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다. 모니터링에 대한 자세한 내용은 [트래픽 관리자 모니터링 정보](../traffic-manager-about-monitoring.md).를 참조하세요.
7. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장**을 클릭합니다.
8. 구성 변경 내용을 테스트합니다. 자세한 내용은 [트래픽 관리자 설정 테스트](traffic-manager-testing-settings.md)를 참조하세요.
9. 트래픽 관리자 프로필이 설정되어 작동하면 회사 도메인 이름이 트래픽 관리자 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다. 작업 방법에 대한 자세한 내용은 [회사 인터넷 도메인에서 트래픽 관리자 도메인 가리키기](traffic-manager-point-internet-domain.md).를 참조하세요.

## 참고 항목

[트래픽 관리자 부하 분산 방법 정보](traffic-manager-load-balancing-methods.md)

[트래픽 관리자 구성 작업](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[트래픽 관리자 개요](../traffic-manmager-overview.md)

[클라우드 서비스](http://go.microsoft.com/fwlink/?LinkId=314074)

[웹 사이트](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[트래픽 관리자 작업(REST API 참조)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 트래픽 관리자 Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49--> 