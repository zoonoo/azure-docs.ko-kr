---
title: Azure API Management 정책 식 | Microsoft Docs
description: Azure API Management에서 정책 식에 대해 자세히 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244005"
---
# <a name="api-management-policy-expressions"></a>API Management 정책 식
이 문서에서는 C# 7의 정책 식 구문에 대해 설명합니다. 각 식에서는 암시적으로 제공된 [context](api-management-policy-expressions.md#ContextVariables) 변수 및 .NET Framework 형식의 허용된 [하위 집합](api-management-policy-expressions.md#CLRTypes)에 액세스할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- 백 엔드 서비스에 컨텍스트 정보를 제공하는 방법을 참조합니다. [쿼리 문자열 매개 변수 설정](api-management-transformation-policies.md#SetQueryStringParameter) 및 [HTTP 헤더 설정](api-management-transformation-policies.md#SetHTTPheader) 정책을 사용하여 이 정보를 제공합니다.
- [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 토큰 클레임에 따라 작업에 대한 액세스 권한을 미리 부여하는 방법을 참조합니다.
- [API 검사기](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) 추적을 사용하여 정책을 평가하는 방법 및 평가 결과를 확인하는 방법을 참조합니다.
- [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) 및 [캐시에 저장](api-management-caching-policies.md#StoreToCache) 정책이 있는 식을 사용하여 API Management 응답 캐싱을 구성하는 방법을 참조합니다. 백 엔드 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 기간을 설정합니다.
- 콘텐츠 필터링을 수행하는 방법을 참조합니다. [흐름 제어](api-management-advanced-policies.md#choose) 및 [본문 설정](api-management-transformation-policies.md#SetBody) 정책을 사용하여 백 엔드에서 받은 응답의 데이터 요소를 제거합니다.
- 정책 설명서를 다운로드하려면 [Api 관리-샘플/정책](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub 리포지토리를 참조하세요.


## <a name="syntax"></a><a name="Syntax"></a>구문
단일 문 식은 `@(expression)`으로 묶으며 여기서 `expression`은 올바르게 구성된 C# 식입니다.

다중 문 식은 `@{expression}`으로 묶습니다. 다중 문 식 내에 모든 코드 경로는 `return` 문으로 끝나야 합니다.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>예제

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>사용
정책 참조에서 다르게 지정하지 않는 한, 식은 어떤 API Management [정책](api-management-policies.md)에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다.

> [!IMPORTANT]
> 정책 식을 사용하는 경우 정책을 정의할 때 정책 식에 대해 제한된 검증만 이루어집니다. 식은 런타임 시 게이트웨이에 의해 실행됩니다. 정책 식에 의해 생성된 모든 예외는 런타임 오류가 발생합니다.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> 정책 식에 허용된 .NET Framework 형식
다음 표에서는 .NET Framework 형식과 정책 식에 허용된 멤버를 보여 줍니다.

|Type|지원되는 멤버|
|--------------|-----------------------|
|뉴턴소프트.Json.포맷팅|모두|
|뉴턴소프트.Json.JsonConvert|직렬화개체, 직렬화오브젝트|
|Newtonsoft.Json.Linq.Extensions|모두|
|Newtonsoft.Json.Linq.JArray|모두|
|Newtonsoft.Json.Linq.JConstructor|모두|
|Newtonsoft.Json.Linq.JContainer|모두|
|Newtonsoft.Json.Linq.JObject|모두|
|Newtonsoft.Json.Linq.JProperty|모두|
|Newtonsoft.Json.Linq.JRaw|모두|
|Newtonsoft.Json.Linq.JToken|모두|
|Newtonsoft.Json.Linq.JTokenType|모두|
|Newtonsoft.Json.Linq.JValue|모두|
|System.Array|모두|
|System.BitConverter|모두|
|System.Boolean|모두|
|System.Byte|모두|
|System.Char|모두|
|System.Collections.Generic.Dictionary<TKey, TValue>|모두|
|시스템.컬렉션.일반.해시셋\<t>|모두|
|시스템.컬렉션.일반.I컬렉션\<t>|모두|
|System.Collections.Generic.IDictionary<TKey, TValue>|모두|
|System.Collections.Generic.IEnumerable\<T>|모두|
|System.Collections.Generic.IEnumerator\<T>|모두|
|시스템.컬렉션.일반.IList\<T>|모두|
|System.Collections.Generic.IReadOnlyCollection\<T>|모두|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|모두|
|시스템.컬렉션.일반.ISet\<T>|모두|
|System.Collections.Generic.KeyValuepair<TKey, TValue>|모두|
|시스템.컬렉션.일반.목록\<T>|모두|
|System.Collections.Generic.Queue\<T>|모두|
|System.Collections.Generic.Stack\<T>|모두|
|System.Convert|모두|
|System.DateTime|(생성자), 추가, 추가 일, 추가 시간, 추가 밀리 초, 추가 분, 추가 달, 추가 초, 추가, 추가 년, 날짜, 일, DayofWeek, DayOfYear, DaysInMonth, 시간, IsDaylightSavingTime, IsLeapYear, 최대 값, 밀리 초, 분, 최소 값, 월, 지금 , 구문 분석, 두 번째, 빼기, 진드기, 시간OfDay, 오늘, 토스트링, UtcNow, 년|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|모두|
|System.Decimal|모두|
|System.Double|모두|
|System.Exception|모두|
|System.Guid|모두|
|System.Int16|모두|
|System.Int32|모두|
|System.Int64|모두|
|시스템.IO.스트링리더|모두|
|시스템.IO.스트링라이터|모두|
|시스템.Linq.열거 가능|모두|
|System.Math|모두|
|System.MidpointRounding|모두|
|System.Net.WebUtility|모두|
|System.Nullable|모두|
|System.Random|모두|
|System.SByte|모두|
|System.Security.Cryptography.AsymmetricAlgorithm|모두|
|시스템.보안.암호화.암호 모드|모두|
|System.Security.Cryptography.HashAlgorithm|모두|
|시스템.보안.암호화.해시 알고리즘 이름|모두|
|System.Security.Cryptography.HMAC|모두|
|System.Security.Cryptography.HMACMD5|모두|
|System.Security.Cryptography.HMACSHA1|모두|
|System.Security.Cryptography.HMACSHA256|모두|
|System.Security.Cryptography.HMACSHA384|모두|
|System.Security.Cryptography.HMACSHA512|모두|
|System.Security.Cryptography.KeyedHashAlgorithm|모두|
|System.Security.Cryptography.MD5|모두|
|시스템.보안.암호화.Oid|모두|
|시스템.보안.암호화.패딩 모드|모두|
|System.Security.Cryptography.RNGCryptoServiceProvider|모두|
|System.Security.Cryptography.RSA|모두|
|시스템.보안.암호화.RSA 암호화 패딩|모두|
|시스템.보안.암호화.RSA서명 패딩|모두|
|System.Security.Cryptography.SHA1|모두|
|System.Security.Cryptography.SHA1Managed|모두|
|System.Security.Cryptography.SHA256|모두|
|System.Security.Cryptography.SHA256Managed|모두|
|System.Security.Cryptography.SHA384|모두|
|System.Security.Cryptography.SHA384Managed|모두|
|System.Security.Cryptography.SHA512|모두|
|System.Security.Cryptography.SHA512Managed|모두|
|시스템.보안.암호화.대칭 알고리즘|모두|
|시스템.보안.암호화.X509 인증서.퍼블릭키|모두|
|System.Security.Cryptography.X509인증서.RSA인증서확장|모두|
|System.Security.Cryptography.X509인증서.X500고유이름|이름|
|System.Security.Cryptography.X509 인증서.X509 인증서|모두|
|System.Security.Cryptography.X509Certificates.X509Certificate2|모두|
|시스템.보안.암호화.X509 인증서.X509콘텐츠 유형|모두|
|System.Security.Cryptography.X509인증서.X509네임타입|모두|
|System.Single|모두|
|System.String|모두|
|System.StringComparer|모두|
|System.StringComparison|모두|
|System.StringSplitOptions|모두|
|System.Text.Encoding|모두|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(생성자), IsMatch, 일치, 일치, 바꾸기, 이스케이프 해제, 분할|
|System.Text.RegularExpressions.RegexOptions|모두|
|System.Text.StringBuilder|모두|
|System.TimeSpan|모두|
|System.TimeZone|모두|
|시스템.시간대정보.조정규칙|모두|
|시스템.시간대정보.전환 시간|모두|
|System.TimeZoneInfo|모두|
|System.Tuple|모두|
|System.UInt16|모두|
|System.UInt32|모두|
|System.UInt64|모두|
|System.Uri|모두|
|System.UriPartial|모두|
|System.Xml.Linq.Extensions|모두|
|System.Xml.Linq.XAttribute|모두|
|System.Xml.Linq.XCData|모두|
|System.Xml.Linq.XComment|모두|
|System.Xml.Linq.XContainer|모두|
|System.Xml.Linq.XDeclaration|모두|
|System.Xml.Linq.XDocument|모두, 제외: 로드|
|System.Xml.Linq.XDocumentType|모두|
|System.Xml.Linq.XElement|모두|
|System.Xml.Linq.XName|모두|
|System.Xml.Linq.XNamespace|모두|
|System.Xml.Linq.XNode|모두|
|System.Xml.Linq.XNodeDocumentOrderComparer|모두|
|System.Xml.Linq.XNodeEqualityComparer|모두|
|System.Xml.Linq.XObject|모두|
|System.Xml.Linq.XProcessingInstruction|모두|
|System.Xml.Linq.XText|모두|
|System.Xml.XmlNodeType|모두|

## <a name="context-variable"></a><a name="ContextVariables"></a> 컨텍스트 변수
`context`라는 변수는 모든 [식](api-management-policy-expressions.md#Syntax)에서 암시적으로 사용할 수 있습니다. 해당 멤버는 `\request`와 관련된 정보를 제공합니다. 모든 `context` 멤버는 읽기 전용입니다.

|컨텍스트 변수|허용된 메서드, 속성 및 매개 변수 값|
|----------------------|-------------------------------------------------------|
|컨텍스트|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [배포](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan - 타임스탬프 값과 현재 시간 사이의 시간 간격<br /><br /> [마지막 오류](#ref-context-lasterror)<br /><br /> [작업](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [요청](#ref-context-request)<br /><br /> RequestId: Guid - 고유한 요청 식별자<br /><br /> [Response](#ref-context-response)<br /><br /> [구독](#ref-context-subscription)<br /><br /> Timestamp: DateTime - 요청이 수신된 시점<br /><br /> Tracing: bool - 추적의 설정 여부를 나타냅니다. <br /><br /> [사용자](#ref-context-user)<br /><br /> [변수](#ref-context-variables): IReadOnlyDictionary<문자열, 개체><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> 서비스Url: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> context.LastError에 대한 자세한 내용은 [오류 처리](api-management-error-handling-policies.md)를 참조하세요.|
|<a id="ref-context-operation"></a>context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|아피스: 이넘너너블<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> 그룹: [IGroup에](#ref-igroup)<\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>context.Request|본문: [IMessageBody](#ref-imessagebody) 또는 `null` 요청에 본문이 없는 경우.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [헤더](#ref-context-request-headers): IReadOnlyDictionary<문자열, 문자열[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> 오리지널 Url: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 요청 헤더 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|
|<a id="ref-context-response"></a>context.Response|본체: [IMessageBody](#ref-imessagebody)<br /><br /> [헤더](#ref-context-response-headers): IReadOnlyDictionary<문자열, 문자열[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 응답 헤더 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: string<br /><br /> FirstName: string<br /><br /> 그룹: [IGroup에](#ref-igroup)<\><br /><br /> Id: string<br /><br /> 아이덴티티: [IUserID에](#ref-iuseridentity)<\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> 서비스Url: [IUrl](#ref-iurl)<br /><br /> 구독키매개 이름: [ISubscription키매개 변수 이름](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|<T\>(보존 콘텐츠 : bool = 거짓): 여기서 T : 문자열, 바이트 [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> 지정된 형식 `T`에서 요청 또는 응답 메시지 본문을 읽는 데 `context.Request.Body.As<T>` 또는 `context.Response.Body.As<T>` 메서드가 사용됩니다. 기본적으로 이 메서드는 원래 메시지 본문 스트림을 사용하고 반환된 후에는 사용할 수 없게 렌더링합니다. 이 메서드가 본문 스트림의 복사본에 대해 작동하도록 하여 이러한 문제를 피하려면 `preserveContent` 매개 변수를 `true`로 설정합니다. 예제를 보려면 [여기](api-management-transformation-policies.md#SetBody)로 이동하세요.|
|<a id="ref-iurl"></a>IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> [쿼리](#ref-iurl-query): IReadOnlyDictionary<문자열,문자열[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: string<br /><br /> Provider: string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 쿼리 매개 변수 값 또는 `defaultValue`(매개 변수가 없는 경우)를 반환합니다.|
|<a id="ref-context-variables"></a>T 컨텍스트입니다. 변수.GetValueOrDefault<\>T (변수 이름: 문자열, 기본값: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> `T` 형식으로 캐스팅되는 변수 값 또는 `defaultValue`(변수가 없는 경우)를 반환합니다.<br /><br /> 이 메서드는 지정된 형식이 반환된 변수의 실제 형식과 일치하지 않는 경우 예외를 발생시킵니다.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> 입력 매개 변수에 유효한 HTTP 기본 인증 권한 부여 요청 헤더 값이 포함된 경우 메서드는 `BasicAuthCredentials` 형식의 개체를 반환하고 그렇지 않은 경우 null을 반환합니다.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> 입력 매개 변수가 요청 헤더에 유효한 HTTP 기본 인증 권한 부여 값을 포함하는 경우 메서드는 `true`를 반환하고 결과 매개 변수는 `BasicAuthCredentials` 형식의 값을 포함합니다. 그렇지 않은 경우 메서드는 `false`를 반환합니다.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> 입력 매개 변수에 유효한 JWT 토큰 값이 포함된 경우 메서드는 `Jwt` 형식의 개체를 반환하고 그렇지 않은 경우 `null`을 반환합니다.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> 입력 매개 변수에 유효한 JWT 토큰 값이 포함된 경우 메서드는 `true`를 반환하고 결과 매개 변수는 `Jwt` 형식의 값을 포함합니다. 그렇지 않은 경우 메서드는 `false`를 반환합니다.|
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> 발행: 날짜 시간?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> 쉼표로 구분된 클레임 값 또는 `defaultValue`(헤더가 없는 경우)를 반환합니다.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 암호화할 일반 텍스트<br /><br />alg - 대칭 암호화 알고리즘의 이름<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />암호화된 일반 텍스트를 반환합니다.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 암호화할 일반 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />암호화된 일반 텍스트를 반환합니다.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 암호화할 일반 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />암호화된 일반 텍스트를 반환합니다.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 해독할 암호 텍스트<br /><br />alg - 대칭 암호화 알고리즘의 이름<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />일반 텍스트를 반환합니다.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 해독할 암호 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />일반 텍스트를 반환합니다.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 해독할 암호 텍스트<br /><br />alg - 암호화 알고리즘<br /><br />key - 암호화 키<br /><br />iv - 초기화 벡터<br /><br />일반 텍스트를 반환합니다.|
|bool VerifyNorevocation (입력: 이 시스템.보안.암호화.X509 인증서.X509 인증서2)|인증서 해지 상태를 확인하지 않고 X.509 체인 유효성 검사를 수행합니다.<br /><br />입력 - 인증서 개체<br /><br />유효성 `true` 검사가 성공하면 반환됩니다. `false` 유효성 검사가 실패하면|


## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)
