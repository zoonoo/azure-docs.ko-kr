---
title: 앱을 Azure Active Directory로 마이그레이션하기 위한 리소스 | Microsoft Docs
description: 애플리케이션 액세스 및 인증을 Azure AD(Azure Active Directory)로 마이그레이션하는 데 도움이 되는 리소스입니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: a49dff0e4ac41670800073c02c5a507eb433c326
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280436"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>애플리케이션을 Azure Active Directory로 마이그레이션하기 위한 리소스

애플리케이션 액세스 및 인증을 Azure AD(Azure Active Directory)로 마이그레이션하는 데 도움이 되는 리소스입니다.

| 리소스  | 설명  |
|:-----------|:-------------|
|[앱을 Azure AD로 마이그레이션](https://aka.ms/migrateapps/whitepaper) | 이 백서에서는 마이그레이션의 이점을 제시하고 4가지 명확한 단계(탐색, 분류, 마이그레이션 및 지속적인 관리)로 마이그레이션을 계획하는 방법을 설명합니다. 프로세스에 대해 생각하고 프로젝트를 소비하기 쉬운 부분으로 나누는 방법이 안내됩니다. 문서 전체에는 과정에 도움이 되는 중요한 리소스에 대한 링크가 있습니다. |
|[솔루션 가이드: AD FS(Active Directory Federation Services)에서 Azure AD로 앱 마이그레이션](https://aka.ms/migrateapps/adfssolutionguide) | 이 솔루션 가이드에서는 애플리케이션 마이그레이션 프로젝트를 계획하고 실행하는 4단계(마이그레이션 백서의 상위 수준에서 설명한 것과 같음)를 안내합니다. 이 가이드에서는 이러한 단계를 AD FS(Active Directory Federation Services)에서 Azure AD로 애플리케이션을 이동하는 구체적인 목표에 적용하는 방법을 알아봅니다.|
|[개발자 자습서: 개발자를 위한 Azure AD application migration 플레이 북 AD FS](https://aka.ms/adfsplaybook) | 이 ASP.NET 코드 샘플과 함께 제공 되는 자습서는 Active Directory Federation Services (AD FS Azure Active Directory)와 통합 된 응용 프로그램을 안전 하 고 안전 하 게 마이그레이션하는 방법을 배우는 데 도움이 됩니다 (Azure AD). 이 자습서에서는 AD FS와 Azure AD 모두에서 앱을 구성 하는 방법을 배울 뿐만 아니라 코드 베이스에 필요한 변경 내용을 인식 하 고 확신 하는 개발자에 게 초점을 맞추고 있습니다.|
| [도구: Active Directory Federation Services 마이그레이션 준비 스크립트](https://aka.ms/migrateapps/adfstools) | 온-프레미스 AD FS(Active Directory Federation Services) 서버에서 실행하여 앱을 Azure AD로 마이그레이션할 준비가 되었는지 확인할 수 있는 스크립트입니다.|
| [배포 계획: AD FS에서 암호 해시 동기화로 마이그레이션](https://aka.ms/ADFSTOPHSDPDownload) | 암호 해시 동기화를 수행하면 사용자 암호의 해시는 온-프레미스 Active Directory에서 Azure AD로까지 동기화됩니다. 이를 통해 Azure AD는 온-프레미스 Active Directory와 상호 작용하지 않고 사용자를 인증할 수 있습니다.| 
| [배포 계획: AD FS에서 통과 인증으로 마이그레이션](https://aka.ms/ADFSTOPTADPDownload)|Azure AD 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 애플리케이션 둘 다에서 동일한 암호로 로그인할 수 있습니다. 이 기능은 사용자에게 더 나은 환경을 제공합니다. 기억할 암호가 하나 줄어들기 때문입니다. IT 기술 지원팀 비용이 감소됩니다. 사용자가 암호를 하나만 기억해도 되면 로그인 방법을 잊어버릴 가능성이 줄어들기 때문입니다. 사람들이 Azure AD를 사용하여 로그인할 때 이 기능은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다.|
| [배포 계획: Azure AD로 SaaS 앱에 Single Sign-On을 사용하도록 설정](https://aka.ms/SSODPDownload) | SSO(Single Sign-On)는 단일 사용자 계정을 사용하여 한 번만 로그인하면 비즈니스 수행에 필요한 모든 앱과 리소스에 액세스할 수 있게 해줍니다. 예를 들어 사용자가 로그인한 후에는 두 번째 인증(예: 암호 입력)을 거치지 않고 Microsoft Office에서 SalesForce로, Box로 이동할 수 있습니다. 
| [배포 계획: 애플리케이션 프록시를 통해 앱을 Azure AD로 확장](https://aka.ms/AppProxyDPDownload)| 직원의 랩톱 및 기타 디바이스에서 온-프레미스 애플리케이션에 액세스할 수 있도록 하려면 일반적으로 VPN(가상 사설망) 또는 완충 영역(DMZ)이 필요했습니다. 이러한 솔루션은 복잡하고 안전하게 만들기도 어려울 뿐만 아니라 설정과 관리에도 비용이 많이 듭니다. Azure AD 애플리케이션 프록시를 사용하면 온-프레미스 애플리케이션에 쉽게 액세스할 수 있습니다. |
| [배포 계획](../fundamentals/active-directory-deployment-plans.md) | Multi-factor authentication, 조건부 액세스, 사용자 프로 비전, 원활한 SSO, 셀프 서비스 암호 다시 설정 등의 기능을 배포 하기 위한 추가 배포 계획을 찾아보세요. |


