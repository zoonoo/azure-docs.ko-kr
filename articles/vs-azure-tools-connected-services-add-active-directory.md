---
title: "Visual Studio에서 연결된 서비스를 사용하여 Azure Active Directory 추가 | Microsoft Docs"
description: "Visual Studio 연결된 서비스 추가 대화 상자를 사용하여 Azure Active Directory 추가"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5ff7c4ee612fd1261d85870be4d6fcbd66b64735
ms.lasthandoff: 03/27/2017


---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Visual Studio에서 연결된 서비스를 사용하여 Azure Active Directory 추가
Azure Active Directory(Azure AD)를 사용하여 ASP.NET MVC 웹 응용 프로그램 또는 웹 API 서비스에서 Active Directory 인증을 위한 SSO(Single Sign-on)을 지원할 수 있습니다. Azure Active Directory 인증을 사용하면 사용자가 Azure Active Directory의 자신의 계정을 사용하여 웹 응용 프로그램에 연결할 수 있습니다. Azure Active Directory 인증의 장점으로 웹 응용 프로그램에서 API를 표시하는 경우 향상된 데이터 보안을 들 수 있습니다. Azure AD를 사용하면, 자체 계정 및 사용자 관리를 사용하는 별도 인증 시스템을 관리할 필요가 없습니다.

## <a name="prerequisites"></a>필수 조건
- Azure 계정 - Azure 계정이 없는 경우 [평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>연결된 서비스 대화 상자를 사용하여 Azure Active Directory에 연결
1. Visual Studio에서 ASP.NET MVC 프로젝트 또는 ASP.NET Web API 프로젝트를 만들거나 엽니다.

1. 솔루션 탐색기에서 **연결된 서비스** 노드를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **연결된 서비스 추가**를 선택합니다.

1. **연결된 서비스** 페이지에서 **Azure Active Directory로 인증**을 선택합니다.
   
    ![연결된 서비스 페이지](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. **Azure AD 인증 구성** 마법사의 **소개** 페이지에서 **다음**을 선택합니다.
   
    ![소개 페이지](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. **Azure AD 인증 구성** 마법사의 **Single-Sign On** 페이지에서 **도메인** 드롭다운 목록에 있는 도메인을 선택합니다. 도메인 목록에는 계정 설정 대화 상자에 나열된 계정으로 액세스할 수 있는 모든 도메인을 포함합니다. 대신, 도메인을 찾을 수 없는 경우 `mydomain.onmicrosoft.com` 등과 같은 도메인 이름을 입력할 수 있습니다. Azure Active Directory 앱을 만들거나 기존 Azure Active Directory 앱의 설정을 사용하는 옵션을 선택할 수 있습니다. 완료되면 **다음**을 선택합니다.
   
    ![Single Sign-On 페이지](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. **Azure AD 인증 구성** 마법사의 **디렉터리 액세스** 페이지에서 **디렉터리 데이터 읽기** 옵션이 선택되어 있는지 확인합니다. 
   
    ![디렉터리 액세스 페이지](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. **마침**을 선택하여 Azure AD 인증을 위한 프로젝트를 사용 가능하도록 필요한 구성 코드 및 참조를 추가합니다. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 Active Directory 도메인을 볼 수 있습니다.

1. Visual Studio에는 프로젝트가 수정된 방식을 보여 주는 [변경된 내용](#how-your-project-is-modified) 문서가 표시됩니다. 모든 항목이 제대로 작동하는지 확인하려면 수정된 구성 파일 중 하나를 열고 이 문서에 언급된 설정이 있는지 확인합니다. 

## <a name="how-your-project-is-modified"></a>프로젝트를 수정하는 방법
마법사를 실행하면 Visual Studio는 Azure Active Directory 및 연관된 참조를 프로젝트에 추가합니다. 또한 프로젝트의 구성 파일 및 코드 파일이 수정되어 Azure AD용 지원을 추가합니다. Visual Studio가 작성한 특정 수정 내용은 프로젝트 형식에 따라 달라집니다. ASP.NET MVC 프로젝트를 수정하는 방법에 대한 자세한 정보는 [변경된 내용 – MVC 프로젝트](http://go.microsoft.com/fwlink/p/?LinkID=513809)를 참조하세요. 웹 API 프로젝트는 [변경된 내용 - 웹 API 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513810)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory에 대한 MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Azure Active Directory 설명서](https://azure.microsoft.com/documentation/services/active-directory/)
* [블로그 게시물: Azure Active Directory에 대한 소개](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)


