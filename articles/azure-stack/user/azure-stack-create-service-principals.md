---
title: Azure Stack에 대 한 서비스 주체 만들기 | Microsoft Docs
description: 리소스에 대 한 액세스를 관리 하려면 Azure Resource Manager에서 역할 기반 액세스 제어를 사용 하 여 사용할 수 있는 서비스 주체를 만드는 방법을 설명 합니다.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 6ac5a44f941f8ebcecc3a1d7e6b5e9dccf5704de
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242215"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>응용 프로그램 리소스 액세스 권한을 부여 Azure Stack에서 서비스 주체 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Resource Manager를 사용 하는 주 서비스를 만들어 Azure Stack 리소스에 대 한 응용 프로그램 액세스를 제공할 수 있습니다. 서비스 주체를 사용 하면 사용 하 여 특정 권한 위임 [역할 기반 액세스 제어](azure-stack-manage-permissions.md)입니다.

모범 사례로 응용 프로그램에 대 한 서비스 주체를 사용 해야 합니다. 서비스 주체는 다음과 같은 이유로 사용자 고유의 자격 증명을 사용 하 여 앱을 실행 하는 것이 좋습니다.

* 서비스 계정 사용 권한과 다른 된 주체에 권한을 할당할 수 있습니다. 일반적으로 서비스 보안 주체 사용 권한 앱 사항을 정확히 수행 제한 됩니다.
* 사용자 역할 또는 책임 변경 되 면 앱의 자격 증명을 변경할 필요가 없습니다.
* 무인된 스크립트를 실행 하는 경우 인증을 자동화 하는 인증서를 사용할 수 있습니다.

## <a name="example-scenario"></a>예제 시나리오 

Azure Resource Manager를 사용 하 여 Azure 리소스를 인벤토리 하는 구성 관리 응용 프로그램을 해야 합니다. 서비스 주체를 만들고 및 읽기 권한자 역할에 할당할 수 있습니다. 이 역할 Azure 리소스에 앱 읽기 전용 액세스를 제공합니다.

## <a name="getting-started"></a>시작

에 대 한 지침으로이 문서의 단계를 사용 합니다.

* 앱에 대 한 서비스 주체를 만듭니다.
* 앱 등록 및 인증 키를 만듭니다.
* 역할에 앱을 할당 합니다.

Azure Stack에 대 한 Active Directory를 구성 하는 방법은 서비스 주체를 만드는 방법을 결정 합니다. 다음 옵션 중 하나를 선택 합니다.

* 서비스에 대 한 주체를 만들 [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad)합니다.
* 서비스에 대 한 주체를 만들 [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs)합니다.

단계에 할당 하기 위한 서비스 사용자 역할을 동일한 azure AD 및 AD FS 합니다. 서비스 주체를 만들 수 있습니다 [권한을 위임할](azure-stack-create-service-principals.md#assign-role-to-service-principal) 역할에 할당 하 여 합니다.

## <a name="create-service-principal-for-azure-ad"></a>Azure AD에 대 한 서비스 주체 만들기

Azure Stack에 Azure AD를 사용 하 여 id 저장소로, 하는 경우 서비스를 Azure portal을 사용 하 여 Azure에서와 같이 동일한 단계를 사용 하 여 주 만들 수 있습니다.

>[!NOTE]
있는지 확인 합니다 [필요한 Azure AD 권한](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) 서비스 주체 만들기를 시작 하기 전에 합니다.

### <a name="create-service-principal"></a>서비스 주체 만들기

응용 프로그램에 대 한 서비스 주체를 만들려면:

1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
2. 선택 **Azure Active Directory** > **앱 등록** > **추가**합니다.
3. 애플리케이션에 대한 이름 및 URL을 제공합니다. 만들려는 애플리케이션 유형으로 **웹앱/API** 또는 **네이티브**를 선택합니다. 값을 설정한 후 **만들기**를 선택합니다.

### <a name="get-credentials"></a>자격 증명 가져오기

프로그래밍 방식으로 로그인 할 때 응용 프로그램 및 인증 키에 대 한 ID를 사용 합니다. 이러한 값을 가져오려면 다음을 수행합니다.

1. Active Directory의 **앱 등록**에서 애플리케이션을 선택합니다.

2. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. 응용 프로그램에는 [샘플 응용 프로그램](#sample-applications) 사용 하 여 **클라이언트 id** 참조할 때 합니다 **응용 프로그램 ID**합니다.

     ![응용 프로그램에 대 한 응용 프로그램 ID](./media/azure-stack-create-service-principal/image12.png)
3. 인증 키를 생성하려면 **키**를 선택합니다.

4. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

>[!IMPORTANT]
키, 키를 저장 한 후 **값** 표시 됩니다. 나중에 키를 검색할 수 없습니다 때문에이 값을 적어둡니다. 애플리케이션에서 검색할 수 있는 위치에 키 값을 저장합니다.

![저장 된 키에 대 한 경고를 키 값입니다.](./media/azure-stack-create-service-principal/image15.png)

마지막 단계로 [응용 프로그램 역할 할당](azure-stack-create-service-principals.md#assign-role-to-service-principal)합니다.

## <a name="create-service-principal-for-ad-fs"></a>AD FS에 대 한 서비스 주체 만들기

AD FS를 사용 하 여 id 저장소로 Azure Stack을 배포한 경우에 다음 작업에 대 한 PowerShell을 사용할 수 있습니다.

* 서비스 주체를 만듭니다.
* 역할에 서비스 주체를 할당 합니다.
* 서비스 주체의 id를 사용 하 여 로그인 합니다.

서비스 주체를 만드는 방법에 대 한 세부 정보를 참조 하세요 [AD FS에 대 한 서비스 주체 만들기](../azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)합니다.

## <a name="assign-the-service-principal-to-a-role"></a>서비스 주체를 역할 할당

구독의 리소스에 액세스하려면 역할에 애플리케이션을 할당해야 합니다. 애플리케이션에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

>[!NOTE]
구독, 리소스 그룹 또는 리소스 수준에서 역할의 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어, 리소스 그룹에 대 한 읽기 권한자 역할을 사용 하 여 앱을 앱의 리소스 그룹에 리소스를 읽을 수 있습니다 의미 합니다.

서비스 주체에 역할 할당에 대 한 지침으로 다음 단계를 사용 합니다.

1. Azure Stack 포털에서 응용 프로그램을 할당 하려는 범위 수준으로 이동 합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다.

2. 응용 프로그램을 할당 하려면 구독을 선택 합니다. 이 예제에서는 구독이 Visual Studio Enterprise입니다.

     ![할당을 위한 Visual Studio Enterprise 구독을 선택 합니다.](./media/azure-stack-create-service-principal/image16.png)

3. 선택 **액세스 제어 (IAM)** 구독에 대 한 합니다.

4. **역할 할당 추가**를 선택합니다.

5. 애플리케이션에 할당할 역할을 선택합니다.

6. 애플리케이션을 검색하고 선택합니다.

7. **확인**을 선택하여 역할 할당을 완료합니다. 해당 범위에 대 한 역할에 할당 된 사용자 목록에서 응용 프로그램을 볼 수 있습니다.

이제는 서비스 주체를 생성 하 고 역할을 할당 하면, Azure Stack 리소스 응용 프로그램에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[사용자 권한 관리](azure-stack-manage-permissions.md)
