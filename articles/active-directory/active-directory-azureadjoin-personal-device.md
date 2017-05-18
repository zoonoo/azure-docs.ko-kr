---
title: "개인 장치를 조직에 조인| Microsoft Docs"
description: "사용자가 자신의 개인 Windows 10 장치를 회사 네트워크에 등록하는 방법에 대해 설명하며 BYOD 시나리오에 대한 배포 단계를 제공합니다."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 9f3d38f5-1cfd-43d4-97da-4fed1255a1ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: bf9f0ae5876af6f9e92f86f0d49315ccb5ff06cf
ms.openlocfilehash: 5f25bee0d6cb4c6985a63073e19929f8d69de139
ms.contentlocale: ko-kr
ms.lasthandoff: 12/29/2016


---
# <a name="join-a-personal-device-to-your-organization"></a>개인 장치를 조직에 조인
## <a name="to-join-a-windows-10-device-to-your-organization"></a>Windows 10 장치를 조직에 조인하려면
1. **시작** 메뉴에서 **설정**을 선택합니다.
2. **계정**을 선택한 다음 **계정**을 클릭합니다.
3. **회사 또는 학교 계정 추가**를 클릭한 다음 조직 계정을 입력합니다.
4. 조직에 대한 로그인 페이지에서 사용자 이름 및 암호를 입력한 다음 **확인**을 클릭합니다.
5. 다중요소 인증 시도라는 메시지가 표시됩니다. (이 문제는 IT 관리자가 구성할 수 있습니다.)
6. Azure Active Directory(Azure AD)에서 장치를 모바일 장치 관리에 등록해야 하는지 여부를 확인합니다.
7. Windows에서 조직의 디렉터리에 있는 장치를 Azure AD에 등록하고 해당하는 경우 모바일 장치 관리에 등록합니다.
8. 관리되는 사용자인 경우 Windows에 자동 로그인되어 바탕 화면이 표시됩니다.
9. 페더레이션 사용자의 경우, Windows 로그인 화면으로 이동하고 자격 증명을 입력해야 합니다.

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)


