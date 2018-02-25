---
title: "Azure 스택에 대 한 id 아키텍처 | Microsoft Docs"
description: "Azure 스택 함께 사용할 수 있습니다 Id 아키텍처에 알아봅니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 0f3e28f7726afab02211902b5ba2e478ae8065df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Azure 스택에 대 한 id 아키텍처
Azure 스택을 사용 하는 id 공급자를 선택 하기 전에 Azure Active Directory (Azure AD)의 옵션 및 Active Directory Federated Services (AD FS) 중요 한 차이점을 이해 합니다. 

## <a name="capabilities-and-limitations"></a>기능 및 제한 사항 
선택한 id 공급자에는 다중 테 넌 트에 대 한 지원을 비롯 하 여 옵션을 제한할 수 있습니다. 

  

|기능 또는 시나리오        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|인터넷에 연결     |예       |옵션|
|다중 테 넌 트에 대 한 지원     |예       |아니요      |
|마켓플레이스에서 배포       |예       |예-를 사용 해야는 [오프 라인 마켓플레이스 배포](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) 도구입니다.|
|Active Directory 인증 라이브러리 (ADAL)에 대 한 지원 |예 |예|
|Azure 명령줄 인터페이스 (CLI), Visual Studio (VS) 및 PowerShell과 같은 도구에 대 한 지원  |예 |예|
|포털을 통해 서비스 사용자 만들기     |예 |아니요|
|인증서가 있는 서비스 사용자 만들기      |예 |예|
|서비스 사용자 암호 (키) 만들기    |예 |아니요|
|응용 프로그램 그래프 서비스를 사용할 수 있습니다.           |예 |아니요|
|응용 프로그램 로그인에 대 한 id 공급자를 사용할 수 있습니다. |예 |예-온-프레미스와 페더레이션 할 응용 프로그램을 필요한 AD FS 합니다. |

## <a name="topologies"></a>토폴로지
다음 섹션에서는 identity 토폴로지를 사용할 수 있습니다에 대 한 정보를 제공 합니다.

### <a name="azure-ad--single-tenant"></a>Azure AD – 단일 테 넌 트 
기본적으로 Azure 스택을 설치 하 고 Azure AD를 사용 하는 경우 Azure 스택 단일 테 넌 트 토폴로지를 사용 합니다. 

단일 테 넌 트 토폴로지는 다음 경우에 유용 합니다.
- 모든 사용자는 동일한 테 넌 트의 일부입니다.
- 서비스 공급자는 조직을 위해 Azure 스택 인스턴스를 호스팅합니다.  

![단일 테 넌 트 토폴로지를 사용 하 여 Azure AD와 azure 스택 토폴로지](media/azure-stack-identity-architecture/single-tenant.png)

이 토폴로지:
- Azure 스택 모든 응용 프로그램을 등록 하 고 서비스를 동일한 Azure AD 디렉터리 테 넌 트 키를 누릅니다. 
- Azure 스택 사용자와 토큰을 포함 하 여 해당 디렉터리에서 응용 프로그램을 인증 합니다. 
- (클라우드 운영자) 관리자 및 테 넌 트 사용자에 대 한 id는 동일한 디렉터리 테 넌 트에 있습니다. 
- 이 Azure 스택 환경에 액세스 하려면 다른 디렉터리에서 사용자를 사용 하도록 설정 하려면 [사용자를 게스트로 초대](azure-stack-identity-overview.md#guest-users) 테 넌 트 디렉터리에 있습니다.  

### <a name="azure-ad--multi-tenant"></a>Azure AD-다중 테 넌 트
클라우드 운영자는 하나 이상의 조직에서 테 넌 트에서 응용 프로그램에 액세스할 수 있도록 Azure 스택을 구성할 수 있습니다. 사용자가 사용자 포털을 통해 응용 프로그램에 액세스 합니다. 이 구성에서는 관리 포털 (클라우드 연산자에 사용) 제한 된 사용자에 게 단일 디렉터리에서 합니다. 

다중 테 넌 트 토폴로지는 다음 경우에 유용 합니다.
- 서비스 공급자는 여러 조직에서 사용자가 Azure 스택에 액세스할 수 있도록 하려고 합니다.

![다중 테 넌 트 토폴로지를 사용 하 여 Azure AD와 azure 스택 토폴로지](media/azure-stack-identity-architecture/multi-tenant.png)

이 토폴로지:
- 조직 당 기준에 리소스에 액세스할 수 있어야 합니다. 
- 한 조직에서 사용자는 사용자에 게 조직 외부에 있는 리소스에 대 한 액세스를 부여할 수 없습니다.  
- 사용자에 대 한 id 보다 별도 디렉터리 테 넌 트 관리자 (클라우드 운영자)에 대 한 id 있을 수 있습니다. 이러한 분리는 identity 공급자 수준에서 계정 격리를 제공합니다. 
 
### <a name="ad-fs"></a>AD FS  
AD FS 토폴로지는 필요한 다음 조건 중 하나에 해당 합니다.
- Azure 스택 인터넷에 연결 되지 않습니다.
- Azure 스택 인터넷에 연결할 수는 있지만 id 공급자에 대 한 AD FS를 사용 하도록 선택 합니다.
  
![AD FS를 사용 하 여 azure 스택 토폴로지](media/azure-stack-identity-architecture/adfs.png)

이 토폴로지:
- 사용을 지원 하기 위해 프로덕션 환경에서 페더레이션 트러스트를 통해 하 여 AD (Active Directory)를 지 원하는 기존 AD FS 인스턴스가 있는 기본 제공 Azure 스택 AD FS 인스턴스를 통합 해야 합니다. 
- Azure 스택에서 그래프 서비스 기존 AD와 통합할 수 있습니다.  OData를 사용할 수도 있습니다 Azure AD Graph API와 일치 하는 Api를 지 원하는 Graph API 서비스를 기반으로 합니다.  

  Graph API의 읽기 전용 권한이 있는 사용자 자격 증명 AD에서 필요 AD와 상호 작용을 하 고 AD 합니다. 
  - 기본 제공 AD FS 서버 2016을 기반으로 합니다. 
  - AD FS 및 AD Server 2012이 하에 기반 해야 합니다. 
  
  광고와 기본 제공 AD FS 간의 상호 작용 OpenID Connect로 제한 되지 않습니다 및 상호 지원 되는 프로토콜을 사용할 수 있습니다.  
  - 사용자 계정을 만들고 관리 온-프레미스에서 AD 합니다.
  - 서비스 사용자 및 응용 프로그램에 대 한 등록이 기본 제공 AD에서 관리 됩니다.



## <a name="next-steps"></a>다음 단계
- [Identity 개요](azure-stack-identity-overview.md)   
- [데이터 센터 통합-Identity](azure-stack-integrate-identity.md)