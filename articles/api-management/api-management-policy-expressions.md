---
title: "Azure API Management 정책 식 | Microsoft Docs"
description: "Azure API Management에서 정책 식에 대해 자세히 알아봅니다."
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: ed28fcea134bfbeda25abff41a3163471cef4294

---
# <a name="api-management-policy-expressions"></a>API Management 정책 식
정책 식 구문은 C# 6.0입니다. 각 식에서 암시적으로 제공된 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 변수와 .NET Framework 형식의 허용된 [하위 집합](api-management-policy-expressions.md#CLRTypes)에 액세스합니다.  
  
> [!NOTE]
>  정책 식에 대한 자세한 내용은 [정책 식](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/) 비디오를 참조하세요.  
>   
>  정책 식을 사용한 정책 구성에 대한 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky와 함께 하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)(영문)을 참조하세요. 이 비디오에는 다음 정책 식 데모가 포함됩니다.  
>   
>  -   10:30 - [쿼리 문자열 매개 변수 설정](api-management-transformation-policies.md#SetQueryStringParameter) 및 [HTTP 헤더 설정](api-management-transformation-policies.md#SetHTTPheader) 정책을 사용하여 백 엔드 서비스에 컨텍스트 정보를 제공하기 위해 API 수준에서 정책을 적용하는 방법을 보여 줍니다. 12분 10초 재생 시점에는 개발자 포털에서 작업을 호출하는 데모가 있으며, 여기서 작동 중인 정책을 볼 수 있습니다.  
> -   13:50 - [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 토큰 클레임 기반 작업에 대해 미리 권한을 부여하는 방법을 보여 줍니다. 15분으로 빨리 감기하여 정책 편집기에 구성된 정책을 본 다음 18분 50초로 빨리 감기하여 필수 권한 부여 토큰 포함 및 제외 모두의 경우로 개발자 포털에서 작업 호출의 데모를 볼 수 있습니다.  
> -   21:00 - [API 검사기](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) 추적을 사용하여 정책이 어떻게 평가되고 평가 결과가 어떻게 되는지 보는 방법을 보여 줍니다.  
> -   25:25 - [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) 및 [캐시에 저장](api-management-caching-policies.md#StoreToCache) 정책과 함께 정책 식을 사용하여 지원 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 API Management 응답 캐싱 기간을 구성하는 방법을 보여 줍니다.  
> -   34:30 - [흐름 제어](api-management-advanced-policies.md#choose) 및 [본문 설정](api-management-transformation-policies.md#SetBody) 정책을 사용하여 백 엔드 서비스에서 받은 응답의 데이터 요소를 제거하여 콘텐츠 필터링을 수행하는 방법을 보여 줍니다. 31분 50초 재생 시점에서 시작하는 [Dark Sky Forecast API](https://developer.forecast.io/)(영문) 개요를 확인하세요.  
> -   이 비디오에 사용된 정책 문을 다운로드하려면 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github 리포지토리를 참조하세요.  
  
  
##  <a name="a-namesyntaxa-syntax"></a><a name="Syntax"></a> 구문  
 단일 문 식은 `@(expression)`으로 묶으며 여기서 `expression`은 올바르게 구성된 C# 식입니다.  
  
 다중 문 식은 `@{expression}`으로 묶습니다. 다중 문 식 내에 모든 코드 경로는 `return` 문으로 끝나야 합니다.  
  
##  <a name="a-namepolicyexpressionsexamplesa-examples"></a><a name="PolicyExpressionsExamples"></a> 예  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge")?3600:int.Parse((string)context.Variables["maxAge"]))  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="a-namepolicyexpressionsusagea-usage"></a><a name="PolicyExpressionsUsage"></a> 사용 방법  
 정책 참조에서 다르게 지정하지 않는 한, 식은 어떤 API Management [정책](api-management-policies.md)에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다.  
  
> [!IMPORTANT]
>  정책 식을 사용하는 경우 정책을 정의할 때 정책 식에 대해 제한된 검증만 이루어집니다. 식은 게이트웨이에 의해 인바운드 또는 아웃바운드 파이프라인에서 런타임으로 실행되므로 정책 식에서 생성한 런타임 예외로 인해 API 호출에서 런타임 오류가 발생합니다.  
  
##  <a name="a-nameclrtypesa-net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> 정책 식에 허용된 .NET Framework 형식  
 다음 표에서는 .NET Framework 형식과 정책 식에 허용된 멤버를 보여 줍니다.  
  
|CLR 형식|지원되는 방식|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JArray|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JConstructor|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JContainer|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JObject|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JProperty|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JRaw|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JToken|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JTokenType|모든 메서드 지원됨|  
|Newtonsoft.Json.Linq.JValue|모든 메서드 지원됨|  
|System.Collections.Generic.IReadOnlyCollection<T\>|모두|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|모두|  
|System.Collections.Generic.ISet<TKey, TValue>|모두|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|키, 값|  
|System.Collections.Generic.List<TKey, TValue>|모두|  
|System.Collections.Generic.Queue<TKey, TValue>|모두|  
|System.Collections.Generic.Stack<TKey, TValue>|모두|  
|System.Convert|모두|  
|System.DateTime|모두|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|모두|  
|System.Decimal|모두|  
|System.Double|모두|  
|System.Guid|모두|  
|System.IEnumerable<T\>|모두|  
|System.IEnumerator<T\>|모두|  
|System.Int16|모두|  
|System.Int32|모두|  
|System.Int64|모두|  
|System.Linq.Enumerable<T\>|모든 메서드 지원됨|  
|System.Math|모두|  
|System.MidpointRounding|모두|  
|System.Nullable<T\>|모두|  
|System.Random|모두|  
|System.SByte|모두|  
|System.Security.Cryptography. HMACSHA384|모두|  
|System.Security.Cryptography. HMACSHA512|모두|  
|System.Security.Cryptography.HashAlgorithm|모두|  
|System.Security.Cryptography.HMAC|모두|  
|System.Security.Cryptography.HMACMD5|모두|  
|System.Security.Cryptography.HMACSHA1|모두|  
|System.Security.Cryptography.HMACSHA256|모두|  
|System.Security.Cryptography.KeyedHashAlgorithm|모두|  
|System.Security.Cryptography.MD5|모두|  
|System.Security.Cryptography.RNGCryptoServiceProvider|모두|  
|System.Security.Cryptography.SHA1|모두|  
|System.Security.Cryptography.SHA1Managed|모두|  
|System.Security.Cryptography.SHA256|모두|  
|System.Security.Cryptography.SHA256Managed|모두|  
|System.Security.Cryptography.SHA384|모두|  
|System.Security.Cryptography.SHA384Managed|모두|  
|System.Security.Cryptography.SHA512|모두|  
|System.Security.Cryptography.SHA512Managed|모두|  
|System.Single|모두|  
|System.String|모두|  
|System.StringSplitOptions|모두|  
|System.Text.Encoding|모두|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item|  
|System.Text.RegularExpressions.Group|Captures, Success|  
|System.Text.RegularExpressions.GroupCollection|Count, Item|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|.ctor, IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|모두|  
|System.Tuple|모두|  
|System.UInt16|모두|  
|System.UInt32|모두|  
|System.UInt64|모두|  
|System.Uri|모두|  
|System.Xml.Linq.Extensions|모든 메서드 지원됨|  
|System.Xml.Linq.XAttribute|모든 메서드 지원됨|  
|System.Xml.Linq.XCData|모든 메서드 지원됨|  
|System.Xml.Linq.XComment|모든 메서드 지원됨|  
|System.Xml.Linq.XContainer|모든 메서드 지원됨|  
|System.Xml.Linq.XDeclaration|모든 메서드 지원됨|  
|System.Xml.Linq.XDocument|모든 메서드 지원됨|  
|System.Xml.Linq.XDocumentType|모든 메서드 지원됨|  
|System.Xml.Linq.XElement|모든 메서드 지원됨|  
|System.Xml.Linq.XName|모든 메서드 지원됨|  
|System.Xml.Linq.XNamespace|모든 메서드 지원됨|  
|System.Xml.Linq.XNode|모든 메서드 지원됨|  
|System.Xml.Linq.XNodeDocumentOrderComparer|모든 메서드 지원됨|  
|System.Xml.Linq.XNodeEqualityComparer|모든 메서드 지원됨|  
|System.Xml.Linq.XObject|모든 메서드 지원됨|  
|System.Xml.Linq.XProcessingInstruction|모든 메서드 지원됨|  
|System.Xml.Linq.XText|모든 메서드 지원됨|  
|System.Xml.XmlNodeType|모두|  
  
##  <a name="a-namecontextvariablesa-context-variable"></a><a name="ContextVariables"></a> 컨텍스트 변수  
 `context`라는 변수는 모든 정책 [식](api-management-policy-expressions.md#Syntax)에서 암시적으로 사용할 수 있습니다. 해당 멤버는 `\request`와 관련된 정보를 제공합니다. 모든 `context` 멤버는 읽기 전용입니다.  
  
|컨텍스트 변수|허용된 메서드, 속성 및 매개 변수 값|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> 배포<br /><br /> LastError<br /><br /> 작업<br /><br /> 제품<br /><br /> 요청<br /><br /> 응답<br /><br /> 구독<br /><br /> Tracing: bool<br /><br /> 사용자<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: string<br /><br /> ServiceName: string|  
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> context.LastError에 대한 자세한 내용은 [오류 처리](api-management-error-handling-policies.md)를 참조하세요.|  
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 요청 헤더 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 응답 헤더 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|  
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: string<br /><br /> Name: string|  
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> 지정된 형식 중 하나로 요청 또는 응답 메시지 본문을 읽는 데 `context.Request.Body.As<T>` 및 `context.Request.Body.As<T>` 메서드가 사용됩니다. 기본적으로 메시지 본문에 액세스하면 원래 메시지 본문이 손실되며 해당 본문을 읽어오는 식으로 본문을 다시 반환하여 설정해야 합니다. 본문 내용을 보존하려면 메시지에 액세스할 때 `preserveContent` 매개 변수를 `true`로 설정합니다. `preserveContent`의 기본값은 `false`입니다. `preserveContent`가 `true`로 설정되고 식에서 본문이 반환되면 반환된 본문을 사용합니다. 자세한 내용은 [본문 설정](api-management-transformation-policies.md#SetBody) 정책을 참조하세요.|  
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|  
|IUserIdentity|Id: string<br /><br /> Provider: string|  
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 쿼리 매개 변수 값 또는 `defaultValue`(매개 변수가 없는 경우)를 반환합니다.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> `T` 형식으로 캐스팅되는 변수 값 또는 `defaultValue`(변수가 없는 경우)를 반환합니다.<br /><br /> 이 메서드는 지정된 형식이 반환된 변수의 실제 형식과 일치하지 않는 경우 예외를 발생시킵니다.|  
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> 입력 매개 변수에 유효한 HTTP 기본 인증 권한 부여 요청 헤더 값이 포함된 경우 메서드는 `BasicAuthCredentials` 형식의 개체를 반환하고 그렇지 않은 경우 null을 반환합니다.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> 입력 매개 변수에 유효한 HTTP 기본 인증 권한 부여 요청 헤더 값이 포함된 경우 메서드는 `true`를 반환하고 결과 매개 변수는 `BasicAuthCredentials` 형식의 값을 포함합니다. 그렇지 않은 경우 메서드는 `false`를 반환합니다.|  
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|  
|Jwt AsJwt(input: this string)|input: string<br /><br /> 입력 매개 변수에 유효한 JWT 토큰 값이 포함된 경우 메서드는 `Jwt` 형식의 개체를 반환하고 그렇지 않은 경우 `null`을 반환합니다.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> 입력 매개 변수에 유효한 JWT 토큰 값이 포함된 경우 메서드는 `true`를 반환하고 결과 매개 변수는 `Jwt` 형식의 값을 포함합니다. 그렇지 않은 경우 메서드는 `false`를 반환합니다.|  
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 클레임 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|

## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 [API Management의 정책](api-management-howto-policies.md)을 참조하세요.  


<!--HONumber=Jan17_HO2-->


