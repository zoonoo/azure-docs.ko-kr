<properties
	pageTitle="허가되지 않은 사용 현황 보고서 | Microsoft Azure"
	description="허가되지 않은 사용 현황 보고서를 사용하면 유료 Azure AD 기능을 사용하는 허가되지 않은 사용자를 식별할 수 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="markvi"/>

# 허가되지 않은 사용 현황 보고서

허가되지 않은 사용 현황 보고서를 사용하면 유료 Azure AD 기능을 사용하는 허가되지 않은 사용자를 식별할 수 있습니다. 이를 통해 구매한 라이선스를 더 잘 활용할 수 있도록 해주고 추가 라이선스가 필요할 때를 알려줍니다.

보고서는 지난 30 일 동안 유료 기능의 실제 사용 현황을 보여줍니다.

## 보고서 구조
 
| 열 이름 |	설명 |
| :--                  | :--         |
| 허가되지 않은 사용자 |	사용자의 이름 |
| 기능 | 기능 이름 예: 조건부 액세스 |
| 액세스한 응용 프로그램 | 기능에 액세스하는 응용 프로그램의 이름입니다. 예: Office 365 SharePoint Online |

 
> [AZURE.NOTE] 사용자 계정이 삭제된 경우 ‘허가되지 않은 사용자’ 열은 1003000090D8B285와 같은 ID로 채워집니다.


## 조건부 액세스 기능

Azure AD Premium 라이선스가 없는 경우에 적용되는 조건부 액세스 정책이 있는 서비스에 허가되지 않은 사용자가 액세스하면 플래그가 지정됩니다.

이는 MFA/위치 정책뿐만 아니라 Intune을 사용하는 장치 정책에 적용됩니다.
 

## 참고 항목

- [Office 365 및 기타 Azure Active Directory 연결 앱과 함께 조건부 액세스 사용](active-directory-conditional-access.md)
- [Azure AD 조건부 액세스 시작하기](active-directory-conditional-access-azuread-connected-apps.md)

<!---HONumber=AcomDC_0727_2016-->