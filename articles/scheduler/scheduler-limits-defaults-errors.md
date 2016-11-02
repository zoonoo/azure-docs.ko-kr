<properties
 pageTitle="스케줄러 제한 및 기본값"
 description="스케줄러 제한 및 기본값"
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
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# 스케줄러 제한 및 기본값

## 스케줄러 할당량, 제한, 기본값 및 한계

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## x-ms-request-id 헤더

스케줄러 서비스에 대한 모든 요청에서는 이름이 **x-ms-request-id**인 응답 헤더를 반환합니다. 이 헤더는 요청을 고유하게 식별하는 불투명 값을 포함합니다.

요청이 계속 실패하고 해당 요청이 제대로 구성되었음을 확인한 경우 이 값을 사용하여 오류를 Microsoft에 보고할 수 있습니다. 보고서에는 x-ms-request-id 값, 요청을 수행한 대략적인 시간, 구독 식별자, 작업 컬렉션 및/또는 작업, 요청이 시도한 작업 유형을 포함합니다.

## 참고 항목


 [스케줄러란?](scheduler-intro.md)

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure 스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure 스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0824_2016-->