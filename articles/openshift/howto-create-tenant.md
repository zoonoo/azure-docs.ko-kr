---
title: Azure Red Hat OpenShift용 Azure AD 테넌트 만들기
description: Microsoft Azure Red Hat OpenShift 클러스터를 호스트하는 Azure AD(Azure Active Directory) 테넌트를 만드는 방법은 다음과 같습니다.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635012"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift용 Azure AD 테넌트 만들기

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월 30일부터 사용이 중지됩니다. 새로운 Azure Red Hat OpenShift 3.11 클러스터 만들기 지원은 2020년 11월 30일까지 계속됩니다. 사용 중지 후에는 보안 취약성을 방지하기 위해 남아 있는 Azure Red Hat OpenShift 3.11 클러스터가 종료됩니다.
> 
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 궁금한 점은 [문의하세요](mailto:arofeedback@microsoft.com).

Microsoft Azure Red Hat OpenShift에는 클러스터를 만들려는 [Azure AD(Azure Active Directory)](../active-directory/develop/quickstart-create-new-tenant.md) 테넌트가 필요합니다. *테넌트* 는 조직 또는 앱 개발자가 Microsoft와 관계를 맺었을 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 가입) 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 다른 Azure AD 테넌트와 구분되는 고유한 것으로, 회사 및 학교 ID와 앱 등록을 포함합니다.

Azure AD 테넌트가 없다면 다음 절차를 따라 만드세요.

## <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

테넌트를 만들려면 다음을 수행합니다.

1. Azure Red Hat OpenShift 클러스터와 연결하려는 계정을 사용해 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. [Azure Active Directory 블레이드](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)를 열어 새 테넌트(새 *Azure Active Directory* 라고도 함)를 만듭니다.
3. **조직 이름** 을 입력합니다.
4. **초기 도메인 이름** 을 입력합니다. 그러면 *onmicrosoft.com* 이 추가됩니다. 여기에서 *조직 이름* 의 값을 다시 사용할 수 있습니다.
5. 테넌트가 생성되는 국가 또는 지역을 선택합니다.
6. **만들기** 를 클릭합니다.
7. Azure AD 테넌트를 만든 후 **새 디렉터리를 관리하려면 여기를 클릭하세요** 링크를 선택합니다. Azure Portal의 오른쪽 위에 새 테넌트 이름이 표시됩니다.  

    ![오른쪽 위에 테넌트 이름을 표시하는 포털의 스크린샷][tenantcallout]  

8. 나중에 Azure Red Hat OpenShift 클러스터를 만들 위치를 지정할 수 있도록 *테넌트 ID* 를 기록해 둡니다. 이제 포털에 새 테넌트에 대한 Azure Active Directory 개요 블레이드가 표시됩니다. **속성** 을 선택하고 **디렉터리 ID** 의 값을 복사합니다. 이 값은 [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서 `TENANT`로 참조됩니다.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>리소스

[Azure AD 테넌트](../active-directory/develop/quickstart-create-new-tenant.md)에 대한 자세한 내용은 [Azure Active Directory 설명서](../active-directory/index.yml)를 참조하세요.

## <a name="next-steps"></a>다음 단계

서비스 주체를 만들고, 클라이언트 암호, 인증 콜백 URL을 생성하고, Azure Red Hat OpenShift 클러스터에서 앱을 테스트하기 위한 새 Active Directory 사용자를 만드는 방법에 대해 알아봅니다.

[Azure AD 앱 개체 및 사용자 만들기](howto-aad-app-configuration.md)