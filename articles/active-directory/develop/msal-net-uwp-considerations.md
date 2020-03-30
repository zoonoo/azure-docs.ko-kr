---
title: UWP 고려 사항 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 유니버설 Windows 플랫폼(UWP)을 사용하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132518"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>MSAL.NET 함께 유니버설 윈도우 플랫폼 사용에 대한 고려 사항
MSAL.NET 유니버설 Windows 플랫폼(UWP)을 사용하는 응용 프로그램의 개발자는 이 문서에서 제시하는 개념을 고려해야 합니다.

## <a name="the-usecorporatenetwork-property"></a>유스기업네트워크 속성
Windows 런타임(WinRT) `PublicClientApplication` 플랫폼에는 부울 속성이 `UseCorporateNetwork`있습니다. 이 속성을 사용하면 사용자가 페더레이션된 Azure Active Directory(Azure AD) 테넌트가 있는 계정에 로그인한 경우 Windows 8.1 응용 프로그램 및 UWP 응용 프로그램을 IWA(통합 Windows 인증)의 이점을 활용할 수 있습니다. 운영 체제에 로그인한 사용자는 단일 사인온(SSO)을 사용할 수도 있습니다. 속성을 설정하면 `UseCorporateNetwork` MSAL.NET WAB(웹 인증 브로커)를 사용합니다.

> [!IMPORTANT]
> `UseCorporateNetwork` 속성을 true로 설정하면 응용 프로그램 개발자가 응용 프로그램에서 IWA를 사용하도록 설정했다고 가정합니다. IWA를 사용하려면 다음을 수행하십시오.
> - 기능 **탭의** `Package.appxmanifest`UWP 응용 프로그램의 에서 다음 기능을 사용하도록 설정합니다.
>   - **엔터프라이즈 인증**
>   - **개인 네트워크(클라이언트 및 서버)**
>   - **공유 사용자 인증서**

Microsoft Store는 엔터프라이즈 인증 또는 공유 사용자 인증서의 기능을 요청하는 응용 프로그램을 수락하기 전에 높은 수준의 확인이 필요하기 때문에 기본적으로 IWA를 사용할 수 없습니다. 모든 개발자가 이 수준의 확인을 원하지는 않습니다.

UWP 플랫폼에서기본 WAB 구현은 조건부 액세스가 활성화된 엔터프라이즈 시나리오에서 제대로 작동하지 않습니다. 사용자가 Windows Hello를 사용하여 로그인하려고 할 때 이 문제의 증상이 표시됩니다. 사용자에게 인증서를 선택하라는 메시지가 표시됩니다.

- PIN에 대한 인증서를 찾을 수 없습니다.
- 사용자가 인증서를 선택한 후에는 PIN에 대한 메시지가 표시되지 않습니다.

사용자 이름 암호 및 전화 인증과 같은 다른 방법을 사용하여 이 문제를 방지할 수 있지만 환경은 좋지 않습니다.

## <a name="troubleshooting"></a>문제 해결

일부 고객은 인터넷에 연결되어 있고 공용 네트워크와 연결이 작동한다는 것을 알고 있는 특정 엔터프라이즈 환경에서 다음과 같은 로그인 오류를 보고했습니다.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

WAB(기본 Windows 구성 요소)가 개인 네트워크를 허용하는지 확인하여 이 문제를 방지할 수 있습니다. 레지스트리 키를 설정하면 됩니다.

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

자세한 내용은 [웹 인증 브로커 - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)를 참조하십시오.

## <a name="next-steps"></a>다음 단계
다음 샘플에서는 자세한 정보를 제공합니다.

예제 | 플랫폼 | 설명 
|------ | -------- | -----------|
|[액티브 디렉토리-도트넷-네이티브-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | MSAL.NET 사용하는 UWP 클라이언트 응용 프로그램입니다. Azure AD 2.0 끝점을 사용하여 인증하는 사용자를 위해 Microsoft 그래프에 액세스합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[액티브 디렉토리-자마린-네이티브-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | MSAL을 사용하여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 Microsoft 그래프에 액세스하는 방법을 보여 주며 결과 토큰을 보여 주기도 합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
