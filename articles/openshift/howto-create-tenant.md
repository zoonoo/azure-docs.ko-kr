---
title: Azure Red Hat 오픈시프트용 Azure AD 테넌트 만들기
description: Microsoft Azure Red Hat OpenShift 클러스터를 호스트할 Azure Active Directory(Azure AD) 테넌트를 만드는 방법은 다음과 같습니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243693"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure Red Hat 오픈시프트용 Azure AD 테넌트 만들기

Microsoft Azure Red Hat OpenShift에는 클러스터를 만들 [Azure Active Directory(Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 테넌트가 필요합니다. *테넌트는* 조직 또는 앱 개발자가 Azure, Microsoft Intune 또는 Microsoft 365에 가입하여 Microsoft와 관계를 만들 때 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 다른 Azure AD 테넌트와 구별되며 자체 작업 및 학교 ID 및 앱 등록을 가지고 있습니다.

Azure AD 테넌트가 아직 없는 경우 다음 지침을 따라 테넌트를 만듭니다.

## <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

테넌트를 만들려면 다음을 수행합니다.

1. Azure Red Hat OpenShift 클러스터와 연결하려는 계정을 사용하여 Azure [포털에](https://portal.azure.com/) 로그인합니다.
2. Azure [Active Directory 블레이드를](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) 열어 새 테넌트(새 *Azure Active Directory라고도*함)를 만듭니다.
3. 조직 **이름을**제공합니다.
4. 초기 **도메인 이름을**제공합니다. 이것은 *onmicrosoft.com* 추가됩니다. *여기에서 조직 이름에* 대한 값을 다시 사용할 수 있습니다.
5. 테넌트가 생성될 국가 또는 지역을 선택합니다.
6. **만들기**를 클릭합니다.
7. Azure AD 테넌트를 만든 후 **여기를 클릭하여 새 디렉터리 링크를 관리합니다.** 새 테넌트 이름은 Azure 포털의 오른쪽 상단에 표시되어야 합니다.  

    ![오른쪽 상단에 테넌트 이름을 표시하는 포털의 스크린샷][tenantcallout]  

8. 나중에 Azure Red Hat OpenShift 클러스터를 만들 위치를 지정할 수 있도록 *테넌트 ID를* 기록합니다. 이제 포털에서 새 테넌트에 대한 Azure Active Directory 개요 블레이드가 표시됩니다. **속성을** 선택하고 **디렉터리 ID의**값을 복사합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서와 같이 `TENANT` 이 값을 참조합니다.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>리소스

Azure [AD 테넌에](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)대한 자세한 내용은 [Azure Active Directory 설명서를](https://docs.microsoft.com/azure/active-directory/) 확인하십시오.

## <a name="next-steps"></a>다음 단계

서비스 주체를 만들고, 클라이언트 보안 및 인증 콜백 URL을 생성하고, Azure Red Hat OpenShift 클러스터에서 앱을 테스트하기 위한 새 Active Directory 사용자를 만드는 방법을 알아봅니다.

[Azure AD 앱 개체 및 사용자 만들기](howto-aad-app-configuration.md)
