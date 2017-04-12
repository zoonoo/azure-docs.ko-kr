---
title: "Azure AD Connect 및 페더레이션 | Microsoft Docs"
description: "이 페이지는 Azure AD Connect를 사용하는 AD FS 작업에 관한 모든 설명서의 중심 위치입니다."
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 28df9bfec4db8e3f57db61eecc631545e389085a
ms.lasthandoff: 03/28/2017


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect 및 페더레이션
Azure AD(Azure Active Directory) Connect를 통해 온-프레미스 AD FS(Active Directory Federation Services) 및 Azure AD와 페더레이션을 구성할 수 있습니다. 페더레이션 로그인에서, 사용자가 자신의 온-프레미스 암호로 Azure AD 기반 서비스에 로그인 하고 자신의 암호를 다시 입력하지 않고도 회사 네트워크에 로그인하도록 설정할 수 있습니다. AD FS와 페더레이션 옵션을 사용하여 AD FS의 새 설치를 배포하거나 Windows Server 2012 R2 팜에 기존 설치를 지정할 수 있습니다.

이 항목은 Azure AD Connect의 페더레이션 관련 기능에 대한 정보를 포함합니다. 관련된 다른 모든 항목에 대한 링크를 나열합니다. Azure AD Connect에 대한 링크는 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: 페더레이션 항목
| 항목 | 포함된 내용 및 적용 시기 |
|:--- |:--- |
| **Azure AD Connect 사용자 로그인 옵션** | |
| [사용자 로그인 옵션 이해](active-directory-aadconnect-user-signin.md) |다양한 사용자 로그인 옵션 및 Azure 로그인 사용자 환경에 미치는 영향에 대해 알아봅니다. |
| **Azure AD Connect를 사용하여 AD FS 설치** | |
| [필수 구성 요소](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Azure AD Connect를 통한 성공적인 AD FS 설치의 필수 구성 요소를 참조하세요. |
| [AD FS 팜 구성](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Azure AD Connect를 사용하여 새 AD FS 팜을 설치합니다. |
| **AD FS 구성 수정** | |
| [트러스트 복구](active-directory-aadconnect-federation-management.md#repairthetrust) |온-프레미스 AD FS와 Office 365/Azure 사이의 현재 트러스트를 복구합니다. |
| [새 AD FS 서버 추가](active-directory-aadconnect-federation-management.md#addadfsserver) |초기 설치 후 추가적인 AD FS 서버를 통한 AD FS 팜을 확장합니다. |
| [새 AD FS WAP 서버 추가](active-directory-aadconnect-federation-management.md#addwapserver) |초기 설치 후 추가적인 WAP(웹 응용 프로그램 프록시) 서버를 통한 AD FS 팜을 확장합니다. |
| [새 페더레이션된 도메인 추가](active-directory-aadconnect-federation-management.md#addfeddomain) |Azure AD를 통해 페더레이션될 또 다른 도메인을 추가합니다. |
| [SSL 인증서 업데이트](active-directory-aadconnectfed-ssl-update.md)| AD FS 팜에 대한 SSL 인증서를 업데이트합니다. |
| **설치 후 작업** | |
| [사용자 지정 회사 로고/일러스트레이션 추가](active-directory-aadconnect-federation-management.md#customlogo) |AD FS 로그인 페이지에 표시되는 사용자 지정 로고를 지정하여 로그인 환경을 수정합니다. |
| [로그인 설명 추가](active-directory-aadconnect-federation-management.md#addsignindescription) |AD FS 로그인 페이지의 로그인 설명을 변경합니다. |
| [AD FS 클레임 규칙 수정](active-directory-aadconnect-federation-management.md#modclaims) |Azure AD Connect 동기화 구성에 해당하는 AD FS의 클레임 규칙을 수정 또는 추가합니다. |

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
* [Azure에서 AD FS 배포](active-directory-aadconnect-azure-adfs.md)
* [Azure Traffic Manager를 사용하여 Azure에서 고가용성 교차 지리적 AD FS 배포](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

