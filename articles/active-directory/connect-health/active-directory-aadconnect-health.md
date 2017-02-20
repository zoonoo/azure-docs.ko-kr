---
title: "클라우드에서 온-프레미스 ID 인프라 모니터링"
description: "사용되는 항목 및 이유를 설명하는 Azure AD Connect 상태 페이지입니다."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 21e599fc2adf3d7dce84cc1c4130504b71928db7
ms.openlocfilehash: c8c5f30e4cb0467f6ccdc65d96b57b766593ba31


---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>온-프레미스 ID 인프라 및 클라우드 동기화 서비스를 모니터링합니다.
Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라 및 동기화 서비스를 모니터링하고 파악할 수 있습니다.  AD FS 서버, Azure AD Connect 서버(즉, 동기화 엔진), Active Directory 도메인 컨트롤러 등과 같은 핵심 ID 구성 요소를 위한 모니터링 기능을 제공하여 Office 365 및 Microsoft Online Services에 대해 신뢰할 수 있는 연결을 유지 관리할 수 있습니다. 또한 이러한 구성 요소에 대한 핵심 데이터 요소를 액세스하기 쉽게 만들어 사용 현황 및 기타 중요한 통찰력을 보다 쉽게 얻을 수 있습니다.

정보는 [Azure AD Connect Health 포털](https://aka.ms/aadconnecthealth)에 표시됩니다. Azure AD Connect Health 포털을 사용하여 경고, 성능 모니터링 및 사용 현황 분석 등을 볼 수 있습니다. Azure AD Connect Health를 통해 핵심 ID 구성 요소의 상태를 한 곳에서 단일 렌즈로 볼 수 있습니다.

![Azure AD Connect Health 정의](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Azure AD Connect Health에 대한 이후 업데이트는 추가 모니터링 및 추가 ID 구성 요소에 대한 통찰력을 포함합니다. ID의 렌즈를 통한 단일 대시보드를 제공하고 사용자가 작업을 완료하는 기능을 향상시킬 수 있도록 하는 훨씬 더 강력하고 정상적이며 통합된 환경을 활용합니다.

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하는 이유
Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 그러나 이 통합을 통해 사용자가 모든 장치에서 온-프레미스와 클라우드 모두의 리소스에 안정적으로 액세스할 수 있도록 이 환경이 정상적인지 확인해야 하는 문제가 있습니다. Azure AD Connect Health는 온-프레미스 ID 인프라에 대해 쉽게 모니터링하고 통찰력을 얻을 수 있는 클라우드 기반 접근 방식을 제공하여 Office 365 또는 다른 Azure AD 응용 프로그램에 액세스하는 데 사용됩니다. 각 온-프레미스 ID 서버에 에이전트를 설치하는 것만큼 간단합니다.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[AD FS에 대한 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
AD FS에 대한 Azure AD Connect Health는 Windows Server 2008 R2에서 AD FS 2.0, Windows Server 2012 및 Windows Server 2012R2에서 AD FS를 지원합니다. 또한 엑스트라넷 액세스에 대한 인증 지원을 제공하는 AD FS 프록시 또는 웹 응용 프로그램 프록시 서버의 모니터링을 지원합니다. AD FS에 대한 Azure AD Connect Health는 Health Agent의 쉽고 저렴한 설치 비용과 함께 다음과 같은 주요 기능 집합을 제공합니다.

* AD FS 및 AD FS 프록시 서버가 정상이 아닐 때 알아야 하는 경고 모니터링
* 중요한 경고에 대한 전자 메일 알림
* AD FS의 용량 계획에 유용한 성능 데이터의 동향 보기
* AD FS를 어떻게 활용하는지 이해하는 데 유용하며 다양한 피벗(앱, 사용자, 네트워크 위치 등)을 통해 AD FS 로그인의 사용량 분석입니다.
* 최근 IP 주소로 잘못된 사용자 이름/암호를 시도한 상위 50명의 사용자와 같은 AD FS에 대한 보고서

다음 비디오는 AD FS에 대한 Azure AD Connect Health의 개요를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[동기화에 대한 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
동기화에 대한 Azure AD Connect Health는 온-프레미스 Active Directory와 Azure Active Directory 간에 발생하는 동기화에 대한 정보를 모니터링하고 제공합니다. 동기화에 대한 Azure AD Connect Health는 다음과 같은 주요 기능 집합을 제공합니다.

* Azure AD Connect 서버 즉, 동기화 엔진이 정상이 아닐 때 알아야 하는 경고 모니터링
* 중요한 경고에 대한 전자 메일 알림
* 다른 작업에 대한 대기 시간 차트, 동기화 작업(추가, 업데이트, 삭제 등)의 동향을 비롯한 동기화 작업 통찰력
* 동기화 속성, 최근에 성공한 Azure AD로 내보내기에 대한 정보 빠른 보기
* 개체 수준 동기화 오류에 대한 보고서 \(는 Azure AD Premium\)이 필요하지 않습니다.

다음 비디오는 동기화에 대한 Azure AD Connect Health의 개요를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-ds-previewactive-directory-aadconnect-health-addsmd"></a>[AD DS용 Azure AD Connect Health(미리 보기)](active-directory-aadconnect-health-adds.md)
AD DS용 Azure AD Connect Health는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에 설치된 도메인 컨트롤러에 대한 모니터링을 제공합니다. 쉽고 저렴한 비용 상태 에이전트 설치를 사용하면 클라우드에서 직접 온-프레미스 AD DS 환경을 모니터링할 수 있습니다. AD DS용 Azure AD Connect Health는 다음과 같은 주요 기능 집합을 제공합니다.

* 도메인 컨트롤러가 중요한 경고에 대한 전자 메일 알림과 함께 비정상인 시기를 감지하는 경고 모니터링
* 도메인 컨트롤러의 상태 및 작동 상태에 대한 빠른 보기를 제공하는 도메인 컨트롤러 대시보드
* 오류가 감지되면 문제 해결 가이드에 대한 링크와 함께 최신 복제 정보를 사용하는 복제 상태 대시보드
* 문제 해결 및 모니터링을 위해 필요한 인기 있는 성능 카운터의 성능 데이터 그래프에 대한 빠른 액세스

다음 비디오는 AD DS에 대한 Azure AD Connect Health의 개요를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health 시작
Azure AD Connect Health를 시작하기가 매우 쉽습니다. 다음 단계를 따르세요.

1. [Azure AD Premium](../active-directory-get-started-premium.md)을 다운로드하거나 [평가판을 시작](https://azure.microsoft.com/trial/get-started-active-directory/)합니다.
2. [Azure AD Connect Health 에이전트를 다운로드하고 설치합니다](#download-and-install-azure-ad-connect-health-agent) .
3. [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth)

> [!NOTE]
> Azure AD Connect Health 대시보드에 데이터를 표시하려면 대상 서버에 Azure AD Connect Health Agent를 설치해야 합니다.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Azure AD Connect Health 에이전트 다운로드 및 설치
* Azure AD Connect Health에 대한 [요구 사항을 충족](active-directory-aadconnect-health-agent-install.md#requirements)해야 함
* AD FS용 Azure AD Connect Health 사용 시작
    * [AD FS용 Azure AD Connect Health Agent 다운로드.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [설치 지침 참조](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* 동기화용 Azure AD Connect Health 사용 시작
    * [최신 버전의 Azure AD Connect 다운로드 및 설치](http://go.microsoft.com/fwlink/?linkid=615771). 동기화용 상태 에이전트는 Azure AD Connect 설치의 일부로 설치됩니다(버전 1.0.9125.0 이상).
* AD DS용 Azure AD Connect Health 사용 시작
    * [AD DS용 Azure AD Connect Health Agent 다운로드.](http://go.microsoft.com/fwlink/?LinkID=820540)
    * [설치 지침 참조](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
    
## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health 포털
Azure AD Connect Health 포털을 사용하여 경고, 성능 모니터링 및 사용 현황 분석을 볼 수 있습니다. https://aka.ms/aadconnecthealth에서 Azure AD Connect Health의 주 블레이드로 이동합니다.  블레이드를 창으로 생각할 수 있습니다. 기본 블레이드에서 빠른 시작, Azure AD Connect Health 내 서비스 및 추가 구성 옵션이 표시됩니다. 스크린샷 아래에는 이들 각각에 대해 간략한 설명이 있습니다.  에이전트를 배포한 후에 상태 서비스는 Azure AD Connect Health가 모니터링하는 서비스에 대해 자동으로 식별됩니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/portal4.png)

* **빠른 시작** – 이 항목을 선택하면 빠른 시작 블레이드가 열립니다. 여기에서 도구 가져오기를 선택하여 Azure AD Connect Health Agent를 다운로드하고, 설명서에 액세스하고, 피드백을 제공할 수 있습니다.
* **Active Directory Federation Services** – Azure AD Connect Health에서 현재 모니터링하는 모든 AD FS 서비스를 나타냅니다. 인스턴스 중 하나를 선택하면, 서비스 인스턴스에 대한 정보가 포함된 블레이드가 열립니다.  개요, 속성, 경고, 모니터링 및 사용 현황 분석이 이러한 정보에 포함됩니다. [여기](active-directory-aadconnect-health-adfs.md)
* **Azure Active Directory Connect(동기화)** – Azure AD Connect Health가 현재 모니터링하는 Azure AD Connect 서버를 나타냅니다. 항목을 선택하면 Azure AD Connect 서버에 대한 정보와 함께 블레이드가 열립니다. [여기](active-directory-aadconnect-health-sync.md)
* **Active Directory 도메인 서비스** – Azure AD Connect Health가 현재 모니터링하는 모든 AD FS 포리스트를 나타냅니다. 포리스트 중 하나를 선택하여 포리스트에 대한 정보가 포함된 블레이드가 열립니다.  이 정보에는 중요한 정보의 개요, 도메인 컨트롤러 대시보드, 복제 상태 대시보드, 경고 및 모니터링이 포함됩니다. [여기](active-directory-aadconnect-health-adds.md)
* **구성** – 여기에서는 다음 옵션을 켜거나 끌 수 있습니다.

  1. 자동으로 Azure AD Connect Health Agent를 최신 버전으로 업데이트하는 자동 업데이트 - Azure AD Connect Health Agent의 최신 버전을 사용할 수 있게 되면 자동으로 업데이트됩니다. 이 옵션은 기본적으로 사용하도록 설정되어 있습니다.
  2. Microsoft가 문제 해결을 위해서만 Azure AD 디렉터리 상태 데이터에 액세스할 수 있음 - 이 옵션을 사용하도록 설정하면 사용자에게 표시되는 것과 동일한 데이터를 Microsoft가 볼 수 있습니다. 이 옵션은 문제 해결 및 지원에 도움이 될 수 있습니다. 이 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.

## <a name="related-links"></a>관련 링크
* [Azure AD Connect Health Agent 설치](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 버전 내역](active-directory-aadconnect-health-version-history.md)


<!--HONumber=Feb17_HO2-->


