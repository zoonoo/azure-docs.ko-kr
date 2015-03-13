<properties 
	pageTitle="Azure API 관리 정책 참조" 
	description="API 관리를 구성하기 위해 사용할 수 있는 정책에 대해 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Azure API 관리 정책 참조

이 항목에서는 다음 API 관리(미리 보기) 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API 관리 정책][API 관리 정책]을 참조하세요.

- [액세스 제한 정책][액세스 제한 정책]
    - [사용 할당량][사용 할당량] - 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
    - [비율 제한][비율 제한] - 호출 및/또는 대역폭 사용률을 제한하여 API 사용량 급증을 방지합니다.
    - [호출자 IP 제한][호출자 IP 제한] - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.
- [콘텐츠 변환 정책][콘텐츠 변환 정책]
    - [HTTP 헤더 설정][HTTP 헤더 설정] - 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.
    - [XML을 JSON으로 변환][XML을 JSON으로 변환] - 요청 또는 응답 본문을 XML에서 "JSON" 또는 "XML에 충실한" 양식의 JSON으로 변환합니다.
    - [본문 문자열 바꾸기][본문 문자열 바꾸기] - 요청 또는 응답 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.
    - [쿼리 문자열 매개 변수 설정][쿼리 문자열 매개 변수 설정] - 요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.
- [캐싱 정책][캐싱 정책]
    - [캐시에 저장][캐시에 저장] - 지정된 캐시 구성에 따라 응답을 캐시합니다.
    - [캐시에서 가져오기][캐시에서 가져오기] - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.
- [기타 정책][기타 정책]
    - [URI 다시 쓰기][URI 다시 쓰기] - 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.
    - [콘텐츠의 URL 마스킹][콘텐츠의 URL 마스킹] - 응답 본문 및 위치 헤더의 링크를 재작성(마스킹)하여 프록시를 통해 동일한 링크를 가리키도록 합니다.
    - [도메인 간 호출 허용][도메인 간 호출 허용] - API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 합니다.
    - [JSONP][JSONP] - 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
    - [CORS][CORS] - CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.


## <a name="access-restriction-policies"></a> 액세스 제한 정책

- [사용 할당량][사용 할당량] - 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
- [비율 제한][비율 제한] - 호출 및/또는 대역폭 사용률을 제한하여 API 사용량 급증을 방지합니다.
- [호출자 IP 제한][호출자 IP 제한] - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.

### <a name="usage-quota"></a> 사용 할당량

**설명:**
갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용합니다.

**정책 명령문:**

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	    <api name="name" calls="number" bandwidth="kilobytes">
	        <operation name="name" calls="number" bandwidth="kilobytes" />
	    </api>
	</quota>


**예제:**

	<policies>
		<inbound>
			<base />
			<quota calls="10000" bandwidth="40000" renewal-period="3600" />
		</inbound>
		<outbound>
			<base />
		</outbound>
	</policies>

**적용할 수 있는 위치:**
제품 범위의 inbound 섹션에서 사용합니다.

**적용할 수 있는 경우:**
제품에 대해 작업 및/또는 API의 사용을 제한해야 하는 경우에 사용합니다.

**적용해야 하는 이유:**
시간 및 대역폭에 따라 제품의 사용 제한을 정의하기 위해. API가 정의된 할당량 한도에 도달하는 경우 후속 호출이 오류 메시지와 함께 거부됩니다. 일반적으로 할당량은 미리 정의된 시간 간격 및 대역폭 한도 동안 허용된 요청 메시지의 수로 정의됩니다.

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>quota calls="number"</td>
  <td>renewal-period 동안 허용된 최대 구독 호출 수(즉, 10000)</td>
</tr>
<tr>
  <td>bandwidth="kilobytes"</td>
  <td>지정된 renewal-period 동안 이 제품, API 또는 작업의 사용에 허용되는 최대 킬로바이트의 수(즉, 40000).</td>
</tr>
<tr>
  <td>renewal-period="seconds"</td>
  <td>할당량이 적용되는 동안 초 단위의 시간 간격(즉, 3600).</td>
</tr>
<tr>
  <td>api name="name"</td>
  <td>할당량이 적용되는 API 호출의 이름</td>
</tr>
<tr>
  <td>calls="number"</td>
  <td>지정된 renewal-period 동안 API 또는 작업에 대해 호출할 수 있는 최대 수(즉, 5000).</td>
</tr>
<tr>
  <td>operation name="name"</td>
  <td>할당량이 적용되는 작업의 이름</td>
</tr>
</tbody>
</table>

### <a name="rate-limit"></a> 비율 제한

**설명:**
API 및 특정 API 작업(옵션)에 대한 요청 흐름의 비율을 제한하여 API 사용량 급증을 저지합니다. 이 정책이 트리거되는 경우 소비자는 `429 Too Many Requests` 응답 상태 코드를 수신합니다.

**정책 명령문:**

	<rate-limit calls="number" renewal-period="seconds">
    	<api name="name" calls="number" renewal-period="seconds">
    		<operation name="name" calls="number" renewal-period="seconds" />
    	</api>
    </rate-limit>

**예제:**

	<policies>
		<inbound>
			<base />
			<rate-limit calls="20" renewal-period="90" />
		</inbound>
		<outbound>
			<base />        
		</outbound>
	</policies>


**적용할 수 있는 위치:**
제품 범위의 inbound 섹션에서 사용합니다.

<table>
<thead>
<tr>
  <th>요소/특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>calls="number"</td>
  <td>갱신 기간당 허용되는 호출 수</td>
</tr>
<tr>
  <td>renewal-period="seconds"</td>
  <td>비율 제한 호출 할당량이 갱신된 이후 시간 간격</td>
</tr>
<tr>
  <td>api name="name"</td>
  <td>비율 제한이 적용되는 API 이름</td>
</tr>
<tr>
  <td>operation name="name"</td>
  <td>비율 제한이 적용되는 작업 이름</td>
</tr>
</tbody>
</table>

### <a name="caller-ip-restriction"></a> 호출자 IP 제한

**설명:**
특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.

**정책 명령문:**

	<ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**예제:**

	<ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**적용할 수 있는 위치:**
모든 범위의 inbound 섹션에서 사용합니다.

**적용해야 하는 경우:**
서비스에 액세스할 수 있는 대상에 대한 특정 제어가 필요한 경우 이 정책을 사용합니다.

**적용해야 하는 이유:**
호스트별 또는 호스트 범위별 기준으로 액세스에 대한 엄격한 제어가 필요한 경우(예를 들어 높은 수준의 보안 요구 사항이 있는 서비스의 경우)에만 이 정책이 필요합니다.

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>ip-filter action="allow | forbid"</td>
  <td>지정된 주소에 대해 호출을 허용해야 할지 여부를 지정합니다.</td>
</tr>
<tr>
  <td>address="address"</td>
  <td>액세스를 허용 또는 거부할 하나 이상의 IP 주소</td>
</tr>
<tr>
  <td>address-range from="address" to="address"</td>
  <td>액세스를 허용 또는 거부할 IP 주소 범위</td>
</tr>
</tbody>
</table>

## <a name="content-transformation-policies"></a> 콘텐츠 변환 정책

- [HTTP 헤더 설정][HTTP 헤더 설정] - 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.
- [XML을 JSON으로 변환][XML을 JSON으로 변환] - 요청 또는 응답 본문을 XML에서 "JSON" 또는 "XML에 충실한" 양식의 JSON으로 변환합니다.
- [본문 문자열 바꾸기][본문 문자열 바꾸기] - 요청 또는 응답 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.
- [쿼리 문자열 매개 변수 설정][쿼리 문자열 매개 변수 설정] - 요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.

### <a name="set-http-header"></a> HTTP 헤더 설정

**설명:**
기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.

HTTP 헤더 목록을 HTTP 메시지에 삽입합니다. 이 정책을 인바운드 파이프라인에 지정하면 이 정책은 대상 서비스로 전달되는 요청의 HTTP 헤더를 설정합니다. 이 정책을 아웃바운드 파이프라인에 지정하면 이 정책은 프록시 클라이언트로 전송되는 응답의 HTTP 헤더를 설정합니다.

**정책 명령문:**

	<set-header name="header name" exists-action="override | skip | append | delete">
	    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
	</set-header>


**예제:**

	<set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>


**적용할 수 있는 위치:**
*게시자*가 아닌 모든 범위의 inbound 및 outbound 섹션에서 사용합니다.

**적용해야 하는 경우:**
HTTP 트랜잭션의 운영 매개 변수 및/또는 반환 값을 지정하는 데 사용합니다.

**적용해야 하는 이유:**
대다수 HTTP 요청 및 여러 응답의 경우 입력/응답 매개 변수를 지정하는 데 헤더가 필요합니다. 따라서 이 정책은 거의 모든 서비스에 적용할 수 있습니다.

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>reconcile-action="override|skip|append"</td>
  <td>헤더가 이미 지정된 경우 수행할 작업을 지정합니다. 참고: "override"로 설정된 경우 동일한 이름으로 여러 항목을 등록하면 모든 항목(여러 번 나열됨)에 따라 헤더가 설정됩니다. 나열된 값만 설정됩니다.</td>
</tr>
<tr>
  <td>header name="header name"</td>
  <td>설정할 헤더의 이름을 지정합니다. 필수입니다.</td>
</tr>
<tr>
  <td>exists-action="override | skip | append"</td>
  <td>헤더가 이미 지정된 경우 수행할 작업을 지정합니다.</td>
</tr>
<tr>
  <td>value="value"</td>
  <td>설정할 헤더의 값을 지정합니다.</td>
</tr>
</tbody>
</table>

### <a name="convert-xml-to-json"></a> XML을 JSON으로 변환

**설명:**
요청 또는 응답 본문을 XML에서 JSON으로 변환합니다.

**정책 명령문:**

	<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**예제:**


	<policies>
		<inbound>
			<base />
		</inbound>
		<outbound>
			<base />
			<xml-to-json kind="direct" apply="always" consider-accept-header="false" />
		</outbound>
	</policies>


**적용할 수 있는 위치:**
API 또는 작업 범위의 inbound 또는 outbound 섹션에서 사용합니다.

**적용해야 하는 이유:**
XML 전용 백 엔드 웹 서비스를 기반으로 하는 API를 최신 형식으로 변환하기 위해

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>kind="javascript-friendly | direct</td>
  <td>변환된 JSON이 JavaScript 개발자에게 익숙한 양식입니다. | 변환된 JSON은 원래 XML 문서 구조를 반영합니다.</td>
</tr>
<tr>
  <td>apply= always | content-type-xml</td>
  <td>항상 변환 <code>Content-Type</code> 헤더가 XML이 있다고 나타낼 경우에만 변환</td>
</tr>
<tr>
  <td>consider-accept-header="true | false"</td>
  <td><code>Accept</code> 헤더의 값에 따라 변환 적용 | 항상 변환</td>
</tr>
</tbody>
</table>

### <a name="replace-string-in-body"></a> 본문 문자열 바꾸기

**설명:**
요청 또는 응답에서 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.

**정책 명령문:**

	<find-and-replace from="what to replace" to="replacement" />

**예제:**

	<find-and-replace from="notebook" to="laptop" />


**적용할 수 있는 위치:**
모든 범위의 inbound 및 outbound 섹션에서 사용합니다.

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>from="what to replace"</td>
  <td>검색할 문자열</td>
</tr>
<tr>
  <td>to="replacement"</td>
  <td>위에서 찾은 문자열을 대신할 문자열</td>
</tr>
</tbody>
</table>

### <a name="set-query-string-parameter"></a> 쿼리 문자열 매개 변수 설정

**설명:**
요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.

**정책 명령문:**

	<set-query-parameter name="param name" exists-action="override | skip | append | delete">
	    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
	</set-query-parameter>


**예제:**

	<policies>
		<inbound>
			<base />
			<set-query-parameter>
				<parameter name="api-key" exists-action="skip">
 					<value>12345678901</value>
        		</parameter>
    			<!-- for multiple parameters with the same name add additional value elements -->
    		</set-query-parameter>
  		</inbound>
  		<outbound>
			<base />
    	</outbound>
	</policies>

**적용할 수 있는 위치:**
모든 범위의 inbound 섹션에서 사용할 수 있습니다.

**적용해야 하는 이유:**
백 엔드 서비스에 필요한 쿼리 매개 변수를 전달하는 데 사용합니다. 그러나 요청의 쿼리 매개 변수는 옵션입니다.

<table>
<thead>
<tr>
  <th>요소/특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>name="name"</td>
  <td>매개 변수를 명명하는 문자열</td>
</tr>
<tr>
  <td>exists-action="override"</td>
  <td>요청의 매개 변수 값(있는 경우)을 바꿉니다.</td>
</tr>
<tr>
  <td>exists-action="skip"</td>
  <td>요청에 매개 변수가 있는 경우 아무 작업도 하지 않습니다.</td>
</tr>
<tr>
  <td>exists-action="append"</td>
  <td>기존 요청 매개 변수에 값을 추가합니다.</td>
</tr>
<tr>
  <td>exists-action="delete"</td>
  <td>요청에서 매개 변수를 제거합니다.*</td>
</tr>
<tr>
  <td>value="value"</td>
  <td>묶는 요소의 매개 변수 값을 설정합니다.</td>
</tr>
</tbody>
</table>

## <a name="caching-policies"></a> 캐싱 정책

- [캐시에 저장][캐시에 저장] - 지정된 캐시 구성에 따라 응답을 캐시합니다.
- [캐시에서 가져오기][캐시에서 가져오기] - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.

### <a name="store-to-cache"></a> 캐시에 저장

**설명:**
지정된 캐시 설정에 따라 응답을 캐시합니다. 해당하는 [캐시에서 가져오기][캐시에서 가져오기] 정책이 있어야 합니다.

**정책 명령문:**

	cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**예제:**

	<policies>
		<inbound>
       		<base />
		      <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
    				<vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
    				<vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
    				<vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
   					<vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
   			</cache-lookup>
       	</inbound>
 		<outbound>
	     	<base />
				<cache-store duration="3600" caching-mode="cache-on" />
   		</outbound>
	</policies>

**적용할 수 있는 위치:**
API와 작업 범위 중 하나 또는 둘 다의 outbound 섹션에서 사용할 수 있습니다.

**적용해야 하는 경우:**
응답 콘텐츠가 일정 기간 동안 정적 상태인 경우 적용해야 합니다.

**적용해야 하는 이유:**
응답 캐싱은 백 엔드 웹 서버에 부과된 대역폭 및 처리 요구 사항을 줄이며 API 소비자가 인지하는 대기 시간을 줄여 줍니다.

<table>
<thead>
<tr>
  <th>요소/특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>seconds</td>
  <td>캐시된 항목의 TTL(Time-to-Live)로 초 단위이며 기본값 = 3600</td>
</tr>
<tr>
  <td>cache-on | do-not-cache</td>
  <td>응답이 캐시됩니다. | 응답이 캐시되지 않으며, 캐시 관련 헤더가 다운스트림 캐싱을 금지하도록 설정됩니다.</td>
</tr>
</tbody>
</table>

### <a name="get-from-cache"></a> 캐시에서 가져오기

**설명:**
캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다. API 소비자의 캐시 유효성 검사 요청에 적절하게 응답합니다. 해당하는 [캐시에 저장][캐시에 저장] 정책이 있어야 합니다.

**정책 명령문:**

	<cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
    	<vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
    	<vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
    	<vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
   		<vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**예제:**

	<policies>
      	<inbound>
      		<base />
	    	<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
      			<vary-by-query-parameter>version</vary-by-query-parameter>
      			<vary-by-header>Accept</vary-by-header>
      			<vary-by-header>Accept-Charset</vary-by-header>
    		</cache-lookup>		 	
		</inbound>
 		<outbound>
 			<cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
			<base /> 		
 		</outbound>
	</policies>

**적용할 수 있는 위치:**
API와 작업 범위 중 하나 또는 둘 다의 inbound 섹션에 사용할 수 있습니다.

**적용해야 하는 경우:**
응답 콘텐츠가 일정 기간 동안 정적 상태인 경우 적용해야 합니다.

**적용해야 하는 이유:**
응답 캐싱은 백 엔드 웹 서버에 부과된 대역폭 및 처리 요구 사항을 줄이며 API 소비자가 인지하는 대기 시간을 줄여 줍니다.

<table>
<thead>
<tr>
  <th>요소/특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>vary-by-developer="true | false"</td>
  <td>*true*로 설정하면 개발자 키별 응답 캐싱을 시작합니다. *false*가 기본값으로 설정됩니다.</td>
</tr>
<tr>
  <td>vary-by-developer-groups="true | false"</td>
  <td>*true*로 설정하면 사용자 역할별 응답 캐싱을 시작합니다. *false*가 기본값으로 설정됩니다.</td>
</tr>
<tr>
  <td>downstream-caching-type="none | private | public"</td>
  <td>*none* - 다운스트림 캐싱이 허용되지 않습니다. 기본값으로 설정됩니다. | *private* - 다운스트림 개인 캐싱이 허용됩니다. | 
*public* - 개인 및 공유 다운스트림 캐싱이 허용됩니다.</td>
</tr>
<tr>
  <td>vary-by-header: "Accept"</td>
  <td><code>Accept</code> 헤더의 값에 따라 응답 캐싱을 시작합니다.</td>
</tr>
<tr>
  <td>vary-by-header: Accept-Charset"</td>
  <td><code>Accept-Charset</code> 헤더의 값에 따라 응답 캐싱을 시작합니다.</td>
</tr>
<tr>
  <td>vary-by-header: "header name"</td>
  <td>지정된 헤더(예: <code>Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match</code>의 값에 따라 응답 캐싱을 시작합니다.  </td>
</tr>
<tr>
  <td>vary-by-query-parameter: "parameter name"</td>
  <td>지정된 쿼리 매개 변수의 값에 따라 응답 캐싱을 시작합니다. 단일 또는 여러 매개 변수를 입력합니다. 세미콜론을 구분 기호로 사용합니다. 지정하지 않은 경우 모든 쿼리 매개 변수가 사용됩니다.</td>
</tr>
</tbody>
</table>

## <a name="other-policies"></a> 기타 정책

- [URI 다시 쓰기][URI 다시 쓰기] - 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.
- [콘텐츠의 URL 마스킹][콘텐츠의 URL 마스킹] - 응답 본문 및 위치 헤더의 링크를 재작성(마스킹)하여 프록시를 통해 동일한 링크를 가리키도록 합니다.
- [도메인 간 호출 허용][도메인 간 호출 허용] - API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 합니다.
- [JSONP][JSONP] - 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
- [CORS][CORS] - CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.

### <a name="rewrite-uri"></a> URI 다시 쓰기

**설명:**
요청 URL을 공용 양식에서 웹 서비스의 필요한 양식으로 변환합니다.

**공용 URL:** <http://api.example.com/storenumber/ordernumber>

**요청 URL:** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>.

재작성 URL에서 추가 템플릿 경로 매개 변수를 추가하지 마세요. 쿼리 문자열 매개 변수만 추가할 수 있습니다.

**정책 명령문:**

	<rewrite-uri template="uri template" />

**예제:**


	<policies>
		<inbound>
			<base />
			<rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
		</inbound>
		<outbound>
			<base />
		</outbound>
	</policies>


**적용할 수 있는 위치:**
작업 범위에서만 inbound 섹션에서 사용할 수 있습니다.

**적용해야 하는 경우:**
사용자에게 친숙하거나 브라우저에서 잘 작동하는 URL을 웹 서비스에 필요한 URL 형식으로 변환해야 하는 경우에 사용합니다.

**적용해야 하는 이유:**
이 정책은 대체 URL 형식을 공개할 경우에만 적용해야 합니다. 클린 URL, RESTful URL, 사용자에게 친숙한 URL 또는 SEO 지원 URL은 쿼리 문자열이 포함되지 않고 리소스 경로(체계 및 권한 뒤에)만 포함된 전적으로 구조적인 URL입니다. 보통 미학, 사용 편의성 또는 SEO(검색 엔진 최적화)를 위해 사용합니다.

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>template="uri template"</td>
  <td>쿼리 문자열 매개 변수가 포함된 실제 웹 서비스 URL</td>
</tr>

</tbody>
</table>

### <a name="mask-urls-in-content"></a> 콘텐츠의 URL 마스킹

**설명**
응답 본문 및 위치 헤더의 링크를 재작성(마스킹)하여 프록시를 통해 동일한 링크를 가리키도록 합니다.

**정책 명령문:**

	<redirect-body-urls />

**예제:**

	<redirect-body-urls />


**적용할 수 있는 위치:**
*API* 및 *작업* 범위에서 적용합니다. inbound 및 outbound 섹션에서 적용할 수 있습니다.

### <a name="allow-cross-domain-calls"></a> 도메인 간 호출 허용

**설명:**
API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 설정합니다.

**정책 명령문:**

	<cross-domain />

**예제:**

	<cross-domain />


**적용할 수 있는 위치:**
*게시자* 범위의 inbound 섹션에서 사용합니다.

### <a name="jsonp"></a> JSONP

**설명:**
패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다. JSONP는 JavaScript 프로그램에서 다른 도메인의 서버로부터 데이터를 요청하는 데 사용하는 메서드입니다. JSONP는 웹 페이지에 대한 액세스 권한이 동일한 도메인 내에 있어야 하는 경우 대부분의 웹 브라우저에서 적용하는 제한을 무시합니다.

**정책 명령문:**

	<jsonp callback-parameter-name="callback function name" />

**예제:**

 	<jsonp callback-parameter-name="cb" />

콜백 매개 변수 `?cb=XXX` 없이 메서드를 호출하는 경우 일반 JSON(함수 호출 래퍼 없이)이 반환됩니다.

콜백 매개 변수 `?cb=XXX`를 추가한 경우 `XXX('<json result goes here>')`;와 같이 콜백 함수 주위에 원래 JSON 결과를 래핑한 JSONP 결과가 반환됩니다.


**적용할 수 있는 위치:**
outbound 섹션에서만 사용합니다.

**적용해야 하는 경우:**
다른 도메인의 서버에서 데이터를 요청하려면 적용해야 합니다.

<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td>callback-parameter-name</td>
  <td>함수가 상주하는 정규화된 도메인 이름이 접두사로 지정된 도메인 간 JavaScript 함수 호출</td>
</tr>
</tbody>
</table>

### <a name="cors"></a> CORS

**설명:**
CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.

CORS를 통해 브라우저와 서버가 상호 작용하여 특정 원본 간 요청(즉, 웹 페이지의 JavaScript에서 다른 도메인으로 실행한 XMLHttpRequests 호출)을 허용할지 여부를 결정할 수 있습니다. 따라서 동일 원본의 요청만 허용하는 것보다 유연성이 더 뛰어나며 모든 원본 간 요청을 허용하는 것보다 보안도 더 높아집니다.

**정책 명령문:**

	 <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**예제:**

 	<cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>


**적용할 수 있는 위치:**
*API* 및 *Operation* 범위에서만 inbound 섹션에서 사용할 수 있습니다.


<table>
<thead>
<tr>
  <th>특성</th>
  <th>설명</th>
</tr>
</thead>
<tbody>
<tr>
  <td><origin>*</origin></td>
  <td>모두 또는 하나 이상의 특정 URI 목록 허용</td>
</tr>
<tr>
  <td><origin>URI</origin></td>
  <td>URI에는 체계, 호스트 및 포트가 포함되어야 합니다. 포트를 생략하면 http의 경우 포트 80을 가정하고 https의 경우 포트 443을 가정합니다.</td>
</tr>
</tbody>
</table>


[API 관리 정책]: ../api-management-howto-policies
[액세스 제한 정책]: #access-restriction-policies
[사용 할당량]: #usage-quota
[비율 제한]: #rate-limit
[호출자 IP 제한]: #caller-ip-restriction
[콘텐츠 변환 정책]: #content-transformation-policies
[HTTP 헤더 설정]: #set-http-header
[XML을 JSON으로 변환]: #convert-xml-to-json
[본문 문자열 바꾸기]: #replace-string-in-body
[쿼리 문자열 매개 변수 설정]: #set-query-string-parameter
[캐싱 정책]: #caching-policies
[캐시에 저장]: #store-to-cache
[캐시에서 가져오기]: #get-from-cache
[기타 정책]: #other-policies
[URI 다시 쓰기]: #rewrite-uri
[콘텐츠의 URL 마스킹]: #mask-urls-in-content
[도메인 간 호출 허용]: #allow-cross-domain-calls
[JSONP]: #jsonp
[CORS]: #cors

<!--HONumber=46--> 
