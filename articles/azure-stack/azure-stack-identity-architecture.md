---
title: Azure Stack에 대 한 id 아키텍처 | Microsoft Docs
description: Azure Stack과 함께 사용할 수 있습니다 id 아키텍처에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: bf69c71a8b361e4a147263bc60324573c710818f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412699"
---
# <a name="identity-architecture-for-azure-stack"></a>Azure Stack에 대 한 id 아키텍처
Azure Stack을 사용 하는 id 공급자를 선택 하기 전에 Azure Active Directory (Azure AD)의 옵션 및 Active Directory Federation Services (AD FS)의 중요 한 차이점을 이해 합니다. 

## <a name="capabilities-and-limitations"></a>기능 및 제한 사항 
선택한 id 공급자에는 다중 테 넌 트에 대 한 지원을 비롯 하 여 옵션을 제한할 수 있습니다. 

  

|기능 또는 시나리오        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|인터넷에 연결     |예       |옵션|
|다중 테 넌 트에 대 한 지원     |예       |아니요      |
|Marketplace에서 제품 항목 |예       |예. 사용 해야 합니다 [오프 라인 마켓플레이스 배포](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) 도구입니다.|
|Active Directory 인증 라이브러리 (ADAL)에 대 한 지원 |예 |예|
|Azure CLI, Visual Studio 및 PowerShell 같은 도구에 대 한 지원  |예 |예|
|Azure portal 통해 서비스 주체 만들기     |예 |아니요|
|인증서를 사용 하 여 서비스 주체 만들기      |예 |예|
|암호 (키)를 사용 하 여 서비스 주체 만들기    |예 |아니요|
|그래프 서비스를 사용 하 여 응용 프로그램           |예 |아니요|
|응용 프로그램 로그인에 대 한 id 공급자를 사용할 수 있습니다. |예 |예. 응용 프로그램을 온-프레미스와 페더레이션 해야 AD FS 인스턴스. |

## <a name="topologies"></a>토폴로지
다음 섹션에서는 절 사용할 수 있는 여러 가지 identity 토폴로지입니다.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: 단일 테 넌 트 토폴로지 
기본적으로 Azure Stack을 설치 하 고 Azure AD를 사용 하는 경우 Azure Stack에는 단일 테 넌 트 토폴로지를 사용 합니다. 

단일 테 넌 트 토폴로지는 다음 경우에 유용 합니다.
- 모든 사용자를 사용 하면 동일한 테 넌 트에 속합니다.
- 조직용 Azure Stack 인스턴스를 호스트 하는 서비스 공급자입니다. 

![Azure AD 사용 하 여 azure Stack 단일 테 넌 트 토폴로지](media/azure-stack-identity-architecture/single-tenant.png)

이 토폴로지는 다음과 같은 특징이 기능:
- Azure Stack 모든 응용 프로그램을 등록 하 고 서비스를 동일한 Azure AD 디렉터리 테 넌 트 키를 누릅니다. 
- Azure Stack 사용자와 토큰을 포함 하 여 해당 디렉터리에서 응용 프로그램을 인증 합니다. 
- (클라우드 운영자) 관리자 및 테 넌 트 사용자 id가 동일한 디렉터리 테 넌 트입니다. 
- 이 Azure Stack 환경에 액세스 하려면 다른 디렉터리에서 사용자를 사용 하도록 설정 하려면 [사용자를 게스트로 초대](azure-stack-identity-overview.md#guest-users) 테 넌 트 디렉터리에 있습니다. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: 다중 테 넌 트 토폴로지
클라우드 운영자는 하나 이상의 조직에서 테 넌 트에서 응용 프로그램에 액세스할 수 있도록 Azure Stack을 구성할 수 있습니다. 사용자가 사용자 포털을 통해 응용 프로그램에 액세스 합니다. 이 구성에서는 관리 포털 (클라우드 연산자가 사용 됨)은 제한 된 사용자에 게 단일 디렉터리에서. 

다중 테 넌 트 토폴로지는 다음 경우에 유용 합니다.
- 서비스 공급자가 여러 조직에서 사용자가 Azure Stack에 액세스할 수 있도록 합니다.

![Azure AD 사용 하 여 azure Stack 다중 테 넌 트 토폴로지](media/azure-stack-identity-architecture/multi-tenant.png)

이 토폴로지는 다음과 같은 특징이 기능:
- 조직 당 기준에 리소스에 액세스할 수 있어야 합니다. 
- 한 조직의 사용자가 해야 조직 외부 사용자 리소스에 대 한 액세스 권한을 부여할 수 없습니다. 
- 사용자에 대 한 id에서 별도 디렉터리 테 넌 트에서 관리자 (클라우드 운영자)에 대 한 id 수 있습니다. 이 분리는 identity 공급자 수준에서 계정을 격리를 제공합니다. 
 
### <a name="ad-fs"></a>AD FS  
AD FS 토폴로지는 필요 다음 조건 중 하나에 해당 합니다.
- Azure Stack 인터넷에 연결 하지 않습니다.
- Azure Stack 인터넷에 연결할 수는 있지만 id 공급자를 위한 AD FS를 사용 하도록 선택 합니다.
  
![AD FS를 사용 하 여 azure Stack 토폴로지](media/azure-stack-identity-architecture/adfs.png)

이 토폴로지는 다음과 같은 특징이 기능:
- 를 지원 하기 위해이 토폴로지를 사용 하 여 프로덕션 환경에서 페더레이션 트러스트를 통해 Active Directory에서 지원 되는 기존 AD FS 인스턴스를 사용 하 여 기본 제공 Azure Stack AD FS 인스턴스를 통합 해야 합니다. 
- 기존 Active Directory 인스턴스를 사용 하 여 Azure Stack에서 Graph 서비스를 통합할 수 있습니다. 또한 Azure AD Graph API를 사용 하 여 일치 하는 Api를 지 원하는 OData 기반 그래프 API 서비스를 사용할 수 있습니다. 

  Active Directory 인스턴스와 상호 작용, Graph API는 읽기 전용 권한이 있는 사용자 자격 증명 Active Directory 인스턴스에서 필요 합니다. 
  - 기본 제공 AD FS 인스턴스는 Windows Server 2016를 기반으로 합니다. 
  - AD FS 및 Active Directory 인스턴스는 Windows Server 2012 이상에 기반 해야 합니다. 
  
  Active Directory 인스턴스 및 기본 제공 AD FS 인스턴스 간의 상호 작용에는 OpenID Connect로 제한 되지 않습니다. 및 상호 지원 되는 프로토콜을 사용할 수 있습니다. 
  - 사용자 계정 생성 및 온-프레미스 Active Directory 인스턴스에서 관리 됩니다.
  - 서비스 주체 및 응용 프로그램에 대 한 등록은 기본 제공 Active Directory 인스턴스에서 관리 됩니다.



## <a name="next-steps"></a>다음 단계
- [Id 개요](azure-stack-identity-overview.md)   
- [데이터 센터 통합-identity](azure-stack-integrate-identity.md)
