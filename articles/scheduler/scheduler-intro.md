<properties
 pageTitle="Azure 스케줄러 정의 | Microsoft Azure"
 description="Azure 스케줄러를 사용하면 클라우드에서 실행할 작업을 선언적으로 설명할 수 있습니다. 그런 다음 해당 작업을 예약하고 자동으로 실행합니다."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Azure 스케줄러 정의

Azure 스케줄러를 사용하면 클라우드에서 실행할 작업을 선언적으로 설명할 수 있습니다. 그런 다음 해당 작업을 예약하고 자동으로 실행합니다. 스케줄러에서는 [Azure 포털](scheduler-get-started-portal.md), 코드, [REST API](https://msdn.microsoft.com/library/dn528946) 또는 Azure PowerShell을 사용하여 이를 수행합니다.

스케줄러는 예약된 작업을 만들고 유지 관리하며 호출합니다. 스케줄러는 작업을 호스트하거나 코드를 실행하지 않습니다. Azure, 온-프레미스 또는 다른 공급자를 통해 다른 곳에서 호스트되는 코드를 _호출_하기만 합니다. Azure 스케줄러는 HTTP, HTTPS 또는 저장소 큐를 통해 호출합니다.

스케줄러는 [작업](scheduler-concepts-terms.md)을 예약하고, 사용자가 검토할 수 있는 작업 실행 결과의 기록을 유지하고, 실행할 작업을 명확하고 안정적으로 예약합니다. Azure WebJobs(Azure 앱 서비스에서 웹앱 기능의 일부) 및 다른 Azure 일정 기능은 백그라운드에서 스케줄러를 사용합니다. [스케줄러 REST API](https://msdn.microsoft.com/library/dn528946)는 이러한 작업에 대한 통신을 관리하도록 도와줍니다. 이와 같이 스케줄러는 [복잡한 일정 및 고급 되풀이](scheduler-advanced-complexity.md)를 간편하게 지원합니다.

스케줄러를 사용하는 여러 가지 시나리오가 있습니다. 예:

+ _응용 프로그램 작업 되풀이_: 주기적으로 Twitter에서 피드로 데이터를 수집합니다.
+ _일별 유지 관리:_ 일별 로그 잘라내기, 백업 수행 및 기타 유지 관리 작업입니다. 예를 들어 관리자가 향후 9개월 간 매일 오전 1시에 데이터베이스를 백업하도록 선택할 수 있습니다.

스케줄러를 사용하면 포털에서 스크립트를 통해 작업 및 [작업 컬렉션](scheduler-concepts-terms.md)을 프로그래밍 방식으로 만들고, 업데이트하고, 삭제하고, 보고, 관리할 수 있습니다.

## 참고 항목

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure 스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure 스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Azure 스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0128_2016-->