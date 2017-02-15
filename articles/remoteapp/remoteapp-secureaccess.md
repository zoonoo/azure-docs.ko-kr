---
title: "Azure RemoteApp 등에 대한 액세스 보호 | Microsoft Docs"
description: "Azure Active Directory의 조건부 액세스를 사용하여 Azure RemoteApp에 대한 액세스를 보호하는 방법을 알아봅니다"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: a19b0b09-ab26-4beb-80bb-33a46da39887
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 3cd588751346517f9359f760561839339b0f9edb


---
# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Azure RemoteApp 등에 대한 액세스 보호
> [!IMPORTANT]
> Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

이 문서에서는 관리자가 Azure RemoteApp을 통해 최종 사용자를 시작하고 SQL 데이터베이스 또는 다른 응용 프로그램 백 엔드와 같은 보안 리소스로 끝나는 보안 액세스 채널을 설치할 수 있는 방법에 대해 간략하게 소개하겠습니다. 조건을 충족하는 인증된 사용자만이 원격 응용 프로그램에 액세스할 수 있도록 하고 안전한 백 엔드만이 다른 위치가 아닌 제어된 Azure RemoteApp 환경에서 액세스할 수 있도록 하는 것이 목표입니다.

관리자가 검토해야 하는 3가지 주요 영역이 있습니다.

![Azure RemoteApp에 대한 조건부 액세스 고려 사항](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

이러한 질문에 대한 정보 및 답변을 계속 읽습니다.

## <a name="who-can-access-the-collection"></a>컬렉션에 액세스할 수 있는 사용자는 누구인가요?
관리자는 컬렉션의 원격 응용 프로그램에 액세스할 수 있는 사용자를 선택합니다. Azure AD(Azure Active Directory) 회사 또는 학교 계정(이전 명칭, "조직 계정") 또는 Microsoft 계정을 사용할 수 있습니다.(예: @outlook.com). Azure AD 계정을 사용하는 대부분의 엔터프라이즈 시나리오는 뒷부분에서 설명할 조건부 액세스 기능을 사용하며 도메인에 가입된 컬렉션에 대한 유일한 선택입니다. 문서의 나머지 부분에서는 Azure AD 계정을 Azure RemoteApp과 함께 사용한다고 가정합니다.

**수행한 내용:**

Azure RemoteApp에 대한 액세스를 제어하는 Azure AD 계정을 사용하면 다음 두 가지를 제공합니다.

1. 게시한 응용 프로그램에 액세스할 수 있고 연결할 해당 백 엔드 응용 프로그램에 액세스할 수 있는 사용자를 항상 압니다.
2. 기본 Azure AD를 제어하므로 사용자 계정을 만들고 삭제하며 암호 정책을 설정하고 Multi-Factor Authentication을 사용할 수 있습니다. 

## <a name="how-is-the-collection-accessed-from-where"></a>컬렉션에 어떻게 액세스하나요? 어디서 액세스하나요?
일반적으로 관리자는 Azure RemoteApp과 같은 공용 인터넷 연결 환경에 액세스하기 위한 정책을 정의하려 합니다. 예를 들어 회사 네트워크 외부에서 환경에 액세스하는 사용자가 Multi-Factor Authentication(MFA)을 사용하여 액세스 권한을 얻거나 혹은 함께 차단되도록 해야 합니다.

Azure RemoteApp 관리자는 Azure AD Premium을 통해 사용할 수 있는 기능을 사용하여 해당 Azure RemoteApp 환경에 대한 조건부 액세스 정책을 설정할 수 있습니다. 또한 풍부한 보고 및 경고 기능을 사용하여 환경이 액세스되는 방법을 모니터링할 수 있습니다.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Azure RemoteApp에 대한 조건부 액세스를 설정하는 방법
예제 시나리오를 안내합니다. Azure RemoteApp 관리자는 사용자가 회사 네트워크 외부에 있을 경우 환경에 대한 액세스를 차단하려 합니다.

> [!NOTE]
> Azure AD를 Premium 계층으로 업그레이드했고 하나 이상의 Azure RemoteApp 컬렉션을 만들었다고 가정합니다.
> 
> 

1. Azure 포털에서 **Active Directory** 탭을 클릭합니다. 구성하려는 디렉터리를 클릭합니다.
   
   주의: 조건부 액세스가 Azure RemoteApp이 아닌 디렉터리의 속성이므로 모든 구성이 디렉터리 수준에서 수행됩니다. 즉, 이러한 변경을 수행하려면 디렉터리 관리자여야 합니다.
2. **응용 프로그램**을 클릭한 다음 **Microsoft Azure RemoteApp**을 클릭하여 조건부 액세스를 설정합니다. 디렉터리에 별도로 "software as a service" 응용 프로그램 각각에 대한 조건부 액세스를 설정할 수 있습니다.
   ![Azure RemoteApp에 대한 조건부 액세스 설정](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
3. **구성** 탭에서 **액세스 규칙 사용**을 [켜기]로 설정합니다.
   ![Azure RemoteApp에 대한 액세스 규칙 사용](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
4. 이제 다양한 규칙을 구성하고 이에 적용할 사용자를 선택할 수 있습니다.
   
   1. **직장이 아닐 때 액세스 차단** 을 선택하여 사용자가 지정한 네트워크 환경 외부에서 Azure RemoteApp에 액세스하는 것을 완전히 차단합니다.
   2. 아래 옵션을 클릭하여 "신뢰할 수 있는 네트워크"를 구성하는 IP 주소 범위를 정의합니다. 외부의 모든 항목은 거부됩니다.
5. 지정한 범위 외부의 IP 주소에서 Azure RemoteApp 클라이언트를 시작하여 구성을 테스트합니다. Azure AD 자격 증명으로 로그인한 후에 다음과 같은 메시지가 표시되어야 합니다.

![Azure RemoteApp에 대한 거절된 액세스](./media/remoteapp-secureaccess/ra-accessdenied.png)

### <a name="future-conditional-access-features"></a>미래 조건부 액세스 기능
Azure Active Directory팀은 조건부 액세스의 새로운 기능을 위해 노력하고 있습니다. 관리자는 네트워크 위치 기반 규칙 이외의 새로운 형식의 규칙을 만들 수 있습니다. 새 기능의 공개 미리 보기를 곧 사용할 수 있습니다.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Azure RemoteApp에 대한 액세스를 모니터링하는 방법
조건부 액세스와 함께 사용하는 훌륭한 기능은 Azure Active Directory Premium 보고 기능입니다. 보고서를 사용하여 누가 환경에 액세스하는지를 모니터링하고 의심스러운 활동을 검색할 수 있습니다.

예를 들어 Azure RemoteApp에 액세스한 사용자의 이름, 액세스 횟수 및 시각을 볼 수 있습니다.

1. Azure 포털에서 **Active Directory**를 클릭한 다음 디렉터리를 클릭합니다.
2. **보고서** 탭으로 이동합니다.
3. 보고서 목록의 **통합된 응용 프로그램**에서 **응용 프로그램 사용 현황**을 선택합니다.
   
   Azure RemoteApp에 대한 몇 가지 집계된 통계를 볼 수 있습니다. 
   ![집계된 Azure RemoteApp 액세스 통계](./media/remoteapp-secureaccess/ra-accessstats.png)
4. 응용 프로그램을 클릭하여 Azure RemoteApp에 액세스하는 사용자에 대한 정보를 노출합니다.
   ![Azure RemoteApp에 대한 사용자 액세스 통계](./media/remoteapp-secureaccess/ra-userstats.png)

### <a name="summary"></a>요약
Azure Active Directory Premium으로 Azure RemoteApp(및 Azure AD를 통해 사용할 수 있는 서비스 응용 프로그램과 같은 기타 소프트웨어)에 대한 액세스 규칙을 설정할 수 있습니다. 규칙은 현재 네트워크 위치 기반 정책으로 제한되지만 나중에 엔터프라이즈 관리의 다른 측면으로 확장됩니다.

또한 Azure AD Premium은 관리자가 Azure RemoteApp 환경을 통해 추가 컨트롤을 확장하는 보고 및 모니터링 기능을 제공합니다.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>내 보안 리소스가 내 Azure RemoteApp 환경에서만 액세스할 수 있는지 확인하려면 어떻게 해야 하나요?
이 문서의 이전 섹션에서 Azure RemoteApp 환경에 대한 액세스를 보호하는 데에 집중했습니다. 액세스할 수 있는 사용자를 선택하고 서비스를 사용할 수 있는 방법을 세부적으로 제어하는 액세스 규칙을 설정하여 이를 수행했습니다.

Azure RemoteApp 배포에 대한 일반적인 시나리오는 원격 응용 프로그램이 예를 들어 SQL 데이터베이스와 같은 백 엔드 리소스와 통신해야 한다는 것입니다. 이 리소스는 온-프레미스(예: 회사 네트워크) 또는 클라우드(예: Azure IaaS)에서 호스팅됩니다. 관리자는 주로 백 엔드 리소스가 사용자의 PC에서 직접 실행되고 공용 인터넷을 통해 액세스하는 응용 프로그램이 아닌 Azure RemoteApp을 통해 배포된 응용 프로그램에서 액세스하도록 하려고 합니다. Azure RemoteApp은 주로 중앙에서 관리되고 보안된 환경이므로 사용자가 백 엔드 리소스와 상호 작용하는 유일한 경로로 표시됩니다.

솔루션은 동일한 Azure 가상 네트워크(VNET)에서 Azure RemoteApp 환경 및 보안 리소스를 모두 배치하는 것입니다. 리소스가 다른 사이트에 있으면 Azure 데이터 센터 및 고객 온-프레미스 환경에 걸쳐 VNet을 만드는 것과 같은 사이트 간 VPN 연결을 설정할 수 있습니다.

Azure RemoteApp에서는 고유의 VNET을 제공할 수 있는 두 가지 형식의 컬렉션 배포를 지원합니다.

* 도메인에 가입되지 않음: VNET에서 다른 리소스의 "line of sight"를 갖게 됩니다. 예를 들어 SQL 인증을 사용하는 SQL 데이터베이스에 응용 프로그램을 연결하는 데 사용할 수 있습니다.(응용 프로그램은 데이터베이스에 대해 직접 사용자를 인증합니다)
* 도메인에 가입: Azure RemoteApp에서 사용되는 가상 컴퓨터는 VNET의 도메인 컨트롤러에 가입됩니다. 응용 프로그램이 백 엔드 리소스에 대한 액세스를 얻기 위해 Windows 도메인 컨트롤러에 대해 인증을 해야 하는 경우 유용합니다.
  ![Azure RemoteApp의 도메인 가입 컬렉션](./media/remoteapp-secureaccess/ra-domainjoined.png)

### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Azure와 내 온-프레미스 환경 간의 보안 연결을 만드는 방법
Azure와 온-프레미스 환경을 연결하기 위한 몇 가지 구성 옵션이 있습니다. 옵션의 개요는 있습니다.

Azure RemoteApp으로 VNet을 구성해야 하고 컬렉션의 만들기 과정에서 사용해야 합니다. 

## <a name="the-complete-solution"></a>전체 솔루션
아래 다이어그램에서는 최종 사용자로부터 Azure RemoteApp(ARA)을 통해 백 엔드 리소스에 보안 액세스 채널을 작성한 완전한 솔루션을 보여줍니다.
![Azure RemoteApp 보안](./media/remoteapp-secureaccess/ra-secureoverview.png) 1단계에서 사용자를 선택하고 ARA에 액세스할 수 있는 방법을 제어하는 액세스 규칙을 만들었습니다. 아래 예제에서는 회사 네트워크에서 작업하는 사용자에 대해 액세스할 수 있습니다. 비규격 사용자는 ARA 환경에 전혀 액세스할 수 없습니다.
"2단계"에서 제어하는 VNet/VPN 구성을 통해서만 백 엔드 리소스를 노출했습니다. Azure RemoteApp은 동일한 VNet에 있습니다. 최종 결과는 ARA 환경을 통해 액세스할 수 있는 리소스입니다.




<!--HONumber=Dec16_HO2-->


