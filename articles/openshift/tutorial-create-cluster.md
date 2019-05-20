---
title: 자습서 - Azure Red Hat OpenShift 클러스터 만들기 | Microsoft Docs
description: Azure CLI를 사용하여 Microsoft Azure Red Hat OpenShift 클러스터를 만드는 방법 알아보기
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/08/2019
ms.openlocfilehash: baada8a5238725456ca4a2ec7e8257c229066115
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466187"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>자습서: Azure Red Hat OpenShift 클러스터 만들기

이 자습서는 시리즈의 1부입니다. Azure CLI를 사용하여 Microsoft Azure의 Red Hat OpenShift 클러스터를 만들고, 크기를 조정한 다음, 삭제하여 리소스를 정리하는 방법을 알아봅니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 만들기

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 만들기
> * [Azure Red Hat OpenShift 클러스터 크기 조정](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift 클러스터 삭제](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

다음을 포함하여 [개발 환경이 설정되어 있는지 확인](howto-setup-environment.md)합니다.
- 최신 CLI 설치(2.0.64 이상 버전)
- 테넌트 만들기
- Azure 애플리케이션 개체 만들기
- 클러스터에서 실행되는 앱에 로그인하는 데 사용되는 Active Directory 사용자 만들기

## <a name="step-1-sign-in-to-azure"></a>1단계: Azure에 로그인

Azure CLI를 로컬로 실행하는 경우 Bash 명령 셸을 열고 `az login`을 실행하여 Azure에 로그인합니다.

```bash
az login
```

 여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>2단계: Azure Red Hat OpenShift 클러스터 만들기

Bash 명령 창에서 다음 변수를 설정합니다.

> [!IMPORTANT]
> 클러스터 이름은 모두 소문자여야 합니다. 그렇지 않으면 클러스터 만들기가 실패합니다.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 [새 앱 등록 만들기](howto-aad-app-configuration.md#create-a-new-app-registration)의 6단계에서 선택한 것과 동일한 클러스터 이름을 사용합니다.

```bash
LOCATION=<location>
```

클러스터를 만들 위치를 선택합니다. Azure에서 OpenShift를 지원하는 Azure 지역 목록은 [지원되는 지역](supported-resources.md#azure-regions)을 참조하세요. 예: `LOCATION=eastus`

`FQDN`을 클러스터의 정규화된 이름으로 설정합니다. 이 이름은 클러스터 이름, 위치 그리고 끝부분에 추가되는 `.cloudapp.azure.com`으로 구성됩니다. [새 앱 등록 만들기](howto-aad-app-configuration.md#create-a-new-app-registration)의 6단계에서 만든 로그온 URL과 동일합니다. 예:   

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

`APPID`를 [새 앱 등록 만들기](howto-aad-app-configuration.md#create-a-new-app-registration)의 9단계에서 저장한 값으로 설정합니다.  

```bash
APPID=<app ID value>
```

`SECRET`를 [클라이언트 비밀 만들기](howto-aad-app-configuration.md#create-a-client-secret)의 6단계에서 저장한 값으로 설정합니다.  

```bash
SECRET=<secret value>
```

`TENANT`를 [새 테넌트 만들기](howto-create-tenant.md#create-a-new-azure-ad-tenant)의 7단계에서 저장한 테넌트 ID 값으로 설정합니다.  

```bash
TENANT=<tenant ID>
```

클러스터용 리소스 그룹을 만듭니다. 위에서 변수를 정의할 때 사용한 Bash 셸에서 다음 명령을 실행합니다.

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>선택 사항: 클러스터의 가상 네트워크를 기존 가상 네트워크에 연결

지금 만들고 있는 클러스터의 가상 네트워크(VNET)를 피어링을 통해 기존 VNET에 연결할 필요가 없으면 이 단계를 건너뜁니다.

먼저, 기존 VNET의 식별자를 가져옵니다. 식별자는 `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}` 형식입니다.

기존 VNET이 속한 네트워크 이름 또는 리소스 그룹을 모르는 경우 [가상 네트워크 블레이드](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks)로 이동하여 해당하는 가상 네트워크를 클릭합니다. 가상 네트워크 페이지가 열리고 가상 네트워크가 속한 네트워크 이름 및 리소스 그룹이 나열됩니다.

BASH 셸에서 다음 CLI 명령을 사용하여 VNET_ID 변수를 정의합니다.

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

예: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>클러스터 만들기

이제 클러스터를 만들 준비가 되었습니다.

 클러스터의 가상 네트워크를 기존 가상 네트워크에 연결할 필요가 없으면 다음 예제에서 후행 `--vnet-peer-id $VNET_ID` 매개 변수를 생략합니다.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

몇 분 후 `az openshift create` 명령이 성공적으로 완료되고 클러스터 세부 정보가 포함된 JSON 응답이 반환됩니다.

> [!NOTE]
> 클러스터 이름이 고유하지 않으면 호스트 이름을 사용할 수 없다는 오류가 발생할 수 있습니다. 원래 앱 등록을 삭제하고, 다른 클러스터 이름을 사용하여 [새 앱 등록 만들기](howto-aad-app-configuration.md#create-a-new-app-registration)의 단계를 다시 실행해보세요. 사용자를 이미 만들었으므로 마지막 단계인 새 사용자 만들기는 생략하세요.

## <a name="step-3-sign-in-to-the-openshift-console"></a>3단계: OpenShift 콘솔에 로그인

이제 새 클러스터의 OpenShift 콘솔에 로그인할 준비가 되었습니다. [OpenShift 웹 콘솔](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html)에서는 OpenShift 프로젝트의 콘텐츠를 시각화, 탐색 및 관리할 수 있습니다.

테스트용으로 만든 [새 Azure AD 사용자](howto-aad-app-configuration.md#create-a-new-active-directory-user)로 로그인하겠습니다. 이렇게 하려면 평소에 Azure Portal에 로그인할 때 사용하는 ID를 캐시하지 않은 새 브라우저 인스턴스가 필요합니다.

1. *incognito* 창(Chrome) 또는 *InPrivate* 창(Microsoft Edge)을 엽니다.
2. [새 앱 등록 만들기](howto-aad-app-configuration.md#create-a-new-app-registration)의 6단계에서 만든 로그온 URL로 이동합니다. 예를 들어 https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> OpenShift 콘솔은 자체 서명된 인증서를 사용합니다.
> 브라우저에서 메시지가 표시되면 경고를 무시하고 "신뢰할 수 없는" 인증서를 수락합니다.

[새 Active Directory 사용자 만들기](howto-aad-app-configuration.md#create-a-new-active-directory-user)에서 만든 사용자 이름 및 암호로 로그인합니다. **요청된 권한** 대화 상자가 나타나면 **조직 대신 동의**를 선택한 다음, **동의**를 선택합니다.

이제 클러스터 콘솔에 로그인되었습니다.

![OpenShift 클러스터 콘솔의 스크린샷](./media/aro-console.png)

 [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) 설명서에서 [OpenShift 콘솔을 사용](https://docs.openshift.com/aro/getting_started/developers_console.html)하여 이미지를 만들고 빌드하는 방법을 자세히 알아보세요.

## <a name="step-4-install-the-openshift-cli"></a>4단계: OpenShift CLI 설치

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html)(또는 *OC 도구*)는 OpenShift 클러스터의 다양한 구성 요소와 상호 작용하는 데 사용되는 애플리케이션 및 하위 수준 유틸리티를 관리할 수 있는 명령을 제공합니다.

OpenShift 콘솔의 오른쪽 위 모서리에서 로그인 이름 옆에 있는 물음표를 클릭하고 **명령줄 도구**를 선택합니다.  **최신 릴리스** 링크를 따라 이동하여 Linux, MacOS 또는 Windows를 지원하는 oc CLI를 다운로드 및 설치합니다.

> [!NOTE]
> 오른쪽 위 모서리에 물음표가 보이지 않으면 왼쪽 위 드롭다운 목록에서 *서비스 카탈로그* 또는 *애플리케이션 콘솔*을 선택합니다.
>
> 또는 직접 [oc CLI를 다운로드](https://www.okd.io/download.html)할 수도 있습니다.

**명령줄 도구** 페이지에서는 `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` 형식의 명령을 제공합니다.  *클립보드에 복사* 단추를 클릭하면 이 명령이 복사됩니다.  터미널 창에서, 로컬로 설치한 oc 도구가 포함되도록 [경로를 설정](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli)합니다. 그런 다음, 복사한 oc CLI 명령을 사용하여 클러스터에 로그인합니다.

위의 단계를 사용하여 토큰 값을 가져올 수 없으면 `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request` 명령으로 토큰 값을 가져옵니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Red Hat OpenShift 클러스터 만들기

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 크기 조정](tutorial-scale-cluster.md)