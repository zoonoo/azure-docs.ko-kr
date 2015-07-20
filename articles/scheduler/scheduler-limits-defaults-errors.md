<properties 
 pageTitle="스케줄러 제한, 기본값 및 오류 코드" 
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
 ms.topic="article" 
 ms.date="04/22/2015" 
 ms.author="krisragh"/>
 
# 스케줄러 제한, 기본값 및 오류 코드

## 스케줄러 할당량, 제한, 기본값 및 한계

다음 표에서는 Azure 스케줄러의 주요 할당량, 제한, 기본값 및 한계를 각각 설명합니다.

|리소스|제한 설명|
|---|---|
|**작업 크기**|최대 작업 크기는 16K입니다. PUT 또는 PATCH가 이 제한보다 큰 작업을 반환할 경우 400 잘못된 요청 상태 코드가 반환됩니다.|
|**요청 URL 크기**|요청 URL의 최대 크기는 2048자입니다.|
|**집계 헤더 크기**|최대 집계 헤더 크기는 4096자입니다.|
|**헤더 수**|최대 헤더 수는 50개입니다.|
|**본문 크기**|최대 본문 크기는 8192자입니다.|
|**되풀이 범위**|최대 되풀이 범위는 18개월입니다.|
|**시작 시간**|최대 "시작 시간"은 18 개월입니다.|
|**작업 기록**|작업 기록에 저장되는 최대 응답 본문은 2048바이트입니다.|
|**빈도**|기본 최대 빈도 할당량은 Free 작업 컬렉션에서 1시간, Standard 작업 컬렉션에서 1분입니다. 최대 빈도는 작업 컬렉션에서 최대값보다 낮게 구성할 수 있습니다. 작업 컬렉션의 모든 작업은 작업 컬렉션에서 설정한 값으로 제한됩니다. 작업 컬렉션의 최대 빈도보다 더 높은 빈도로 작업을 만들려고 시도하면 요청이 실패하고 409 충돌 상태 코드가 반환됩니다.|
|**작업**|기본 최대 작업 할당량은 Free 작업 컬렉션에서 5개, Standard 작업 컬렉션에서 50개입니다. 최대 작업 수는 작업 컬렉션에서 구성할 수 있습니다. 작업 컬렉션의 모든 작업은 작업 컬렉션에서 설정한 값으로 제한됩니다. 최대 작업 할당량보다 많은 작업을 만들려고 하면 요청이 실패하고 409 충돌 상태 코드가 반환됩니다.|
|**작업 기록의 보존**|작업 기록은 최대 2개월까지 유지됩니다.|
|**완료 및 오류 작업 보존**|완료된 작업과 오류가 발생한 작업은 60일동안 유지됩니다.|
|**시간 초과**|HTTP 동작에는 30초의 고정(구성 불가) 요청 시간 제한이 적용됩니다. 더 오래 실행되는 작업의 경우 HTTP 비공기 프로토콜을 따릅니다. 예를 들어, 즉시 202를 반환하나 백그라운드에서 작업을 계속합니다.|

## x-ms-request-id 헤더

스케줄러 서비스에 대한 모든 요청에서는 이름이 **x-ms-request-id**인 응답 헤더를 반환합니다. 이 헤더는 요청을 고유하게 식별하는 불투명 값을 포함합니다.

요청이 계속 실패하고 해당 요청이 제대로 구성되었음을 확인한 경우 이 값을 사용하여 오류를 Microsoft에 보고할 수 있습니다. 보고서에는 ofx-ms-request-id 값, 요청을 수행한 대략적인 시간, 구독 ID, 클라우드 서비스, 작업 컬렉션 및/또는 작업, 요청이 시도한 작업 유형을 포함합니다.

## 스케줄러 상태 및 오류 코드

표준 HTTP 상태 코드 외에도 Azure 스케줄러 REST API는 확장 오류 코드 및 오류 메시지를 반환합니다. 확장 코드는 표준 HTTP 상태 코드를 대체하는 것이 아니라 표준 HTTP 상태 코드와 함께 사용할 수 있는 추가적인 조치 가능 정보를 제공합니다.

예를 들어, 여러 이유로 HTTP 404 오류 문제가 발생할 수 있으므로 확장 메시지에 추가 정보를 포함하면 문제 해결에 도움이 될 수 있습니다. REST API가 반환하는 표준 HTTP 코드에 대한 자세한 정보는 [서비스 관리 상태 및 오류 코드](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx)를 참조하십시오. 서비스 API에 대한 REST API 작업은 [HTTP/1.1 상태 코드 정의](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)에서 정의한 대로 표준 HTTP 상태 코드를 반환합니다. 다음 표에서는 서비스에서 반환할 수 있는 일반적인 오류에 대해 설명합니다.

|오류 코드|HTTP 상태 코드|사용자 메시지|
|----|----|----|
|MissingOrIncorrectVersionHeader|잘못된 요청(400)|버전 관리 헤더가 지정되지 않았거나 잘못 지정되었습니다.|
|InvalidXmlRequest|잘못된 요청(400)|요청 본문의 XML이 잘못되었거나 올바르게 지정되지 않았습니다.|
|MissingOrInvalidRequiredQueryParameter|잘못된 요청(400)|필요한 쿼리 매개 변수가 이 요청에 대해 지정되지 않았거나 잘못 지정되었습니다.|
|InvalidHttpVerb|잘못된 요청(400)|지정된 HTTP 동사가 서버에서 인식할 수 없거나 이 리소스에 유효하지 않습니다.|
|AuthenticationFailed|사용할 수 없음(403)|서버가 요청을 인증하지 못했습니다. 인증서가 유효하고 이 구독과 연결되어 있는지 확인합니다.|
|ResourceNotFound|찾을 수 없음(404)|지정된 리소스는 존재하지 않습니다.|
|InternalError|내부 서버 오류(500)|서버에 내부 오류가 발생했습니다. 요청을 다시 시도하세요.|
|OperationTimedOut|내부 서버 오류(500)|허용된 시간 내에 작업을 완료할 수 없습니다.|
|ServerBusy|서비스를 사용할 수 없음(503)|서버(또는 내부 구성 요소)가 현재 요청을 수신할 수 없습니다. 요청을 다시 시도하세요.|
|SubscriptionDisabled|사용할 수 없음(403)|구독이 비활성화된 상태입니다.|
|BadRequest|잘못된 요청(400)|매개 변수가 올바르지 않습니다.|
|ConflictError|충돌(409)|충돌이 발생하여 작업을 완료할 수 없습니다.|
|TemporaryRedirect|임시 리디렉션(307)|요청된 개체를 사용할 수 없습니다. 개체의 새 위치에 대한 임시 URI는 응답의 위치 필드에서 가져올 수 있습니다. 새 URI에서 원래 요청을 반복할 수 있습니다.|

API 작업 관리 서비스는 관리 서비스가 정의한 오류 정를 반환할 수도 있습니다. 이 추가 오류 정보는 응답 본문에 반환됩니다. 오류 응답의 본문은 아래에 표시된 기본 형식을 따릅니다.

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## 참고 항목

 [스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
 
 [관리 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)
 
 [Azure 스케줄러에서 계획 및 요금 청구](scheduler-plans-billing.md)
 
 [Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)
 
 [스케줄러 REST API 참조](https://msdn.microsoft.com/library/dn528946)
 
 [스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)
 
 [스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)

  

<!---HONumber=July15_HO2-->