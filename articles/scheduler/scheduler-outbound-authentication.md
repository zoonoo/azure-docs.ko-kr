<properties
 pageTitle="스케줄러 아웃바운드 인증"
 description="스케줄러 아웃바운드 인증"
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
 ms.date="06/30/2016"
 ms.author="krisragh"/>

# 스케줄러 아웃바운드 인증

인증을 요구하는 서비스를 호출하기 위해 스케줄러 작업이 필요할 수 있습니다. 이를 통해, 호출된 서비스가 스케줄러 작업이 리소스에 액세스 가능한지 여부를 결정할 수 있습니다. 이러한 서비스 중 일부에는 다른 Azure 서비스, Salesforce.com, Facebook 및 보안 사용자 지정 웹사이트가 포함됩니다.

## 인증 추가 및 제거

스케줄러 작업에 간단하게 인증을 추가할 수 있습니다. 즉 작업을 만들거나 업데이트할 때 JSON 자식 요소 `authentication`을 `request` 요소에 추가합니다. `authentication` 개체의 일부로 PUT, PATCH 또는 POST 요청에서 스케줄러 서비스에 전달되는 암호는 응답에서 절대 반환되지 않습니다. 응답에서 암호 정보는 null로 설정되거나, 인증된 엔터티를 나타내는 공용 토큰을 갖을 수 있습니다.

인증을 제거하려면 작업을 명시적으로 PUT 또는 PATCH하여 `authentication` 개체를 null로 설정합니다. 응답에는 인증 속성이 하나도 표시되지 않습니다.

현재 지원되는 인증 유형은 `ClientCertificate`모델(SSL/TLS 클라이언트 인증서를 사용할 경우), `Basic`(기본 인증의 경우), `ActiveDirectoryOAuth`(Active Directory OAuth 인증용)뿐입니다.

## ClientCertificate 인증에 대한 요청 본문

`ClientCertificate` 모델을 사용하여 인증을 추가할 때는 다음 추가 요소를 요청 본문에 추가합니다.

|요소|설명|
|:---|:---|
|_인증(부모 요소)_|SSL 클라이언트 인증서를 사용하기 위한 인증 개체 입니다.|
|_type_|필수입니다. 인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`입니다.|
|_pfx_|필수입니다. PFX 파일의 Base64 인코딩 콘텐츠입니다.|
|_password_|필수입니다. PFX 파일에 액세스하기 위한 암호입니다.|


## ClientCertificate 인증에 대한 응답 본문

인증 정보와 함께 요청을 보내면 응답에는 다음 인증 관련 요소가 포함됩니다.

|요소 |설명 |
|:--|:--|
|_인증(부모 요소)_ |SSL 클라이언트 인증서를 사용하기 위한 인증 개체 입니다.|
|_type_ |인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`입니다.|
|_certificateThumbprint_ |인증서의 지문입니다.|
|_certificateSubjectName_ |인증서의 주체 고유 이름입니다.|
|_certificateExpiration_ |인증서의 만료일입니다.|

## 기본 인증의 요청 본문

`Basic` 모델을 사용하여 인증을 추가할 때는 다음 추가 요소를 요청 본문에 추가합니다.

|요소|설명|
|:--|:--|
|_인증(부모 요소)_ |기본 인증을 사용 하기 위한 인증 개체입니다.|
|_type_ |필수입니다. 인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다.|
|_username_ |필수입니다. 인증하기 위한 사용자 이름입니다.|
|_password_ |필수입니다. 인증하기 위한 암호입니다.|

## 기본 인증의 응답 본문

인증 정보와 함께 요청을 보내면 응답에는 다음 인증 관련 요소가 포함됩니다.

|요소|설명|
|:--|:--|
|_인증(부모 요소)_ |기본 인증을 사용 하기 위한 인증 개체입니다.|
|_type_ |인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다.|
|_username_ |인증된 사용자 이름입니다.|

## ActiveDirectoryOAuth 인증의 요청 본문

`ActiveDirectoryOAuth` 모델을 사용하여 인증을 추가할 때는 다음 추가 요소를 요청 본문에 추가합니다.

|요소 |설명 |
|:--|:--|
|_인증(부모 요소)_ |ActiveDirectoryOAuth 인증을 사용하기 위한 인증 개체입니다.|
|_type_ |필수입니다. 인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다.|
|_tenant_ |필수입니다. Azure AD 테넌트의 테넌트 식별자입니다.|
|_audience_ |필수입니다. 이 값은 https://management.core.windows.net/.|로 설정됩니다.
|_clientId_ |필수입니다. Azure AD 응용 프로그램의 클라이언트 ID를 제공합니다.|
|_secret_ |필수입니다. 토큰을 요청하는 클라이언트의 암호입니다.|

### 테넌트 식별자 확인

Azure PowerShell에서 `Get-AzureAccount`를 실행하여 Azure AD 테넌트의 테넌트 식별자를 확인할 수 있습니다.

## ActiveDirectoryOAuth 인증의 응답 본문

인증 정보와 함께 요청을 보내면 응답에는 다음 인증 관련 요소가 포함됩니다.

|요소 |설명 |
|:--|:--|
|_인증(부모 요소)_ |ActiveDirectoryOAuth 인증을 사용하기 위한 인증 개체입니다.|
|_type_ |인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다.|
|_tenant_ |Azure AD 테넌트의 테넌트 식별자입니다. |
|_audience_ |이 값은 https://management.core.windows.net/.|로 설정됩니다.
|_clientId_ |Azure AD 응용 프로그램의 클라이언트 ID입니다.|

## 참고 항목


 [스케줄러란?](scheduler-intro.md)

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure 스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure 스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0706_2016-->