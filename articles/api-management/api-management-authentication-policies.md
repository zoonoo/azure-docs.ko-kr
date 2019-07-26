---
title: Azure API Management 인증 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 인증 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 5ca9bd4964cf190eaa2be6d66d57c7ada971d675
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442395"
---
# <a name="api-management-authentication-policies"></a>API Management 인증 정책
이 토픽에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.

##  <a name="AuthenticationPolicies"></a> 인증 정책

-   [기본 사용 인증](api-management-authentication-policies.md#Basic) - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.

-   [클라이언트 인증서 사용 인증](api-management-authentication-policies.md#ClientCertificate) - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.

-   [관리 id로 인증](api-management-authentication-policies.md#ManagedIdentity) -API Management 서비스에 대 한 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 인증 합니다.

##  <a name="Basic"></a> 기본 사용 인증
 `authentication-basic` 정책을 사용하여 기본 인증을 사용하는 백 엔드 서비스를 인증합니다. 이 정책은 HTTP 권한 부여 헤더를 정책에서 제공한 자격 증명에 해당하는 값으로 효과적으로 설정합니다.

### <a name="policy-statement"></a>정책 문

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>예제

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|인증-기본|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|Description|필수|기본값|
|----------|-----------------|--------------|-------------|
|username 이름|기본 자격 증명의 사용자 이름을 지정합니다.|예|N/A|
|password|기본 자격 증명의 비밀번호를 지정합니다.|예|해당 사항 없음|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

##  <a name="ClientCertificate"></a> 클라이언트 인증서 사용 인증
 `authentication-certificate` 정책을 사용하여 클라이언트 인증서를 사용하는 백 엔드 서비스를 인증합니다. 먼저 인증서를 [API Management에 설치](https://go.microsoft.com/fwlink/?LinkID=511599)하고 지문으로 식별해야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>예

이 예제에서는 클라이언트 인증서가 해당 지 문으로 식별 됩니다.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
이 예제에서 클라이언트 인증서는 리소스 이름으로 식별 됩니다.
```xml
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />
```

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|인증-인증서|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|thumbprint|클라이언트 인증서에 대한 지문입니다.|`thumbprint` 또는`certificate-id` 중 하나를 제공 해야 합니다.|해당 사항 없음|
|인증서 id|인증서 리소스 이름입니다.|`thumbprint` 또는`certificate-id` 중 하나를 제공 해야 합니다.|N/A|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

##  <a name="ManagedIdentity"></a>관리 id를 사용 하 여 인증
 `authentication-managed-identity` 정책을 사용 하 여 API Management 서비스의 관리 id를 사용 하 여 백 엔드 서비스를 인증 합니다. 이 정책은 관리 되는 id를 효과적으로 사용 하 여 지정 된 리소스에 액세스 하기 위해 Azure Active Directory에서 액세스 토큰을 가져옵니다.

### <a name="policy-statement"></a>정책 문

```xml
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>
```

### <a name="example"></a>예제

```xml
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" />
```

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|authentication-managed-identity |루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|Description|필수|기본값|
|----------|-----------------|--------------|-------------|
|resource|문자열. Azure Active Directory에 있는 대상 web API (보안 리소스)의 앱 ID URI입니다.|예|해당 사항 없음|
|output-token-variable-name|문자열. 토큰 값을 개체 형식 `string`으로 수신 하는 컨텍스트 변수의 이름입니다.|아니요|N/A|
|ignore-error|Boolean입니다. 로 설정 하면 `true`액세스 토큰을 가져올 수 없는 경우에도 정책 파이프라인이 계속 실행 됩니다.|아니요|false|

### <a name="usage"></a>사용법
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)
