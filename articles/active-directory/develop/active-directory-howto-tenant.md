---
title: "Azure AD 테넌트를 얻는 방법 | Microsoft Docs"
description: "응용 프로그램 등록 및 구축을 위해 Azure Active Directory 테넌트를 얻는 방법입니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 85783d58b2b02a9d0c6230429bebf2806514dee5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Azure Active Directory 테넌트를 얻는 방법
Azure Active Directory (Azure AD)에서 [테넌트](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) 는 조직을 나타냅니다.  조직이 Azure, Microsoft InTune 또는 Office 365와 같은 Microsoft 클라우드 서비스에 등록할 때 받아서 소유하는 Azure AD 서비스의 전용 인스턴스입니다.  각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다.  

테넌트는 회사에 사용자 및 암호, 사용자 프로필 데이터, 사용 권한 등에 대한 사용자의 정보를 저장합니다.  또한 그룹, 응용 프로그램, 조직 및 보안에 관련된 기타 정보도 포함합니다.

Azure AD 사용자가 응용 프로그램에 로그인하도록 허용하려면 자신의 테넌트에 응용 프로그램을 등록해야 합니다.  Azure AD 테넌트에 응용 프로그램을 게시하는 것은 **무료**입니다.  사실 대부분의 개발자는 실험, 개발, 준비 및 테스트 목적으로 여러 개의 테넌트와 응용 프로그램을 만듭니다.  응용 프로그램에 등록하고 사용하는 조직에서는 고급 디렉터리 기능을 활용하려는 경우 라이선스를 구입할지 여부를 선택할 수 있습니다.

그럼 Azure AD 테넌트 가져오기를 시작해볼까요?  다음과 같이 경우에 따라 프로세스가 약간 다릅니다.

* [기존 Office 365 구독이 있는 경우](#use-an-existing-office-365-subscription)
* [Microsoft 계정에 연결된 기존 Azure 구독이 있는 경우](#use-an-msa-azure-subscription)
* [조직 계정에 연결된 기존 Azure 구독이 있는 경우](#use-an-organizational-azure-subscription)
* [위의 경우가 하나도 해당되지 않고 처음부터 시작하려는 경우](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>기존 Office 365 구독 사용
기존 Office 365 구독이 있는 경우 이미 Azure AD 테넌트가 있습니다. O365 계정으로 [Azure Portal](https://portal.azure.com)에 로그인하고 Azure AD를 사용하기 시작할 수 있습니다.

## <a name="use-an-msa-azure-subscription"></a>MSA Azure 구독 사용
이전에 개인 Microsoft 계정으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다.  [Azure Portal](https://portal.azure.com)에 로그인할 때 기본 테넌트에 자동으로 로그인됩니다. 필요에 따라 이 테넌트를 무료로 사용할 수 있으나 조직 관리자 계정을 만들 수도 있습니다.

이렇게 하려면 다음 단계를 따르세요.  또는 새 테넌트를 만들고 유사한 프로세스 후에 이 테넌트에 관리자를 만들 수도 있습니다.

1. 개인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 "Azure Active Directory" 섹션으로 이동합니다(왼쪽 탐색 모음의 **모든 서비스**에 있음).
3. 자동으로 "기본 디렉터리"에 로그인되어야 합니다. 그렇지 않은 경우 오른쪽 위 모퉁이에서 계정 이름을 클릭하여 디렉터리를 전환할 수 있습니다.
4. **빠른 태스크** 섹션에서 **사용자 추가**를 선택합니다.
5. [사용자 추가 양식]에 다음 세부 정보를 입력합니다.

   * 이름: (적절한 값 선택)
   * 사용자 이름: (이 관리자의 사용자 이름 선택)
   * 프로필: (이름, 성, 직위 및 부서에 대한 적절한 값 입력)
   * 역할: 전역 관리자 
6. 사용자 추가 양식을 완료하고 새 관리자의 임시 암호를 받으면, 암호를 변경하려는 경우 새 사용자로 로그인해야 하므로 암호를 기록해두세요. 또한 대체 메일을 사용하여 사용자에게 직접 암호를 보낼 수도 있습니다.
7. **만들기**를 클릭하여 새 사용자를 만듭니다.
8. 임시 암호를 변경하려면 이 새 사용자 계정으로 [https://login.microsoftonline.com](https://login.microsoftonline.com)에 로그인하고 요청된 경우 암호를 변경합니다.

## <a name="use-an-organizational-azure-subscription"></a>조직 Azure 구독 사용
이전에 조직 계정으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다.  [Azure Portal](https://portal.azure.com)에서 "모든 서비스" 및 "Azure Active Directory"로 이동할 때 테넌트를 찾아야 합니다.  필요에 따라 이 테넌트를 무료로 사용할 수 있습니다.

## <a name="start-from-scratch"></a>처음부터 시작
위의 모든 항목을 이해할 수 없어도 걱정하지 마세요. 새로운 Azure AD 디렉터리를 만들려면 [Azure Portal](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)에 방문하세요. 프로세스가 완료되면 등록할 때 선택한 도메인 이름으로 자신만의 Azure AD 테넌트를 얻게 됩니다.  [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 모음에서 **Azure Active Directory**로 이동하여 테넌트를 찾을 수 있습니다.
