<properties
   pageTitle="Azure AD B2B 공동 작업 미리 보기: 작동 방법 | Microsoft Azure"
   description="Azure Active Directory B2B 공동 작업이 비즈니스 파트너가 선택적으로 회사 응용 프로그램에 액세스할 수 있게 함으로써 회사 간 관계를 지원하는 방법에 대해 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Azure AD B2B 공동 작업 미리 보기: 작동 방법
Azure AD B2B 공동 작업은 초대 및 충전 모델을 기반으로 합니다. 사용하려는 응용 프로그램과 함께 공동 작업하려는 대상의 메일 주소를 제공합니다. Azure AD는 링크가 포함된 메일 초대를 보냅니다. 파트너 사용자가 링크를 클릭하면 자신의 Azure AD 계정을 사용하여 로그인하거나 새 Azure AD 계정을 등록하라는 메시지가 표시됩니다.

1. 관리자는 Azure 포털을 사용하여 [구조화된 .csv 파일](active-directory-b2b-references-csv-file-format.md)을 업로드하여 파트너 사용자를 초대합니다.
2. 포털은 이러한 파트너 사용자에게 초대 메일을 보냅니다.
3. 파트너 사용자가 메일의 링크를 클릭하면 해당 작업 자격 증명(이미 Azure AD에 있을 경우)을 사용하여 로그인하거나 또는 Azure AD B2B 공동 작업 사용자로 등록하라는 메시지가 표시됩니다.
4. 파트너 사용자가 초대한 응용 프로그램으로 리디렉션됩니다. 이제 사용자에게 액세스 권한이 부여됩니다.

## 디렉터리 작업
파트너 사용자가 외부 사용자로 Azure AD에 있습니다. 이는 관리자가 회사의 사용자에 대해 수행하는 것처럼 Azure 포털을 통해 또는 Azure PowerShell을 사용하여 라이선스를 프로비전하고, 그룹 구성원을 할당하고, 회사 앱에 대해 추가 액세스를 허용할 수 있음을 의미합니다.

Azure AD B2B를 사용하는 데 유료 Azure AD 구독(Basic 또는 Premium)이 필요하지 않지만 유료 Azure AD 구독(Basic 또는 Premium)이 있는 테넌트는 다음과 같은 추가 혜택을 받습니다.

 - 관리자가 그룹을 앱에 할당하여 초대한 사용자 액세스를 더 간편하게 관리할 수 있습니다.
 - 초대 메일과 사용 환경을 브랜딩하는 관리 테넌트 브랜딩을 사용하여 초대한 파트너 사용자에게 더 많은 컨텍스트를 제공합니다.

## 관련 문서
 Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기

 - [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [자세한 연습](active-directory-b2b-detailed-walkthrough.md)
 - [CSV 파일 형식 참조](active-directory-b2b-references-csv-file-format.md)
 - [외부 사용자 토큰 형식](active-directory-b2b-references-external-user-token-format.md)
 - [외부 사용자 개체 특성 변경 사항](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [현재 미리 보기 제한 사항](active-directory-b2b-current-preview-limitations.md)
 - [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->