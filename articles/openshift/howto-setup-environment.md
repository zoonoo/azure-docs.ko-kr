---
title: Azure Red Hat OpenShift 개발 환경 설정 | Microsoft Docs
description: Microsoft Azure의 Red Hat OpenShift를 사용 하 여 작업에 대 한 필수 구성 요소는 다음과 같습니다.
services: openshift
keywords: red hat openshift 설정 설정
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: f0ef421d7954aa33cf69e7de2f4902a86ed8b580
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306396"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Azure Red Hat OpenShift 개발 환경 설정

을 빌드하고 Microsoft Azure의 Red Hat OpenShift 응용 프로그램을 실행 하려면를 해야 합니다.

* Azure 가상 머신 예약 인스턴스 구입 합니다.
* 2.0.65 버전 설치 (또는 이상)의 Azure CLI (Azure Cloud Shell을 사용 하 여).
* 등록 된 `openshiftmanagedcluster` 기능과 연결 된 리소스 공급자입니다.
* Azure Active Directory (Azure AD) 테 넌 트를 만듭니다.
* Azure AD 응용 프로그램 개체를 만듭니다.
* Azure AD 사용자를 만듭니다.

다음 지침에서는 이러한 필수 구성 요소의 모든 안내 합니다.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Azure Red Hat OpenShift 응용 프로그램 노드 예약된 인스턴스 구매

Azure Red Hat OpenShift를 사용 하려면 먼저 지나면 수 클러스터 프로 비전 하는 Azure Red Hat OpenShift 예약 응용 프로그램 노드 4 개를 구매 해야 합니다.

Azure 고객으로 라면 [Azure Red Hat OpenShift 예약 인스턴스 구매](https://aka.ms/openshift/buy) Azure portal을 통해. 구매 후 24 시간 내 구독 활성화 됩니다.

Azure는 고객이 아닌 경우 [영업 팀에 문의](https://aka.ms/openshift/contact-sales) 고 프로세스를 시작 하려면 페이지 맨 아래에 판매 양식을 작성 합니다.

참조를 [가격 책정 페이지 Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) 자세한 합니다.

## <a name="install-the-azure-cli"></a>Azure CLI 설치

Azure의 Red Hat OpenShift 필요 2.0.65 버전의 Azure CLI 이상. Azure CLI를 이미 설치한 경우에 실행 하 여 어떤 버전을 확인할 수 있습니다.

```bash
az --version
```

출력의 첫 번째 줄은 버전이 CLI, 예를 들어 `azure-cli (2.0.65)`합니다.

에 대 한 지침은 다음과 같습니다 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 새로 설치 또는 업그레이드 해야 하는 경우.

또는 사용할 수는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)합니다. Azure Cloud Shell을 사용 하는 경우 선택 해야 합니다 **Bash** 와 함께 수행 하려는 경우 환경을 [만들기 Azure Red Hat OpenShift 클러스터를 관리 하 고](tutorial-create-cluster.md) 자습서 시리즈입니다.

## <a name="register-providers-and-features"></a>공급자 기능 등록

합니다 `Microsoft.ContainerService openshiftmanagedcluster` 기능을 `Microsoft.Solutions`, 및 `Microsoft.Network` 공급자 첫 번째 Azure Red Hat OpenShift 클러스터를 배포 하기 전에 수동으로 구독을 등록 해야 합니다.

이러한 공급자와 기능을 수동으로 등록 하려면 Azure portal에서 CLI를 설치한 경우에 Bash 셸에서 또는 Azure Cloud Shell (Bash) 세션에서 다음 지침을 사용 합니다.

1. 여러 Azure 구독이 있는 경우 관련 구독 ID를 지정 합니다.

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Microsoft.ContainerService openshiftmanagedcluster 기능을 등록 합니다.

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

1. Microsoft.Storage 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Microsoft.Compute 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Microsoft.Solutions 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Microsoft.Network 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Microsoft.KeyVault 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Microsoft.ContainerService 리소스 공급자의 등록을 새로 고칩니다.

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure Active Directory (Azure AD) 테 넌 트 만들기

Azure Red Hat OpenShift를 microsoft 조직 및 해당 관계를 나타내는 연결 된 Azure Active Directory (Azure AD) 테 넌 필요 합니다. Azure AD 테 넌 트에 등록, 빌드 및 앱 관리와 다른 Azure 서비스를 사용 하 여 할 수 있습니다.

지침에 따라 Azure AD 테 넌 트 Azure Red Hat OpenShift 클러스터에 사용할 없는 경우 테스트에 대 한 테 넌 트를 만들려는 [는 Azure AD 테 넌 트 Azure Red Hat OpenShift 클러스터를 만들](howto-create-tenant.md) 하기 전에 이 가이드를 사용 하 여 계속 합니다.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Azure AD 사용자, 보안 그룹 및 응용 프로그램 만들기 개체

Azure의 Red Hat OpenShift에는 저장소를 구성 하는 등, 클러스터에 작업을 수행 하는 권한이 필요 합니다. 이러한 사용 권한을 통해 표현 됩니다는 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)합니다. Azure Red Hat OpenShift 클러스터에서 실행 되는 앱을 테스트 하는 것에 대 한 새 Active Directory 사용자를 만들기도 합니다.

지침을 따릅니다 [Azure AD 응용 프로그램 개체 및 사용자 만들기](howto-aad-app-configuration.md) 서비스 주체를 만들려면 앱에 대 한 클라이언트 암호 및 인증 콜백 URL을 생성 하 고 새 액세스 하기 위해 Azure AD 보안 그룹 및 사용자는 클러스터입니다.

## <a name="next-steps"></a>다음 단계

이제 Azure의 Red Hat OpenShift를 사용할 준비가 되었습니다!

이 자습서를 시도해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
