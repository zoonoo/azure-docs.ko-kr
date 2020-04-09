---
title: 프로덕션을 위한 모바일 앱 호출 웹 API 준비 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법을 알아봅니다. (프로덕션용 앱 준비)를 준비합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882542"
---
# <a name="prepare-mobile-apps-for-production"></a>프로덕션을 위한 모바일 앱 준비

이 문서에서는 모바일 앱을 프로덕션으로 이동하기 전에 모바일 앱의 품질과 안정성을 개선하는 방법에 대한 자세한 내용을 제공합니다.

## <a name="handle-errors"></a>오류 처리

프로덕션을 위해 모바일 앱을 준비할 때 몇 가지 오류 조건이 발생할 수 있습니다. 처리해야 할 주요 사례는 자동 오류 및 상호 작용에 대한 대체입니다. 고려해야 할 다른 조건으로는 네트워크 중단 상황, 서비스 중단, 관리자 동의 요구 사항 및 기타 시나리오별 사례가 포함됩니다.

각 MSAL(Microsoft 인증 라이브러리) 유형에 대해 오류 조건을 처리하는 방법을 설명하는 샘플 코드 및 위키 콘텐츠를 찾을 수 있습니다.

- [MSAL 안드로이드 위키](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS 위키](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET 위키](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>문제 완화 및 조사

앱에서 문제를 더 잘 진단하려면 데이터를 수집합니다. 수집할 수 있는 데이터 종류에 대한 자세한 내용은 [MSAL 응용 프로그램에서 로깅을](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)참조하십시오.

다음은 데이터 수집에 대한 몇 가지 제안 사항입니다.

- 사용자가 문제가 있을 때 도움을 요청할 수 있습니다. 로그를 캡처하고 일시적으로 저장하는 것이 가장 좋습니다. 사용자가 로그를 업로드할 수 있는 위치를 제공합니다. MSAL은 인증에 대한 자세한 정보를 캡처하기 위해 로깅 확장을 제공합니다.

- 원격 분석을 사용할 수 있는 경우 MSAL을 통해 사용자가 앱에 로그인하는 방법에 대한 데이터를 수집하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

추가 샘플을 사용해 보려면 [데스크톱 및 모바일 공용 클라이언트 앱을](sample-v2-code.md#desktop-and-mobile-public-client-apps)참조하십시오.
