---
title: 유니버설 Windows 플랫폼 고려 사항 (Microsoft Authentication Library for.NET) | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 유니버설 Windows 플랫폼을 사용할 때 특정 고려 사항에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544129"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>MSAL.NET 사용 하 여 유니버설 Windows 플랫폼 관련 고려 사항
Xamarin ios, MSAL.NET을 사용 하는 경우 계정에 수행 해야 하는 몇 가지 고려 사항이 있습니다.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 속성
WinRT 플랫폼에서 `PublicClientApplication` 는 다음 부울 속성이 ``UseCorporateNetwork``합니다. 이 속성을 사용 하면 통합 Windows 인증을 활용 하려면 Win8.1 및 UWP 응용 프로그램 (및 따라서 SSO 사용자를 로그인 운영 체제를 사용 하 여) 사용자가 페더레이션된 azure에서 계정으로 로그인 하는 경우 AD 테 넌 트입니다. 이 WAB (Web Authentication Broker)를 활용합니다. 

> [!IMPORTANT]
> 이 속성을 true로 설정 응용 프로그램 개발자가 응용 프로그램에서 통합 Windows 인증 (IWA)을 설정 있는지 가정 합니다. 이 방법을 사용 합니다.
> - 에 ``Package.appxmanifest`` UWP 응용 프로그램에 대 한에 **기능** 탭에서 다음 기능을 사용 하도록 설정:
>   - 엔터프라이즈 인증
>   - 개인 네트워크(클라이언트 및 서버)
>   - 공유 사용자 인증서

IWA 엔터프라이즈 인증 또는 공유 사용자 인증서 기능을 요청 하는 응용 프로그램을 Windows 스토어에 허용할 확인 높은 수준이 필요 하기 때문에 기본으로 사용 되지 않습니다 하 고 모든 개발자가 높을수록 수행 하려고 합니다. 인증 수준입니다. 

기본 구현 (WAB)의 UWP 플랫폼에서 조건부 액세스를 사용 하도록 설정 하는 엔터프라이즈 시나리오에서 제대로 작동 하지 않습니다. 현상은 사용자 Windows hello를 사용 하 여 한 로그인을 시도 하 고 인증서가 있지만 pin에 대 한 인증서가 없는 사용자를 선택 또는 선택 하지만 Pin 입력 되지 제안 됩니다. 문제를 해결 하는 대체 방법을 사용 하는 것 (사용자 이름/암호 + 전화 인증), 환경이 좋지 않습니다. 하지만 합니다. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 샘플에서 제공 됩니다.

샘플 | 플랫폼 | 설명 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 유니버설 Windows 플랫폼 클라이언트 응용 프로그램을 Azure AD v2.0 끝점을 사용 하 여 사용자 인증에 대 한 Microsoft Graph에 액세스, msal.net을 사용 하 여 합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | MSAL을 사용 하 여 MSA 및 AAD v2.0 끝점을 통해 Azure AD를 인증 하 고 결과 토큰을 사용 하 여 Microsoft Graph를 액세스 하는 방법을 보여주는 간단한 Xamarin Forms 앱입니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-xamarin-native.png)|