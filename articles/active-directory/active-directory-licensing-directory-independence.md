<properties
   pageTitle="여러 Azure Active Directory 추가 및 관리 | Microsoft Azure"
   description="Azure Active Directory 추가 및 관리를 위한 지침 및 모범 사례와 더불어 디렉터리를 완전히 독립된 리소스로 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/05/2016"
   ms.author="curtand"/>

# 여러 Azure Active Directory 추가 및 관리

Azure AD(Azure Active Directory)의 각 디렉터리는 완전히 독립된 리소스로, 관리하는 다른 디렉터리와 논리적으로 독립된 완전한 기능을 갖춘 피어입니다. 또한 디렉터리 간에는 부모-자식 관계가 없습니다. 디렉터리 간 독립성에는 리소스 독립성, 관리 독립성 및 동기화 독립성이 포함됩니다.

##리소스 독립성

한 디렉터리에서 리소스를 만들거나 삭제해도 다른 디렉터리의 리소스에 영향을 주지 않습니다. 단, 아래에 설명된 외부 사용자는 부분적으로 제외됩니다. 한 디렉터리에서 사용자 지정 도메인 'contoso.com'을 사용하는 경우 다른 디렉터리에서 사용할 수 없습니다.

##관리 독립성

'Contoso' 디렉터리의 관리자가 아닌 사용자가 테스트 디렉터리 ‘Test’를 만들면 다음과 같이 수행됩니다. - 기본적으로 디렉터리를 만든 사용자는 새로 만든 디렉터리에서 외부 사용자로 추가된 후 해당 디렉터리에서 전역 관리자 역할이 할당됩니다. - 'Contoso' 디렉터리의 관리자는 'Test'의 관리자가 'Test' 디렉터리에 대한 관리 권한을 특별히 부여하지 않는 한 이 디렉터리에 대한 직접 관리 권한이 없습니다. 'Contoso'의 관리자가 'Test'를 만든 사용자 계정을 제어하는 경우 해당 관리자는 'Test' 디렉터리에 대한 액세스를 제어할 수 있습니다. - 한 디렉터리의 사용자에 대한 관리자 역할을 변경(추가 또는 제거)하는 경우 이 변경 내용은 사용자가 다른 디렉터리에 대해 가질 수 있는 관리자 역할에 영향을 주지 않습니다.

##동기화 독립성

각 Azure AD 디렉터리를 독립적으로 구성하여 다음 중 하나의 단일 인스턴스에서 데이터를 동기화할 수 있습니다. - 데이터를 단일 AD 포리스트와 동기화하는 디렉터리 동기화 도구(DirSync). - 데이터를 하나 이상의 온-프레미스 포리스트 및/또는 Azure AD 이외의 데이터 소스와 동기화하는 Forefront Identity Manager용 Azure Active Directory 커넥터.

##Azure AD 디렉터리 추가

Azure 클래식 포털에서 Azure AD 디렉터리를 추가하려면 왼쪽의 Azure Active Directory 확장을 선택하고 **추가**를 탭합니다.

> [AZURE.NOTE]다른 Azure 리소스와 달리 디렉터리는 Azure 구독의 자식 리소스가 아닙니다. Azure 구독을 취소하거나 만료를 허용하는 경우에도 Azure PowerShell, Azure Graph API 또는 Office 365 관리 센터와 같은 다른 인터페이스를 사용하여 디렉터리 데이터에 계속 액세스할 수 있습니다. 다른 구독을 디렉터리와 연결할 수도 있습니다.

Azure AD 라이선스 문제 및 모범 사례에 대한 광범위한 개요는 [Azure Active Directory 라이선스란?](active-directory-licensing-what-is.md)을 참조하세요.

<!---HONumber=AcomDC_0107_2016-->