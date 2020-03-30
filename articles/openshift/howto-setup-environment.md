---
title: Azure Red Hat 오픈시프트 개발 환경 설정
description: 다음은 Microsoft Azure Red Hat OpenShift와 함께 작업하기 위한 필수 구성 조건입니다.
keywords: 레드 햇 오픈 시프트 설정 설정
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477037"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Azure Red Hat OpenShift 개발 환경 설정

Microsoft Azure Red Hat OpenShift 응용 프로그램을 빌드하고 실행하려면 다음을 수행해야 합니다.

* Azure CLI의 버전 2.0.65(또는 그 이상)를 설치합니다(또는 Azure 클라우드 셸 사용).
* 기능 및 `AROGA` 관련 리소스 공급자에 등록합니다.
* Azure Active 디렉터리(Azure AD) 테넌트를 만듭니다.
* Azure AD 응용 프로그램 개체를 만듭니다.
* Azure AD 사용자를 만듭니다.

다음 지침은 이러한 모든 전제 조건을 안내합니다.

## <a name="install-the-azure-cli"></a>Azure CLI 설치

Azure Red Hat OpenShift버전 2.0.65 이상Azure CLI가 필요합니다. Azure CLI를 이미 설치한 경우 실행하여 사용할 버전을 확인할 수 있습니다.

```azurecli
az --version
```

출력의 첫 번째 줄에는 CLI 버전이 있습니다. `azure-cli (2.0.65)`

새 설치 또는 업그레이드가 필요한 경우 [Azure CLI를 설치하기](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 위한 지침은 다음과 같습니다.

또는 [Azure 클라우드 셸을](https://docs.microsoft.com/azure/cloud-shell/overview)사용할 수 있습니다. Azure 클라우드 셸을 사용하는 경우 [Azure Red Hat OpenShift 클러스터](tutorial-create-cluster.md) 자습서 시리즈 만들기 및 관리와 함께 수행하려는 경우 **Bash** 환경을 선택해야 합니다.

## <a name="register-providers-and-features"></a>공급자 및 기능 등록

기능, `Microsoft.Solutions`, `Microsoft.Compute` `Microsoft.Storage`및 `Microsoft.KeyVault` `Microsoft.Network` 공급자는 `Microsoft.ContainerService AROGA` 첫 번째 Azure Red Hat OpenShift 클러스터를 배포하기 전에 구독에 수동으로 등록해야 합니다.

이러한 공급자 및 기능을 수동으로 등록하려면 CLI를 설치한 경우 Bash 셸의 다음 지침을 사용하거나 Azure 포털의 Azure Cloud Shell(Bash) 세션에서 다음 지침을 사용합니다.

1. Azure 구독이 여러 개인 경우 관련 구독 ID를 지정합니다.

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Microsoft.ContainerService AROGA 기능을 등록하십시오.

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Microsoft.Storage 공급자등록:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Microsoft.Compute 공급자등록:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Microsoft.Solutions 공급자등록:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Microsoft.Network 공급자등록:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Microsoft.KeyVault 공급자를 등록하십시오.

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Microsoft.ContainerService 리소스 공급자의 등록을 새로 고칩니다.

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure Active 디렉터리(Azure AD) 테넌트 만들기

Azure Red Hat OpenShift 서비스에는 조직및 Microsoft와의 관계를 나타내는 연결된 Azure Active Directory(Azure AD) 테넌트가 필요합니다. Azure AD 테넌트를 사용하면 앱을 등록, 빌드 및 관리하고 다른 Azure 서비스를 사용할 수 있습니다.

Azure Red Hat OpenShift 클러스터의 테넌트로 사용할 Azure AD가 없거나 테스트를 위해 테넌트를 만들려는 경우 이 가이드를 계속하기 전에 [Azure Red Hat OpenShift 클러스터에 대한 Azure AD 테넌트 만들기의](howto-create-tenant.md) 지침을 따릅니다.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Azure AD 사용자, 보안 그룹 및 응용 프로그램 개체 만들기

Azure Red Hat OpenShift에는 저장소 구성과 같은 클러스터에서 작업을 수행할 수 있는 권한이 필요합니다. 이러한 권한은 서비스 [주체를](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)통해 표시됩니다. 또한 Azure Red Hat OpenShift 클러스터에서 실행 중인 앱을 테스트하기 위해 새 Active Directory 사용자를 만들어야 합니다.

[Azure AD 앱 개체 및 사용자 만들기의](howto-aad-app-configuration.md) 지침에 따라 서비스 주체를 만들고, 앱에 대한 클라이언트 보안 및 인증 콜백 URL을 생성하고, 새 Azure AD 보안 그룹 및 사용자를 만들어 클러스터에 액세스합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Red Hat 오픈시프트를 사용할 준비가 되었습니다!

튜토리얼을 보십시오 :
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
