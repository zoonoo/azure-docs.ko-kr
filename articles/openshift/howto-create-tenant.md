---
title: Azure Red Hat OpenShift는 Azure AD 테 넌 트 만들기 | Microsoft Docs
description: Microsoft Azure의 Red Hat OpenShift 클러스터를 호스트 하는 Azure Active Directory (Azure AD) 테 넌 트를 만드는 방법은 다음과 같습니다.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306457"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift는 Azure AD 테 넌 트 만들기

Microsoft Azure의 Red Hat OpenShift 필요는 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 클러스터를 만들 테 넌 트입니다. A *테 넌 트* 조직 또는 응용 프로그램 개발자는 Microsoft와 Microsoft 365, Azure 및 Microsoft Intune에 등록 하 여 관계를 만들 때 수신 하는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테 넌 트를 고유 하 고 다른 Azure AD에서 별도 테 넌 트 권한이 고유한 회사 및 학교 id 및 앱 등록 합니다.

Azure AD 테 넌 트에 아직 없는 경우 새로 만들려면 다음이 지침을 따릅니다.

## <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

테 넌 트를 만들려면:

1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/) Azure Red Hat OpenShift 클러스터를 사용 하 여 연결 하려는 계정을 사용 하 여 합니다.
2. 엽니다는 [Azure Active Directory 블레이드](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) 새 테 넌 트를 만들려면 (새 라고도 *Azure Active Directory*).
3. 제공 된 **조직 이름**합니다.
4. 제공 된 **초기 도메인 이름**합니다. 이것은 아무런 *onmicrosoft.com* 추가 합니다. 에 대 한 값을 다시 사용할 수 있습니다 *조직 이름* 여기 있습니다.
5. 국가 / 테 넌 트를 만들 지역을 선택 합니다.
6. **만들기**를 클릭합니다.
7. Azure AD 테 넌 트를 만든 후 선택 합니다 **새 디렉터리를 관리 하려면 여기를 클릭** 링크 합니다. 새 테 넌 트 이름은 Azure portal의 오른쪽 상단에에서 표시 되어야 합니다.  

    ![오른쪽 상단에서에서 테 넌 트 이름을 보여 주는 포털 스크린샷][tenantcallout]  

8. 기록해는 *테 넌 트 ID* 나중에 Azure Red Hat OpenShift 클러스터를 만들 수 있는 위치를 지정할 수 있습니다. 포털에서 이제 새 테 넌 트에 대 한 Azure Active Directory 개요 블레이드를 표시 됩니다. 선택 **속성** 에 대 한 값을 복사 하 **디렉터리 ID**합니다. 이 값으로 이라고 `TENANT` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>리소스

체크 아웃 [Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/) 대 한 자세한 내용은 [Azure AD 테 넌 트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)합니다.

## <a name="next-steps"></a>다음 단계

서비스 주체 만들기, 클라이언트 암호 및 인증 콜백 URL을 생성 및 Azure Red Hat OpenShift 클러스터에서 앱을 테스트 하는 것에 대 한 새 Active Directory 사용자를 만드는 방법에 알아봅니다.

[Azure AD 응용 프로그램 개체 및 사용자 만들기](howto-aad-app-configuration.md)