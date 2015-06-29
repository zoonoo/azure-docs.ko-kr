<properties
 pageTitle="스케줄러란?"
 description=""
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
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# 스케줄러란?

Azure 스케줄러를 사용하면 클라우드에서 실행할 작업을 선언적으로 설명할 수 있습니다. 그런 다음 해당 작업을 예약하고 자동으로 실행합니다. Azure 스케줄러에서는 [Azure 포털](scheduler-get-started-portal.md), 코드, [REST API](https://msdn.microsoft.com/library/dn528946) 또는 PowerShell을 사용하여 이를 수행합니다.

Azure 스케줄러는 예약된 작업을 유지, 관리, 예약 및 호출합니다. Azure 스케줄러는 작업을 호스트하거나 코드를 실행하지 않습니다. 다른 곳에서 호스트되는 코드를 _호출_하기만 합니다. 이 코드는 Azure, 온-프레미스 또는 다른 공급자를 통해 호스트될 수 있습니다. Azure 스케줄러는 HTTP, HTTPS 또는 저장소 큐를 통해 호출합니다.

Azure 스케줄러는 작업을 예약하고, 사용자가 요청할 수 있는 작업 실행 결과의 기록을 유지하고, 실행할 작업을 명확하고 안정적으로 예약합니다. Azure 모바일 서비스 예약 스크립트, Azure 웹앱 WebJob 및 다른 Azure 예약 기능은 백그라운드에서 Azure 스케줄러를 사용합니다. [스케줄러 REST API](https://msdn.microsoft.com/library/dn528946)는 이러한 작업에 대한 통신을 관리하도록 도와줍니다. 이와 같이 스케줄러는 [복잡한 일정 및 고급 되풀이를 간편하게](scheduler-advanced-complexity.md) 지원합니다.

Azure 스케줄러를 사용하는 여러 가지 시나리오가 있습니다. 예:

+ _응용 프로그램 작업 되풀이_: 주기적으로 Twitter에서 데이터를 수집하고 데이터를 피드에 수집합니다.
+ _일상적인 유지 관리_: 일상적으로 로그를 정리하고 백업을 수행하고 기타 유지 관리 작업을 수행합니다. 예를 들어 관리자는 향후 9개월 동안 매일 오전 1시에 데이터베이스를 백업하도록 선택할 수 있습니다.

스케줄러를 사용하면 포털에서 스크립트를 통해 [“작업 컬렉션” 및 “작업”](scheduler-concepts-terms.md)을 프로그래밍 방식으로 만들고, 업데이트하고, 삭제하고, 보고, 관리할 수 있습니다.

## 참고 항목

 [스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [관리 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러에서 계획 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [스케줄러 REST API 참조](https://msdn.microsoft.com/library/dn528946)

 [스케줄러 PowerShell Cmdlet 참조](scheduler-powershell-reference.md)

 [스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)
 

<!---HONumber=58_postMigration-->