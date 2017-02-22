---
title: "Azure RemoteApp에 필요한 컬렉션의 종류는 무엇입니까? | Microsoft Docs"
description: "Azure RemoteApp과 함께 사용할 수 있는 컬렉션의 형식에 알아봅니다."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c13ec78d-07e9-4646-8194-cf3efafc1760
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c526faeef2418cfbbca7948004691e127327020


---
# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Azure RemoteApp에 필요한 컬렉션의 종류는 무엇입니까?
> [!IMPORTANT]
> Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

Azure RemoteApp를 사용하면 어떠한 장치의 사용자와도 앱 및 리소스를 공유할 수 있습니다. 앱 및 리소스를 포함할 컬렉션을 만든 다음 해당 컬렉션을 사용자와 공유하면 됩니다. 서로 다른 네트워크 및 인증 옵션을 사용하는 두 가지 컬렉션 옵션이 있습니다. 어느 옵션이 사용자에게 적합합니까?

Azure RemoteApp 컬렉션을 최대한 활용하는 데 필요한 여러 가지 고려 사항과 선택을 살펴보겠습니다. 

## <a name="quick-differences-between-the-collection-types"></a>컬렉션 유형 간의 차이점 요약
|  | 클라우드 | 하이브리드 |
| --- | --- | --- |
| 기존 VNET 사용 |예 |예 |
| AD에 연결된 계정(DirSync)이 필요합니다. |아니요 |예 |
| 도메인 가입이 필요합니다. |아니요 |예 |
| 도메인 컨트롤러를 VNET에 액세스할 수 있어야 합니다. |아니요 |예 |

## <a name="cloud-collections"></a>클라우드 컬렉션
* 신속하게 만들기 - 컬렉션이 신속하게 프로비전되므로 앱이 사용자에게 더 빨리 다가갑니다.
* 자신의 앱을 가져오거나 우리의 앱을 공유합니다. 사용자 지정 이미지(Azure VM에서 빌드된) 또는 구독에 포함된 이미지 중 하나를 사용할 수 있습니다.
* 사용자의 컬렉션과 온-프레미스 도메인 사이의 연결을 구성하지 않아도 됩니다.
* 하지만 선택적으로 자기만의 Azure VNET를 사용하여 데이터 공유를 위해 온-프레미스 환경에 대한 액세스를 제공하거나 Windows 이외의 인증을 SQL Server 같은 리소스에 사용할 수 있습니다(데이터베이스 인증을 사용하여).

그렇다면 어떻게 만드나요?

* 클라우드 전용입니까? 포털에서 **빠른 생성** 옵션으로 만듭니다.
* 클라우드 + VNET입니까? **VNET을 사용하여 만들기** 옵션을 사용하여 만들지만 도메인에 가입을 선택하지 마십시오.

## <a name="hybrid-collections"></a>하이브리드 컬렉션
* 온-프레미스 네트워크 + Azure VNET에 대한 전체 액세스를 제공합니다.
* 앱 및 데이터에 대한 도메인 조인 액세스를 포함합니다. 원격 응용 프로그램은 사용자의 온-프레미스 Active Directory에 대해 인증한 다음 사용자 도메인의 리소스에 액세스할 수 있습니다.
* 기존 시스템 센터 솔루션 및 Windows 그룹 정책을 사용하여 고급 모니터링 및 관리를 사용하도록 설정합니다(Windows Server 2012 R2에서 만든 사용자 지정 이미지를 통해).
* Azure VNET을 로컬 VNET에 연결하는 [Express 경로](https://azure.microsoft.com/services/expressroute/) 를 지원합니다.

**VNET을 사용하여 만들기** 옵션을 사용하여 만들고 도메인에 가입을 선택합니다.

## <a name="authentication-options"></a>인증 옵션
Azure RemoteApp은 Microsoft 계정과 Azure Active Directory 계정을 모두 지원하지만 일부 컬렉션은 일부 방법을 지원하지 않을 수 있습니다. 

| 계정 유형 |  | 클라우드 | 클라우드 + VNET | 하이브리드 |
| --- | --- | --- | --- | --- |
| Microsoft 계정 | |예 |예 |아니요 |
| Azure AD(Azure Active Directory) | | | | |
| Azure AD만 |예 |예 |아니요 | |
| 암호 동기화를 사용하는 AD Connect |예 |예 |예 | |
| 암호 동기화를 사용하지 않는 AD Connect |예 |예 |아니요 | |
| AD FS를 사용하는 AD Connect |예 |예 |예 | |
| 타사 Azure 지원 ID 공급자(예: Ping) |예 |예 |예 | |
| Multi-Factor Authentication | |예 |예 |예 |

### <a name="cloud-and-cloud--vnet"></a>클라우드 및 클라우드 + VNET
클라우드 컬렉션을 사용하면 Microsoft 계정, Azure AD 계정 또는 둘의 혼합을 사용할 수 있습니다. 사용자에게 가장 적합한 계정을 사 합니다.

Microsoft 계정을 사용하기 위한 특정 요구 사항이 없습니다. 

Azure AD 계정을 사용하려는 경우 Azure AD 테넌트가 구독에 연결된 테넌트와 일치하는지 확인해야 합니다. Azure RemoteApp 구독을 만들 때 사용한 Azure AD 테넌트는 사용자의 구독에 자동으로 연결되었습니다. 권한을 부여하는 모든 Azure AD 사용자는 같은 테넌트여야 합니다. 필요한 경우 구독과 관련된 [Azure AD 테넌트를 변경](remoteapp-changetenant.md) 할 수 있습니다.

### <a name="hybrid-or-cloud--azure-ad--ad"></a>하이브리드(또는 클라우드 + Azure AD + AD)
Azure AD + 온-프레미스 Active Directory 사용은 하이브리드 컬렉션에 대한 전제 조건입니다. 두 디렉터리를 통합하려면 AD Connect를 사용해야 합니다. 하지만 AD 연결을 구성하는 방법의 경우 몇 가지 선택이 있습니다. 

암호 동기화 사용 또는 AD 페더레이션 사용 등 두 가지 AD Connect 시나리오가 있습니다. [AD Connect 정보](../active-directory/active-directory-aadconnect.md) 를 체크 아웃하여 둘 중 가장 적합한 시나리오를 알아냅니다.

또한 Azure AD + AD를 클라우드 컬렉션과 함께 사용할 수 있습니다. 같은 설정 단계를 수행해야 합니다.

Azure AD 및 Active Directory를 구성하는 데 필요한 단계는 [Azure RemoteApp에 대한 Azure AD + Active Directory 요구 사항](remoteapp-ad.md) 을 확인하세요.

## <a name="go-create-your-collection"></a>사용자의 컬렉션을 만드십시오!
알겠습니다. 방법을 알았으므로 첫 번째 Azure RemoteApp 컬렉션을 만드는 한 가지만 남았습니다.

[클라우드 컬렉션 만들기](remoteapp-create-cloud-deployment.md) 또는 [하이브리드 컬렉션을 만들기](remoteapp-create-hybrid-deployment.md) - 그냥 만들어만 보세요!




<!--HONumber=Nov16_HO3-->


