---
title: "Azure Active Directory 연결 마법사를 사용하여 오류를 진단하는 방법"
description: "Active Directory 연결 마법사에서 호환되지 않는 인증 유형 검색"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 4f29f62b2996cae98b02c1ed5fcb59eca09301ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Azure Active Directory 연결 마법사를 사용하여 오류 진단
이전 인증 코드를 검색하는 동안 마법사에서 호환되지 않는 인증 유형을 검색했습니다.   

## <a name="what-is-being-checked"></a>무엇을 확인합니까?
**참고:** 프로젝트에서 이전 인증 코드를 제대로 감지 하려면 프로젝트를 빌드해야 합니다.  이 오류가 발생했고 프로젝트에 이전 인증 코드가 없는 경우 다시 작성한 다음 다시 시도합니다.

### <a name="project-types"></a>프로젝트 형식
이 마법사는 프로젝트에 올바른 인증 논리를 삽입할 수 있도록 사용자가 개발 중인 프로젝트 형식을 확인합니다.  프로젝트의 `ApiController`에서 파생되는 컨트롤러가 있으면 프로젝트가 WebAPI 프로젝트로 간주됩니다.  프로젝트의 `MVC.Controller`에서 파생되는 컨트롤러만 있으면 프로젝트가 MVC 프로젝트로 간주됩니다.  다른 항목은 이 마법사에서 지원되지 않습니다.

### <a name="compatible-authentication-code"></a>호환 가능한 인증 코드
또한 이 마법사에서는 이전에 이 마법사로 구성되었거나 이 마법사와 호환되는 인증 설정이 있는지도 확인합니다.  모든 설정이 있는 경우 재진입 사례로 간주되고 마법사가 열릴 때 해당 설정이 표시됩니다.  설정이 일부만 있으면 오류 사례로 간주됩니다.

MVC 프로젝트에서 이 마법사는 이전에 마법사를 사용한 결과에 따라 다음과 같은 설정을 확인합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

또한 이 마법사는 이전에 마법사를 사용한 결과에 따라 다음과 같은 Web API 프로젝트 설정을 확인합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>호환되지 않는 인증 코드
마지막으로, 이 마법사에서는 이전 버전의 Visual Studio를 사용하여 구성된 인증 코드의 버전을 감지하려고 합니다. 이 오류가 발생한 경우에는 프로젝트에 호환되지 않는 인증 코드가 있음을 의미합니다. 마법사에서는 이전 버전의 Visual Studio에서 다음과 같은 인증 유형을 감지합니다.

* Windows 인증 
* 개별 사용자 계정 
* 조직 계정 

MVC 프로젝트에서 Windows 인증을 감지하기 위해 마법사는 사용자의 **web.config** 파일에서 `authentication` 요소를 찾습니다.

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Web API 프로젝트에서 Windows 인증을 감지하기 위해 마법사는 사용자 프로젝트의 **.csproj** 파일에서 `IISExpressWindowsAuthentication` 요소를 찾습니다.

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

개별 사용자 계정 인증을 감지하기 위해 마법사는 사용자의 **Packages.config** 파일에서 패키지 요소를 찾습니다.

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

조직 계정 인증의 이전 양식을 감지하기 위해 마법사는 **web.config**에서 다음 요소를 찾습니다.

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

인증 유형을 변경하려면 호환되지 않는 인증 유형을 제거하고 마법사를 다시 실행하세요.

자세한 내용은 [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하세요.

#<a name="next-steps"></a>다음 단계
- [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)