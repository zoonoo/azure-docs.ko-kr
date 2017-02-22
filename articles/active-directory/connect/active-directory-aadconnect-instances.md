---
title: "Azure AD Connect: 서비스 인스턴스 동기화 | Microsoft Docs"
description: "이 페이지에서는 Azure AD 인스턴스에 대한 특별한 고려 사항을 문서화합니다."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: ce4d51c4ed38645ee81325404ff7214f845d5bf0
ms.openlocfilehash: 317f3be6cb39da7c7c57f7f368c750672609726e


---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: 인스턴스에 대한 특별한 고려 사항
Azure AD Connect는 Azure AD 및 Office 365의 전세계 인스턴스로 가장 많이 사용됩니다. 그러나 다른 인스턴스도 있고 URL 및 기타 특별한 고려 사항에 대한 다른 요구 사항을 포함합니다.

## <a name="microsoft-cloud-germany"></a>Microsoft 클라우드 독일
[Microsoft 클라우드 독일](http://www.microsoft.de/cloud-deutschland) 은 독일 데이터 트러스티에서 운영하는 독립 클라우드입니다.

| 프록시 서버에서 열린 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +인증서 해지 목록 |

Azure AD 테넌트에 로그인할 경우 onmicrosoft.de 도메인의 계정을 사용해야 합니다.

Microsoft 클라우드 독일에 현재 표시되지 않는 기능은 다음과 같습니다.

* **Azure AD Connect Health**를 사용할 수 없습니다.
* **자동 업데이트**를 사용할 수 없습니다.
* **비밀번호 쓰기 저장**을 사용할 수 없습니다.
* 다른 Azure AD Premium 서비스를 사용할 수 없습니다.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government 클라우드
[Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/) 는 미국 정부를 위한 클라우드입니다.

이 클라우드는 DirSync의 이전 버전에서 지원되어 왔습니다. Azure AD Connect의 1.1.180 빌드에서 차세대 클라우드가 지원됩니다. 이 차세대 클라우드는 미국 전용 기반 끝점을 사용하고 프록시 서버에서 열린 URL의 다른 목록이 있습니다.

| 프록시 서버에서 열린 URL |
| --- |
| \*.microsoftonline.com |
| \*.gov.us.microsoftonline.com |
| +인증서 해지 목록 |

Azure AD Connect는 Azure AD 테넌트가 Government 클라우드에 있음을 자동으로 검색할 수 없습니다. Azure AD Connect를 설치하는 경우 대신 다음 작업을 수행해야 합니다.

1. Azure AD Connect 설치를 시작합니다.
2. EULA를 허용하는 첫 번째 페이지가 표시되면 진행을 멈추고 설치 마법사가 실행되도록 합니다.
3. regedit을 시작하고 레지스트리 키 `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance`을 `2` 값으로 변경합니다.
4. Azure AD Connect 설치 마법사로 다시 이동하여 EULA에 동의하고 계속합니다. 설치하는 동안 (Express 설치가 아닌) **사용자 지정 구성** 설치 경로를 사용해야 합니다. 그런 다음 일반적인 설치를 계속합니다.

Microsoft Azure Government 클라우드에 현재 표시되지 않는 기능은 다음과 같습니다.

* **Azure AD Connect Health**를 사용할 수 없습니다.
* **자동 업데이트**를 사용할 수 없습니다.
* **비밀번호 쓰기 저장**을 사용할 수 없습니다.
* 다른 Azure AD Premium 서비스를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.



<!--HONumber=Jan17_HO5-->


