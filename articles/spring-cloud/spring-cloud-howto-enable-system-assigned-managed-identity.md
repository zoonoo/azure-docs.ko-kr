---
title: Azure Spring Cloud 애플리케이션에 대해 시스템에서 할당한 관리 ID 사용
description: 응용 프로그램에 대해 시스템 할당 관리 id를 사용 하도록 설정 하는 방법입니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.openlocfilehash: 2aadc5806f0c5c610dc28b32039ca944af87ab13
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231729"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Azure 스프링 클라우드 응용 프로그램에 대해 시스템 할당 관리 id를 사용 하도록 설정 하는 방법
Azure 리소스에 대 한 관리 되는 id는 Azure 스프링 클라우드 응용 프로그램과 같은 Azure 리소스에 Azure Active Directory 자동으로 관리 되는 id를 제공 합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

이 문서에서는 Azure Portal 및 CLI (버전 0.2.4에서 사용 가능)를 사용 하 여 Azure 스프링 클라우드 앱에 대 한 시스템 할당 관리 id를 사용 하거나 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
Azure 리소스에 대 한 관리 id에 익숙하지 않은 경우 [개요 섹션](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)을 참조 하세요.
배포 된 Azure 스프링 클라우드 인스턴스가 필요 합니다. Azure CLI를 [사용 하 여 배포 하는 빠른 시작을](spring-cloud-quickstart-launch-app-cli.md)따르세요.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가
시스템 할당 id를 사용 하 여 앱을 만들려면 응용 프로그램에서 추가 속성을 설정 해야 합니다.

### <a name="using-azure-portal"></a>Azure Portal 사용
[Azure Portal](https://portal.azure.com/)에서 관리 id를 설정 하려면 먼저 앱을 만든 후 기능을 사용 하도록 설정 합니다.

1. 평소처럼 포털에서 앱을 만듭니다. 포털에서 해당 앱으로 이동합니다.
2. 왼쪽 탐색 창에서 **설정** 그룹까지 아래로 스크롤합니다.
3. **ID**를 선택합니다.
4. **시스템 할당** 탭에서 **상태**를 *켜기*로 바꿉니다. **저장**을 클릭합니다.

 ![포털에서 관리 되는 id](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Azure CLI 사용
앱을 만들거나 기존 앱에서 시스템 할당 관리 id를 사용 하도록 설정할 수 있습니다.

**앱을 만드는 동안 시스템 할당 관리 id 사용**

다음 예제에서는 매개 변수에 의해 요청 된 대로 시스템 할당 관리 id를 사용 하 여 *app_name* 이라는 앱을 만듭니다 `--assign-identity` .

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**기존 앱에서 시스템 할당 관리 Id 사용** `az spring-cloud app identity assign`명령을 사용 하 여 기존 앱에서 시스템이 할당 한 id를 사용 하도록 설정 합니다.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Azure 리소스 토큰 가져오기
앱은 관리 되는 id를 사용 하 여 Azure Key Vault와 같이 Azure Active Directory로 보호 되는 다른 리소스에 액세스 하는 토큰을 가져올 수 있습니다. 이러한 토큰은 응용 프로그램의 특정 사용자가 아닌 리소스에 액세스 하는 응용 프로그램을 나타냅니다.

[응용 프로그램에 대 한 액세스를 허용 하도록 대상 리소스를 구성](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal)해야 할 수도 있습니다. 예를 들어 Key Vault 액세스 하는 토큰을 요청 하는 경우 응용 프로그램의 id를 포함 하는 액세스 정책을 추가 했는지 확인 합니다. 그렇지 않으면 토큰이 포함되어 있더라도 Key Vault 호출이 거부됩니다. Azure Active Directory 토큰을 지원하는 리소스에 대한 자세한 내용은 [Azure AD 인증을 지원하는 Azure 서비스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)를 참조하세요.

Azure 스프링 클라우드는 Azure 가상 머신과 토큰 획득을 위해 동일한 끝점을 공유 합니다. Java SDK 또는 스프링 부팅 초보자를 사용 하 여 토큰을 얻는 것이 좋습니다.  토큰 만료 및 HTTP 오류 처리와 같은 중요 한 항목에 대해 다양 한 코드 및 스크립트 예제에 [VM 토큰을 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) 및 지침을 참조 하세요.

권장: Java SDK 또는 스프링 부팅 초보자를 사용 하 여 토큰을 가져옵니다.  [다음 단계](#next-steps)에서 샘플을 참조 하세요.

## <a name="disable-system-assigned-identity-from-an-app"></a>앱에서 시스템이 할당 한 id를 사용 하지 않도록 설정
시스템 할당 id를 제거 하면 Azure AD 에서도 삭제 됩니다. 앱 리소스를 삭제 하면 자동으로 Azure AD에서 시스템이 할당 된 id가 제거 됩니다.

### <a name="using-azure-portal"></a>Azure Portal 사용
더 이상 필요 하지 않은 시스템 할당 관리 id를 앱에서 제거 하려면 다음을 수행 합니다.

1. Azure 스프링 클라우드 인스턴스를 포함 하는 Azure 구독과 연결 된 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. 원하는 Virtual Machine으로 이동하여 **ID**를 선택합니다.
1. **시스템 할당** / **상태**에서 **끄기** 를 선택 하 고 **저장**을 클릭 합니다.

 ![포털에서 관리 되는 id](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Azure CLI 사용
시스템 할당 관리 id가 더 이상 필요 하지 않은 앱에서 제거 하려면 다음 명령을 사용 합니다.
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>다음 단계
* [Java SDK에서 관리 되는 id를 사용 하는 방법](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
* [스프링 부팅 스타터에서 관리 id를 사용 하 여 Azure Key Vault 액세스](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Azure 리소스에 대 한 관리 되는 id에 대 한 자세한 정보](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)

