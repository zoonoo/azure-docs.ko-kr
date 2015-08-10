<properties
	pageTitle="Azure AD Connect 동기화: 다중 포리스트 시나리오 개요"
    description="이 항목의 목적은 몇 가지 일반적인 시나리오를 다루고 Azure AD Connect Sync의 동기화 서비스에서 이러한 시나리오가 표현되는 방법을 다룹니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect 동기화: 다중 포리스트 시나리오 개요

많은 조직에는 여러 온-프레미스 Active Directory 포리스트가 포함된 환경이 있습니다. 둘 이상의 온-프레미스 Active Directory 포리스트가 배포된 이유는 여러 가지가 있습니다. 일반적인 예로 계정 리소스 포리스트, 합병 및 인수 관련 포리스트 또는 데이터를 아웃소싱하는 데 사용하는 포리스트 설계가 있습니다.

Microsoft는 단일 포리스트용 솔루션인 DirSync 및 다중 포리스트 시나리오용 솔루션인 FIM을 제공합니다. 그러나 FIM의 구성은 어려울 수 있으며 상당한 시간이 필요할 수 있습니다.

Azure AD Connect Sync를 사용하면 구성을 크게 간소화하고 보다 예측적으로 만들 수 있습니다.

Azure AD Connect Sync에 의해 제공된 기본 구성에서는 다음과 같이 가정됩니다.

1. 사용자는 하나의 계정만 사용할 수 있으며 이 계정이 위치한 포리스트는 사용자를 페더레이션하는 데 사용됩니다.
2. 사용자에게는 하나의 사서함만 있습니다.
3. 사용자의 사서함을 호스트하는 포리스트에는 Exchange 전체 주소 목록(GAL)에 표시되는 특성에 대해 최상의 데이터 품질을 가집니다. 사용자에 대해 사서함이 없는 경우 아무 포리스트나 사용하여 이러한 특성 값에 기여할 수 있습니다.


이 항목의 목적은 몇 가지 일반적인 시나리오를 다루고 Azure AD Connect Sync의 동기화 서비스에서 이러한 시나리오가 표현되는 방법을 다룹니다.

1. 기술 구분 
2. 선택적 GALSync 사용한 전체 메시 
3. 계정 리소스 포리스트 


## 기술 구분

이 환경에서는 모든 포트리스 온-프레미스가 별개의 엔터티로 취급되며 다른 포리스트에 사용자가 없습니다.<br> 각 포리스트에는 자체적인 Exchange 조직이 있으며 포리스트 간에 GALSync가 없습니다. 이것은 합병/인수 후 또는 각 사업부가 서로 격리되어 운영되는 조직 내의 상황일 수 있습니다.

![기술 구분][1]

이 그림에서는 각 포리스트에 있는 각 개체가 메타 버스에 한 번 표시되며 대상 Azure AD 디렉터리에서 집계됩니다. 이것은 각 원본 AD 포리스트에 1개의 DirSync 서버가 연결된 것과 동일한 최종 결과입니다.
 




## 선택적 GALSync 사용한 전체 메시

완전한 메시 토폴로지는 사용자와 리소스가 아무 포리스트에나 위치할 수 있게 지원하며 일반적으로 포리스트 사이에 양방향 트러스트가 있습니다.

Exchange가 1개 이상의 포리스트에 있다면 1개의 포리스트에 있는 사용자를 서로의 포리스트 내 연락처로 표현하는 GALSync 솔루션이 선택적으로 제공될 수 있습니다.

이 시나리오에서는 id 개체가 일반적으로 메일 특성을 사용하여 조인됩니다. 따라서 하나의 포리스트에 사서함이 있는 사용자는 다른 포리스트의 연락처와 조인됩니다. 배포 및 보안 그룹을 각 포리스트에 찾을 수 있으며 다양한 사용자, 연락처 및 FSP(외부 보안 주체)를 포함할 수 있습니다.

다음 그림은 이 시나리오를 간략하게 설명합니다.

![선택적 GALSync 사용한 전체 메시][2]


## 계정 리소스 포리스트
계정 리소스 포리스트 토폴로지에는 활성 사용자 계정이 있는 하나 이상의 계정 포리스트가 있습니다.<br> 이 시나리오에는 모든 계정 포리스트를 신뢰하는 하나의 포리스트가 포함됩니다. 이 포리스트에는 일반적으로 Exchange 및 Lync와 확장된 AD 스키마가 있습니다. 모든 Exchange, Lync 서비스는 물론 기타 공유 서비스는 이 포리스트에 있습니다. 사용자는 이 포리스트에 비활성화된 사용자 계정이 있으며, 사서함이 이 계정 포리스트에 연결됩니다.

아래 그림은 하나의 계정만 사용하여 이 시나리오를 요약합니다.

![계정 리소스 포리스트][3]


## 추가 리소스

* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=July15_HO5-->