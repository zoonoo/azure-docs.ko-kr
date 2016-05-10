<properties
	pageTitle="Azure Active Directory 도메인 서비스 미리 보기: FAQ | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스에 대해 자주 묻는 질문과 대답입니다."
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/31/2016"
	ms.author="maheshu"/>

# Azure Active Directory 도메인 서비스 개요: FAQ

이 페이지는 Azure Active Directory 도메인 서비스 미리 보기에 대한 자주 묻는 질문을 응답합니다. 업데이트를 계속 확인합니다.

### 문제 해결 가이드
Azure AD 도메인 서비스를 구성 또는 관리할 때 발생하는 일반적인 문제에 대한 솔루션은 [문제 해결 가이드](active-directory-ds-troubleshooting.md)를 참조합니다.


### 구성

#### 단일 Azure AD 디렉터리에 여러 도메인을 만들 수 있나요?
아니요. 단일 Azure AD 디렉터리에 대해 Azure AD 도메인 서비스에서 서비스되는 단일 도메인만 만들 수 있습니다.

#### 내 구독 내의 여러 가상 네트워크에서 사용할 수 있도록 Azure AD 도메인 서비스를 만들 수 있나요?
서비스 자체는 이 시나리오를 직접 지원하지 않습니다. Azure AD 도메인 서비스는 한 번에 하나의 가상 네트워크에서 사용할 수 있습니다. 그러나 다른 가상 네트워크에 Azure AD 도메인 서비스를 노출하기 위해 여러 가상 네트워크 간의 연결을 구성할 수 있습니다. 이 문서에서는 [Azure에서 가상 네트워크를 연결](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)하는 방법을 설명합니다.

#### PowerShell을 사용하여 Azure AD 도메인 서비스를 사용할 수 있나요?
Azure AD 도메인 서비스의 PowerShell/자동화된 배포는 현재 사용할 수 없습니다.

#### Azure AD 도메인 서비스는 새 Azure 포털에서 사용할 수 있나요?
아니요. Azure AD 도메인 서비스는 [Azure 클래식 포털](https://manage.windowsazure.com)에서만 구성할 수 있습니다. 향후 [Azure 포털](https://portal.azure.com)에 대한 지원을 확장할 예정입니다.

#### Azure AD 도메인 서비스 관리되는 도메인에 도메인 컨트롤러를 추가할 수 있나요?
아니요. Azure AD 도메인 서비스에서 제공하는 도메인은 관리되는 도메인입니다. 이 도메인에 대해서는 도메인 컨트롤러를 프로비전, 구성 또는 다른 방식으로 관리하지 않아도 됩니다. 이러한 관리 작업은 Microsoft에서 서비스 형태로 제공합니다. 따라서 관리되는 도메인에 대해 추가 도메인 컨트롤러(읽기-쓰기 및 읽기 전용 모두 없음)를 추가할 수 없게 됩니다.

### 관리 및 운영

#### 원격 데스크톱을 사용하여 관리되는 도메인의 도메인 컨트롤러에 연결할 수 있습니까?
아니요. 관리되는 도메인이기 때문에, 원격 데스크톱을 통해 도메인에 대한 도메인 컨트롤러에 연결할 수 없습니다. 'AAD DC Administrators' 그룹의 멤버는ADAC(Active Directory 관리 센터) 또는 AD PowerShell 같은 AD 관리 도구를 사용하여 관리되는 도메인을 관리할 수 있습니다. 이러한 도구는 관리되는 도메인에 가입된 Windows 서버의 '원격 서버 관리 도구' 기능을 사용하여 설치할 수 있습니다.

#### Azure AD 도메인 서비스를 사용한 적이 있습니다. 이 도메인에 도메인 가입 컴퓨터를 사용하려면 사용자 계정은 무엇입니까?
관리 그룹에 추가한 모든 사용자 계정(즉, ‘AAD DC Administrators’)은 컴퓨터를 도메인에 가입시킬 수 있습니다. 또한 이 그룹의 사용자에게는 도메인에 가입 된 컴퓨터에 대한 원격 데스크톱 액세스가 부여됩니다.

#### Azure AD 도메인 서비스에서 제공하는 도메인에 대한 도메인 관리자 권한을 사용할 수 있습니까?
아니요. 관리되는 서비스이기 때문에, 도메인에 대한 관리 권한이 부여되지 않습니다. 즉, '도메인 관리자' 뿐만 아니라 '엔터프라이즈 관리자' 권한도 모두 도메인 내에서 사용할 수 없습니다. 또한 Azure AD 디렉터리 내에서 기존 도메인 관리자 또는 엔터프라이즈 관리자 그룹에는 도메인에서 도메인/엔터프라이즈 관리자 권한이 부여되지 않습니다.

#### Azure AD 도메인 서비스에서 제공 하는 도메인에 LDAP 또는 다른 AD 관리 도구를 사용하여 그룹 멤버 자격을 수정할 수 있습니까?
아니요. Azure AD 도메인 서비스에서 서비스를 제공하는 도메인에 그룹 멤버 자격을 수정할 수 없습니다. 사용자 특성에 대해서도 동일하게 적용됩니다. 그러나 Azure AD 또는 온-프레미스 도메인에서 그룹 구성원 자격 또는 사용자 특성을 변경할 수도 있습니다. 이러한 변경 내용은 Azure AD 도메인 서비스에 자동으로 동기화됩니다.

#### Azure AD 도메인 서비스에서 제공하는 도메인의 스키마를 확장할 수 있나요?
아니요. 스키마는 관리되는 도메인에 대해 Microsoft에서 관리합니다. 스키마 확장은 Azure AD 도메인 서비스에서 지원되지 않습니다.

#### Azure AD 도메인 서비스에서 제공하는 DNS 레코드를 수정할 수 있나요?
예. 'AAD DC Administrators' 그룹에 속하는 사용자는 관리되는 도메인 내에서 DNS 레코드를 수정하기 위하여 'DNS 관리자' 권한이 부여됩니다. 이러한 사용자는 DNS를 관리하기 위해 관리되는 도메인에 가입한 Windows Server를 실행하는 컴퓨터에서 DNS 관리자 콘솔을 사용할 수 있습니다. DNS 관리자 콘솔을 사용하려면, 'DNS 서버 도구'를 설치합니다. 이것은 서버의 '원격 서버 관리 도구' 선택적 기능의 일부입니다. [DNS를 관리하고, 모니터링하고 문제를 해결하는 유틸리티](https://technet.microsoft.com/library/cc753579.aspx)에 대한 자세한 정보는 TechNet를 참조하세요.

### 요금 청구 및 가용성

#### 유료 서비스입니까?
서비스는 공개 미리 보기 기간 동안에 특별히 절감된 가격에 사용할 수 있습니다. 서비스가 일반적으로 사용 가능(GA)하면 전체 가격을 지불합니다. 자세한 내용은 가격 책정 페이지를 참조하세요.

#### 서비스에 대한 무료 평가판이 있습니까?
이 서비스는 Azure을 위해 무료 평가판에 포함됩니다. [Azure의 1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

#### Enterprise Mobility Suite(EMS)의 일부로 Azure AD 도메인 서비스를 가져올 수 있습니까?
아니요, Azure AD 도메인 서비스는 종량제 Azure 서비스이며 EMS의 일부가 아닙니다. Azure AD의 SKU 모두에 사용 가능한 Azure AD 도메인 서비스(무료, 기본, 및 프리미엄)는 사용에 따라 시간 단위로 요금이 청구됩니다.

#### 어떤 Azure 지역에서 서비스를 사용할 수 있습니까?
Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역의 목록은 [지역 페이지](active-directory-ds-regions.md)를 참조하세요.

#### 언제 Azure AD 도메인 서비스를 일반적으로 사용할 수 있습니까?
현재 언제 서비스를 일반적으로 사용할 수 있는지에 대한 타임 라인을 공유할 수 없습니다.

<!---HONumber=AcomDC_0427_2016-->