<properties 
	pageTitle="Azure 포털을 사용하여 웹 앱 관리" 
	description="Azure 포털을 사용하여 Microsoft Azure의 웹 앱 관리 작업 개요" 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Azure 포털을 사용하여 웹 앱 관리

[Azure 포털](https://portal.azure.com)에서 다른 구성 요소의 컨테이너인 일련의 *블레이드*를 통해 웹 앱을 관리합니다. (Azure 포털의 개요는 [포털](http://go.microsoft.com/fwlink/?LinkId=529715) 변경 내용 가이드를 참조하세요.)

웹 앱에 대한 블레이드를 보려면 **홈**을 클릭한 다음 앱 이름을 클릭하거나 **찾아보기**를 클릭하여 모든 Azure 리소스를 봅니다.

![](./media/web-sites-manage/manage01.png)

블레이드의 상단 부분에는 일부 일반적인 동작에 대한 제어가 있습니다.

- **설정** 모든 관리 설정 목록을 봅니다.

- **찾아보기** 브라우저 창에서 웹 앱을 엽니다.

- 앱을 시작 또는 중지하고, 배포 슬롯을 교환하고, 앱을 삭제하는 등의 기타 단추

**필수 항목**은 URL, 위치, [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 계획 및 현재 상태(실행 중, 중단됨 등)를 포함하여 앱에 대한 기본 정보를 나열합니다.

아래 **필수 항목**은 앱을 모니터링, 관리 및 배포할 수 있는 다양한 부분입니다. 블레이드의 레이아웃을 사용자 지정하려면 마우스 오른쪽 단추로 클릭하여 **사용자 지정** 또는 **파트 추가**를 선택합니다. 이 항목은 나머지 부분은 기본 레이아웃에서의 파트를 설명합니다.

## 모니터링

**모니터링**은 웹 앱에 대한 메트릭을 보여주는 그래프입니다. 그래프를 구성하려면 **편집**을 클릭합니다. 표시할 시간 범위 및 메트릭을 선택할 수 있습니다. 사용 가능한 메트릭에는 요청 수, 평균 응답 시간, 서버 오류 및 CPU 시같이 포함됩니다.

![](./media/web-sites-manage/manage02.png)

경고 규칙을 추가하려면 그래프를 클릭한 다음 **경고 추가**를 클릭합니다. 경고 규칙은 특정 메트릭이 일부 임계값이 도달하는 시기를 알려줍니다. 예를 들어, 서버 오류 수가 5분을 주기로 일정 값을 초과하면 경고할 수 있습니다.

**모니터링**에는 분석 구성, 응용 프로그램 모니터링 설정 및 가용성 테스트 만들기 부분도 포함됩니다. 자세한 내용은 [Azure 앱 서비스에서 웹 앱에 대한 기본 사항 모니터링](web-sites-monitor.md)을 참조하세요.

## 사용

![](./media/web-sites-manage/manage03.png)

- **파일 시스템 저장소**는 앱에서 사용 중인 파일 저장소의 분량을 보여줍니다.
- **할당량**은 앱에서 사용 중인 리소스 사용 할당량을 보여줍니다. 자세한 내용을 보려면 이 부분을 클릭합니다.
- **크기 조정**에서는 인스턴스 수의 크기를 조정하고 자동 크기 조정을 설정할 수 있습니다. [Azure 앱 서비스에서 웹 앱 확장](../how-to-scale-websites.md)을 참조하세요.  
- **예상 지출**은 앱에 대한 예상 비용을 보여줍니다.
- **가격 책정 계층**에서는 가격 책정 계층을 변경할 수 있습니다.

## 작업

![](./media/web-sites-manage/manage04.png)

- **이벤트**. 이벤트 로그를 보려면 클릭합니다.  
- **경고 규칙**. 경고 규칙을 보고 새 경고를 추가하려면 클릭합니다.
- **로그 스트리밍** (A). 응용 프로그램 로그를 보려면 클릭합니다. 로그를 사용하도록 설정하려면 **설정**으로 이동하여 **진단 로그** 블레이드를 엽니다. 
- **콘솔**. 포털 내에서 실행되는 명령줄을 열려면 클릭합니다. `mkdir` 및 `dir`와 같은 명령을 실행하는 데 사용할 수 있습니다.  
- **프로세스 탐색기** (B). 작업 집합 및 스레드 수를 포함하여 앱에서 실행 중인 프로세스를 보려면 클릭합니다.

## 배포
 
![](./media/web-sites-manage/manage05.png)

- 연속 배포를 설정합니다. [Azure 앱 서비스에서 웹 앱 배포를 위해 Git 사용](web-sites-publish-source-control.md)을 참조하세요.
- 배포 슬롯입니다. [Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경으로 배포](web-sites-staged-publishing.md)를 참조하세요.
- Git 또는 FTP에 대한 배포 자격 증명을 설정합니다. 

## 네트워킹

- 가상 네트워크에 앱 연결
- 하이브리드 연결 추가

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 다음 단계

- [Azure 앱 서비스에서 웹 앱 크기 조정](../how-to-scale-websites.md)
- [WebJob으로 백그라운드 작업 실행](web-sites-create-web-jobs.md)
- [Azure 웹 앱 백업](web-sites-backup.md) 및 [복원](web-sites-restore.md)
- [Azure 앱 서비스에서 웹 앱에 대한 기본 사항 모니터링](web-sites-monitor.md)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!--HONumber=54--> 