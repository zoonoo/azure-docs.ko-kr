---
title: Azure Red Hat OpenShift 실행 OpenShift 4-명령줄을 사용 하 여 Azure Active Directory 인증 구성
description: 명령줄을 사용 하 여 OpenShift 4를 실행 하는 Azure Red Hat OpenShift 클러스터에 대 한 Azure Active Directory 인증을 구성 하는 방법에 대해 알아봅니다.
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0e9e7c5fccf56077e138ffe24226344257bb3a13
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636291"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Azure Red Hat OpenShift 4 클러스터에 대 한 Azure Active Directory 인증 구성 (CLI)

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에서는 Azure CLI 버전 2.6.0 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

Azure Active Directory 응용 프로그램을 구성 하는 데 사용할 클러스터 관련 Url을 검색 합니다.

리소스 그룹 및 클러스터 이름에 대 한 변수를 설정 합니다.

을 **\<resource_group>** 리소스 그룹의 이름으로,을 **\<aro_cluster>** 클러스터의 이름으로 바꿉니다.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

클러스터의 OAuth 콜백 URL을 생성 하 고 **Oauthcallbackurl** 변수에 저장 합니다. 

> [!NOTE]
> `AAD`Oauth 콜백 URL의 섹션은 나중에 설정할 oauth id 공급자 이름과 일치 해야 합니다.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

OauthCallbackURL의 형식은 사용자 지정 도메인과 약간 다릅니다.

* 사용자 지정 도메인 (예:)을 사용 하는 경우 다음 명령을 실행 `contoso.com` 합니다. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* 사용자 지정 도메인을 사용 하지 않는 경우은 `$domain` 8 자의 alnum과 문자열 이며에 의해 확장 됩니다 `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> `AAD`Oauth 콜백 URL의 섹션은 나중에 설정할 oauth id 공급자 이름과 일치 해야 합니다.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>인증을 위한 Azure Active Directory 응용 프로그램 만들기

**\<client_secret>** 응용 프로그램에 대 한 보안 암호로 대체 합니다.

```azurecli-interactive
client_secret=<client_secret>
```

Azure Active Directory 응용 프로그램을 만들고 만든 응용 프로그램 식별자를 검색 합니다.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

응용 프로그램을 소유 하는 구독의 테 넌 트 ID를 검색 합니다.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>매니페스트 파일을 만들어 ID 토큰에 포함할 선택적 클레임을 정의 합니다.

응용 프로그램 개발자는 Azure AD 응용 프로그램에서 [선택적 클레임](../active-directory/develop/active-directory-optional-claims.md) 을 사용 하 여 응용 프로그램으로 전송 되는 토큰에서 원하는 클레임을 지정할 수 있습니다.

선택적 클레임을 사용하여 다음을 수행할 수 있습니다.

- 애플리케이션에 대한 토큰에 포함할 추가 클레임을 선택합니다.
- Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

`email` `upn` `upn` Azure Active Directory에서 반환 하는 ID 토큰의 일부로를 추가 하 여 클레임을 사용 하 고로 대체 하도록 openshift를 구성 합니다.

파일 **에manifest.js** 를 만들어 Azure Active Directory 응용 프로그램을 구성 합니다.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>매니페스트를 사용 하 여 Azure Active Directory 응용 프로그램의 optionalClaims 업데이트

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Azure Active Directory 응용 프로그램 범위 사용 권한 업데이트

Azure Active Directory에서 사용자 정보를 읽을 수 있으려면 적절 한 범위를 정의 해야 합니다.

Azure Active Directory에 대 한 권한을 추가 합니다 **. user. 사용자.** 범위를 사용 하 여 로그인을 사용 하도록 설정 하 고 사용자 프로필을 읽습니다.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> 이 Azure Active Directory에 대 한 전역 관리자로 인증 하지 않는 한 동의를 부여 하는 메시지는 무시 해도 안전 합니다. 표준 도메인 사용자는 AAD 자격 증명을 사용 하 여 처음으로 클러스터에 로그인 할 때 동의를 부여 하 라는 메시지가 표시 됩니다.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>클러스터에 사용자 및 그룹 할당 (선택 사항)

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다. Azure AD를 사용하면 테넌트 관리자 및 개발자는 앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한할 수 있습니다.

Azure Active Directory 설명서의 지침에 따라 [사용자 및 그룹을 앱에 할당](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)합니다.

## <a name="configure-openshift-openid-authentication"></a>OpenShift Openid connect 인증 구성

`kubeadmin`자격 증명을 검색 합니다. 다음 명령을 실행하여 `kubeadmin` 사용자의 암호를 찾습니다.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

다음 명령을 사용 하 여 OpenShift 클러스터의 API 서버에 로그인 합니다. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

OpenShift 암호를 만들어 Azure Active Directory 응용 프로그램 비밀을 저장 합니다.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Azure Active Directory에 대해 OpenShift Openid connect 인증을 구성 하는 **oidc** 파일을 만듭니다. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

클러스터에 구성을 적용 합니다.

```azurecli-interactive
oc apply -f oidc.yaml
```

다음과 유사한 응답을 받게 됩니다.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Azure Active Directory를 통해 로그인 확인

이제 OpenShift 웹 콘솔을 로그 아웃 하 고 다시 로그인을 시도 하면 **AAD** 를 사용 하 여 로그인 할 수 있는 새로운 옵션이 제공 됩니다. 몇 분 정도 기다려야 할 수 있습니다.

![Azure Active Directory 옵션이 있는 로그인 화면](media/aro4-login-2.png)
