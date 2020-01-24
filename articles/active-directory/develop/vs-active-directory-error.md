---
title: Azure AD 연결 서비스를 사용 하 여 오류 진단 (Visual Studio)
description: Active Directory의 연결된 서비스에서 호환되지 않는 인증 유형 검색
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: a6ec31f0d60c7f6e3737dc4042b05a6d8bf3dd5e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699974"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Azure Active Directory의 연결된 서비스를 사용하여 오류 진단

이전 인증 코드를 검색 하는 동안 Azure Active Directory 연결 된 서비스에서 호환 되지 않는 인증 유형을 검색 했습니다.

프로젝트에서 이전 인증 코드를 제대로 감지 하려면 프로젝트를 다시 빌드해야 합니다. 이 오류가 표시 되 고 프로젝트에 이전 인증 코드가 없으면 다시 빌드하여 다시 시도 합니다.

## <a name="project-types"></a>프로젝트 형식

이 연결된 서비스는 프로젝트에 올바른 인증 논리를 삽입할 수 있도록 사용자가 개발 중인 프로젝트 형식을 확인합니다. 프로젝트의 `ApiController`에서 파생 되는 컨트롤러가 있으면 프로젝트가 WebAPI 프로젝트로 간주 됩니다. 프로젝트의 `MVC.Controller`에서 파생되는 컨트롤러만 있으면 프로젝트가 MVC 프로젝트로 간주됩니다. 연결된 서비스는 다른 모든 프로젝트 형식을 지원하지 않습니다.

## <a name="compatible-authentication-code"></a>호환 가능한 인증 코드

또한 연결된 서비스는 이전에 이 서비스로 구성되었거나 이 서비스와 호환되는 인증 설정이 있는지도 확인합니다. 모든 설정이 있는 경우 다시 재진입용로 간주 되 고 연결 된 서비스가 열리면 설정을 표시 합니다.  일부 설정만 있으면 오류가 발생 한 것으로 간주 됩니다.

MVC 프로젝트에서 이 연결된 서비스는 이전에 서비스를 사용한 결과에 따라 다음과 같은 설정을 확인합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

또한, 연결 된 서비스는 웹 API 프로젝트에서 다음 설정 중 하나를 확인 하 여 이전에 서비스를 사용한 결과를 확인 합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>호환되지 않는 인증 코드

마지막으로, 이 연결된 서비스에서는 이전 버전의 Visual Studio를 사용하여 구성된 인증 코드의 버전을 감지하려고 합니다. 이 오류가 발생한 경우에는 프로젝트에 호환되지 않는 인증 코드가 있음을 의미합니다. 연결된 서비스에서는 이전 버전의 Visual Studio에서 다음과 같은 인증 유형을 감지합니다.

* Windows 인증
* 개별 사용자 계정
* 조직 계정

MVC 프로젝트에서 Windows 인증을 감지하기 위해 연결된 서비스는 사용자의 `web.config` 파일에서 `authentication` 요소를 찾습니다.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

웹 API 프로젝트에서 Windows 인증을 감지하기 위해 연결된 서비스는 프로젝트의 `.csproj` 파일에서 `IISExpressWindowsAuthentication` 요소를 찾습니다.

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

개별 사용자 계정 인증을 감지하기 위해 연결된 서비스는 사용자의 `packages.config` 파일에서 패키지 요소를 찾습니다.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

조직 계정 인증의 이전 양식을 감지하기 위해 연결된 서비스는 `web.config`에서 다음 요소를 찾습니다.

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

인증 유형을 변경하려면 호환되지 않는 인증 유형을 제거하고 연결된 서비스를 다시 추가해 보세요.

자세한 내용은 [Azure AD의 인증 시나리오](authentication-scenarios.md)를 참조하세요.
