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
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250954"
---
# <a name="api-management-authentication-policies"></a>API Management 인증 정책
이 토픽에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.  

##  <a name="AuthenticationPolicies"></a> 인증 정책  
  
-   [기본 사용 인증](api-management-authentication-policies.md#Basic) - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.  
  
-   [클라이언트 인증서 사용 인증](api-management-authentication-policies.md#ClientCertificate) - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.  
  
##  <a name="Basic"></a> 기본 사용 인증  
 `authentication-basic` 정책을 사용하여 기본 인증을 사용하는 백 엔드 서비스를 인증합니다. 이 정책은 HTTP 권한 부여 헤더를 정책에서 제공한 자격 증명에 해당하는 값으로 효과적으로 설정합니다.  
  
### <a name="policy-statement"></a>정책 문  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>예  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>요소  
  
|이름|설명|필수|  
|----------|-----------------|--------------|  
|인증-기본|루트 요소입니다.|yes|  
  
### <a name="attributes"></a>특성  
  
|이름|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|사용자 이름|기본 자격 증명의 사용자 이름을 지정합니다.|yes|해당 없음|  
|암호|기본 자격 증명의 비밀번호를 지정합니다.|yes|해당 없음|  
  
### <a name="usage"></a>사용 현황  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** 인바운드  
  
-   **정책 범위:** API  
  
##  <a name="ClientCertificate"></a> 클라이언트 인증서 사용 인증  
 `authentication-certificate` 정책을 사용하여 클라이언트 인증서를 사용하는 백 엔드 서비스를 인증합니다. 먼저 인증서를 [API Management에 설치](https://go.microsoft.com/fwlink/?LinkID=511599)하고 지문으로 식별해야 합니다.  
  
### <a name="policy-statement"></a>정책 명령문  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>예  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>요소  
  
|이름|설명|필수|  
|----------|-----------------|--------------|  
|인증-인증서|루트 요소입니다.|yes|  
  
### <a name="attributes"></a>특성  
  
|이름|설명|필수|기본값|  
|----------|-----------------|--------------|-------------|  
|thumbprint|클라이언트 인증서에 대한 지문입니다.|yes|해당 없음|  
  
### <a name="usage"></a>사용 현황  
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.  
  
-   **정책 섹션:** 인바운드  
  
-   **정책 범위:** API  
  

## <a name="next-steps"></a>다음 단계
정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)   
