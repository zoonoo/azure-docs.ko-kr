<properties
	pageTitle="Azure AD 테넌트를 얻는 방법 | Microsoft Azure"
	description="응용 프로그램 등록 및 구축을 위해 Azure Active Directory 테넌트를 얻는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/02/2015"
	ms.author="dastrock"/>

# Azure Active Directory 테넌트를 얻는 방법

Azure Active Directory (Azure AD)에서 [테넌트](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)는 조직을 나타냅니다. 조직이 Azure, Microsoft InTune 또는 Office 365와 같은 Microsoft 클라우드 서비스에 등록할 때 받아서 소유하는 Azure AD 서비스의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다.

테넌트는 회사에 사용자 및 암호, 사용자 프로필 데이터, 사용 권한 등에 대한 사용자의 정보를 저장합니다. 또한 그룹, 응용 프로그램, 조직 및 보안에 관련된 기타 정보도 포함합니다.

Azure AD 사용자가 응용 프로그램에 로그인하도록 허용하려면 자신의 테넌트에 응용 프로그램을 등록해야 합니다. Azure AD 테넌트에 응용 프로그램을 게시하는 것은 **무료**입니다. 사실 대부분의 개발자는 실험, 개발, 준비 및 테스트 목적으로 여러 개의 테넌트를 만듭니다. 응용 프로그램에 등록하고 사용하는 조직에서는 고급 디렉터리 기능을 활용하려는 경우 라이선스를 구입할지 여부를 선택할 수 있습니다.

그럼 Azure AD 테넌트 가져오기를 시작해볼까요? 다음과 같이 경우에 따라 프로세스가 약간 다릅니다.

- [기존 Office 365 구독이 있는 경우](#use-an-existing-office-365-subscription)
- [Microsoft 계정에 연결된 기존 Azure 구독이 있는 경우](#use-an-msa-azure-subscription)
- [조직 계정에 연결된 기존 Azure 구독이 있는 경우](#use-an-organizational-azure-subscription)
- [위의 경우가 하나도 해당되지 않고 처음부터 시작하려는 경우](#start-from-scratch)

## 기존 Office 365 구독 사용
기존 Office 365 구독이 있지만 Azure 구독이 없는 경우(및 [Azure 관리 포털](https://manage.windowsazure.com)에 로그인할 수 없는 경우) [지침](https://technet.microsoft.com/library/dn832618.aspx)에 따라 Azure AD 테넌트에 대한 액세스 권한을 얻으세요.

## MSA Azure 구독 사용
이전에 개인 Microsoft 계정으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다. [Azure 관리 포털](https://manage.windowsazure.com)의 "모든 항목" 및 "Active Directory"에서 "기본 테넌트"라는 이름의 테넌트를 찾아야 합니다. 필요에 따라 이 테넌트를 무료로 사용할 수 있으나 조직 관리자 계정을 만들 수도 있습니다.

이렇게 하려면 다음 단계를 따르세요. 또는 새 테넌트를 만들고 유사한 프로세스 후에 이 테넌트에 관리자를 만들 수도 있습니다.

1.	개인 계정으로 [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2.	포털의 "Active Directory" 섹션으로 이동합니다(왼쪽 탐색 모음에 있음).
3.	사용 가능한 디렉터리 목록에서 "기본 디렉터리" 항목을 선택합니다.
4.	페이지의 맨 위에서 사용자 링크를 클릭합니다. 원본 위치 열의 "Microsoft 계정" 목록에 단일 사용자가 표시됩니다.
5.	페이지 맨 아래에 있는 “사용자 추가"를 클릭합니다.
6.	사용자 추가 양식에 다음 세부 정보를 입력합니다.
    - 사용자 유형: 조직의 새 사용자
    - 사용자 이름: (이 관리자의 사용자 이름 선택)
    - 이름/성/표시 이름: (적절한 값 선택)
    - 역할: 전역 관리자
    - 암호 확인용 메일: (적절한 값 입력)
    - 옵션: Multi-Factor Authentication 사용
    - 마지막으로 녹색 "만들기" 단추를 클릭해 사용자 만들기를 완료합니다(및 임시 암호를 표시합니다).
7.	사용자 추가 양식을 완료하여 새 관리자의 임시 암호를 받으면 암호를 변경하려는 경우 새 사용자로 로그인해야 하므로 이 암호를 기록해두세요. 또한 대체 메일을 사용하여 사용자에게 직접 암호를 보낼 수도 있습니다.
8.	임시 암호를 변경하려면 이 새 사용자 계정으로 https://login.microsoftonline.com에 로그인하고 요청 시 암호를 변경합니다.


## 조직 Azure 구독 사용
이전에 조직 계정으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다. [Azure 관리 포털](https://manage.windowsazure.com)의 "모든 항목" 및 "Active Directory"에서 테넌트를 찾아야 합니다. 필요에 따라 이 테넌트를 무료로 사용할 수 있습니다. 포털의 왼쪽 아래에 있는 "새로 만들기" 단추를 사용하여 새 테넌트를 만들 수도 있습니다.


## 처음부터 시작
위의 모든 항목을 이해할 수 없어도 걱정하지 마세요. 새 조직으로 Azure에 등록하려면 [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization)을 방문하기만 하면 됩니다. 프로세스를 완료하면 등록할 때 선택한 도메인 이름을 가진 자신만의 Azure AD 테넌트가 생깁니다. [Azure 관리 포털](https://manage.windowsazure.com)의 왼쪽 탐색 모음에서 "Active Directory"로 이동하여 테넌트를 찾을 수 있습니다.

Azure 등록 과정의 일부로 신용 카드 정보를 제공해야 합니다. 믿고 진행할 수 있습니다. Azure AD에서의 응용 프로그램 게시 및 새 테넌트 만들기에 대한 비용은 청구되지 않습니다.

<!---HONumber=August15_HO6-->