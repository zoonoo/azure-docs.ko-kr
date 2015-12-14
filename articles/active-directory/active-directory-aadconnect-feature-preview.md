<properties
   pageTitle="Azure AD Connect: 미리 보기의 기능 | Microsoft Azure"
   description="이 항목에서는 Azure AD Connect의 미리 보기에 있는 기능을 더 자세하게 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/02/2015"
   ms.author="andkjell;billmath"/>

# 미리 보기 기능에 대한 자세한 내용
이 항목에서는 현재 미리 보기의 기능을 사용하는 방법에 대해 설명합니다.

## 사용자 쓰기 저장
> [AZURE.IMPORTANT]사용자 쓰기 저장 미리 보기 기능은 AAD Connect 8월 업데이트에서 임시로 제거되었습니다. 이 기능을 사용하도록 설정한 경우 사용하지 않도록 설정해야 합니다.

사용자 쓰기 저장은 초기 미리 보기에 있습니다. Azure AD가 모든 사용자 개체의 원본이고 이 기능을 사용하도록 설정하기 전에 온-프레미스 Active Directory가 비어 있는 경우에만 이 기능을 사용할 수 있습니다.

> [AZURE.IMPORTANT]이 기능은 테스트 환경에서만 테스트해야 하며 프로덕션 용도로 사용되는 Azure AD 디렉터리에서는 사용할 수 없습니다.

## 그룹 쓰기 저장
선택적 기능의 그룹 쓰기 저장에 대한 옵션을 사용하면 Exchange가 설치된 포리스트로 "Office 365 그룹"을 쓰기 저장할 수 있습니다. 항상 클라우드에서 마스터되는 새 그룹 종류입니다. 다음과 같이 outlook.office365.com 또는 myapps.microsoft.com에서 찾을 수 있습니다.


![동기화 필터링](./media/active-directory-aadconnect-feature-preview/office365.png)

![동기화 필터링](./media/active-directory-aadconnect-feature-preview/myapps.png)

이 그룹은 온-프레미스 AD DS에서 배포 그룹으로 표현됩니다. 이 새 그룹 종류를 인식하려면 온-프레미스 Exchange는 Exchange 2013 누적 업데이트 8(2015년 3월에 릴리스됨)이어야 합니다.

**참고**

- 미리 보기에서 현재 주소록 특성이 채워지지 않습니다. 이 작업을 수행하는 가장 쉬운 방법은 Exchange PowerShell cmdlet update-recipient를 사용하는 것입니다.
- Exchange 스키마가 있는 포리스트만 그룹에 대한 유효한 대상이 됩니다. 검색된 Exchange가 없는 경우, 그룹 쓰기 저장을 사용하도록 설정할 수 없습니다.
- 그룹 쓰기 저장 기능은 현재 보안 그룹 또는 배포 그룹을 처리하지 않습니다.

Office 365 그룹에 대한 자세한 내용은 [여기](http://aka.ms/O365g)에 있습니다.

## 디렉터리 확장
디렉터리 확장을 사용하면 온-프레미스 Active Directory의 사용자 고유 특성을 사용하여 Azure AD에서 스키마를 확장할 수 있습니다.

단일 값 특성만이 지원되고 특성에서 값이 250자를 초과할 수 없습니다. 메타 버스 및 Azure AD 스키마는 선택된 특성으로 확장됩니다. Azure AD에서 새 응용 프로그램은 특성으로 추가됩니다.

![동기화 필터링](./media/active-directory-aadconnect-feature-preview/extension3.png)

이제 이 특성은 그래프를 통해 사용할 수 있습니다.

![동기화 필터링](./media/active-directory-aadconnect-feature-preview/extension4.png)

## 다음 단계
[Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 계속 진행합니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_1203_2015-->