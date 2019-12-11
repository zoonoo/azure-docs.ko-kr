---
title: Azure AD 연결 된 서비스를 사용 하 여 오류 진단
description: Active directory 연결 된 서비스에서 호환 되지 않는 인증 유형을 검색 했습니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a7528bafd9183ae917370a6de35d1d94c3783e5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966455"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Azure Active Directory 연결 된 서비스를 사용 하 여 오류 진단

이전 인증 코드를 검색 하는 동안 Azure Active Directory 연결 된 서비스에서 호환 되지 않는 인증 유형을 검색 했습니다.

프로젝트에서 이전 인증 코드를 제대로 감지 하려면 프로젝트를 다시 빌드해야 합니다. 이 오류가 표시 되 고 프로젝트에 이전 인증 코드가 없으면 다시 빌드하여 다시 시도 합니다.

## <a name="project-types"></a>프로젝트 형식

연결 된 서비스는 프로젝트에 올바른 인증 논리를 삽입할 수 있도록 개발 중인 프로젝트 형식을 확인 합니다. 프로젝트의 `ApiController`에서 파생 되는 컨트롤러가 있으면 프로젝트가 WebAPI 프로젝트로 간주 됩니다. 프로젝트의 `MVC.Controller`에서 파생 되는 컨트롤러만 있으면 프로젝트가 MVC 프로젝트로 간주 됩니다. 연결 된 서비스는 다른 프로젝트 형식을 지원 하지 않습니다.

## <a name="compatible-authentication-code"></a>호환 되는 인증 코드

또한 연결 된 서비스는 이전에 구성 되었거나 서비스와 호환 되는 인증 설정을 확인 합니다. 모든 설정이 있는 경우 다시 재진입용로 간주 되 고 연결 된 서비스가 열리면 설정을 표시 합니다.  일부 설정만 있으면 오류가 발생 한 것으로 간주 됩니다.

MVC 프로젝트에서 연결 된 서비스는 이전에 서비스를 사용한 결과에 따라 다음 설정 중 하나를 확인 합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

또한, 연결 된 서비스는 웹 API 프로젝트에서 다음 설정 중 하나를 확인 하 여 이전에 서비스를 사용한 결과를 확인 합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>호환 되지 않는 인증 코드

마지막으로, 연결 된 서비스는 이전 버전의 Visual Studio로 구성 된 인증 코드의 버전을 검색 하려고 시도 합니다. 이 오류가 표시 되 면 프로젝트에 호환 되지 않는 인증 형식이 포함 되어 있음을 의미 합니다. 연결 된 서비스는 이전 버전의 Visual Studio에서 다음과 같은 인증 유형을 검색 합니다.

* Windows-hitelesítés
* 개별 사용자 계정
* 조직 계정

MVC 프로젝트에서 Windows 인증을 감지 하기 위해 연결 된는 `web.config` 파일에서 `authentication` 요소를 찾습니다.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

웹 API 프로젝트에서 Windows 인증을 감지 하기 위해 연결 된 서비스는 프로젝트의 `.csproj` 파일에서 `IISExpressWindowsAuthentication` 요소를 찾습니다.

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

개별 사용자 계정 인증을 검색 하기 위해 연결 된 서비스는 `packages.config` 파일의 package 요소를 찾습니다.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

이전 형태의 조직 계정 인증을 검색 하기 위해 연결 된 서비스는`web.config`에서 다음 요소를 찾습니다.

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

인증 유형을 변경 하려면 호환 되지 않는 인증 유형을 제거 하 고 연결 된 서비스를 다시 추가 해 보세요.

자세한 내용은 [AZURE AD에 대 한 인증 시나리오](authentication-scenarios.md)를 참조 하세요.
