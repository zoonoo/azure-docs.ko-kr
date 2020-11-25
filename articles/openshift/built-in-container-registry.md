---
title: Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성
description: Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 0a6449e6b728ee690dd6ddee192868aaeb3511ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95911042"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성

Azure Red Hat OpenShift는 새 이미지 리포지토리를 요청 시 자동으로 프로 비전 하는 기능을 추가 하는 [openshift Container Registry (OCR)](https://docs.openshift.com/aro/4/registry/architecture-component-imageregistry.html) 라는 통합 컨테이너 이미지 레지스트리를 제공 합니다. 그러면 사용자가 응용 프로그램 빌드에 대 한 기본 제공 위치를 제공 하 여 결과 이미지를 푸시할 수 있습니다.

이 문서에서는 Azure Red Hat OpenShift (ARO) 4 클러스터에 대 한 기본 제공 컨테이너 이미지 레지스트리를 구성 합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure AD 설정
> * Openid connect Connect 설정
> * 기본 제공 컨테이너 이미지 레지스트리 액세스

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 ARO 클러스터가 있다고 가정 합니다. ARO 클러스터가 필요한 경우 ARO 자습서 인 [Azure Red Hat OpenShift 4 클러스터 만들기](/azure/openshift/tutorial-create-cluster)를 참조 하세요. 에 대 한 인수를 사용 하 여 클러스터를 만들어야 `--pull-secret` `az aro create` 합니다.  이는 Azure Active Directory 인증 및 기본 제공 컨테이너 레지스트리를 구성 하는 데 필요 합니다.

클러스터가 있으면 [Azure Red Hat OpenShift 4 클러스터에 연결](/azure/openshift/tutorial-connect-cluster)의 단계를 수행 하 여 클러스터에 연결 합니다.
   * 이 문서의 뒷부분에 나오는 명령을 사용 하기 때문에 "OpenShift CLI 설치"의 단계를 수행 해야 `oc` 합니다.
   * 클러스터 콘솔 URL (예:)을 기록해 둡니다 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . 및의 값 `<random>` 은 `<region>` 이 문서의 뒷부분에서 사용 됩니다.
   * `kubeadmin`자격 증명을 확인 합니다. 또한이 문서의 뒷부분에서 사용 됩니다.

### <a name="configure-azure-active-directory-authentication"></a>Azure Active Directory 인증 구성 

Azure AD (Azure Active Directory)는 OIDC (Openid connect Connect)를 구현 합니다. OIDC를 사용 하면 Azure AD를 사용 하 여 ARO 클러스터에 로그인 할 수 있습니다. [Azure Active Directory 인증 구성](configure-azure-ad-cli.md) 의 단계에 따라 클러스터를 설정 합니다.

## <a name="access-the-built-in-container-image-registry"></a>기본 제공 컨테이너 이미지 레지스트리 액세스

이제 ARO 클러스터에 대 한 인증 방법을 설정 했으므로 기본 제공 레지스트리에 액세스할 수 있습니다.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>관리자가 될 Azure AD 사용자 정의

1. Azure AD 사용자의 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 합니다. Openid connect를 사용 하 여 관리자를 정의 하기 위해 Azure Active Directory에 대해 OpenShift Openid connect 인증을 활용 합니다.

   1. InPrivate, Incognito 또는 기타 해당 브라우저 창 기능을 사용 하 여 콘솔에 로그인 합니다. 이 창은 OIDC를 사용 하도록 설정한 후 다르게 보입니다.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Openid connect Connect 활성화 된 로그인 창":::
   1. **Openid connect** 선택

   > [!NOTE]
   > 여기에서 로그인 하는 데 사용 하는 사용자 이름 및 암호를 기록해 둡니다. 이 사용자 이름 및 암호는이 문서 및 다른 문서에서 다른 작업의 관리자 역할을 합니다.
2. 다음 단계를 사용 하 여 OpenShift CLI로 로그인 합니다.  논의를 위해이 프로세스를 라고 `oc login` 합니다.
   1. 웹 콘솔의 맨 오른쪽에서 로그인 한 사용자의 상황에 맞는 메뉴를 확장 하 고 **로그인 명령 복사** 를 선택 합니다.
   2. 필요한 경우 동일한 사용자를 사용 하 여 새 탭 창에 로그인 합니다.
   3. **토큰 표시** 를 선택 합니다.
   4. 아래와 같이 **이 토큰을 사용 하 여 로그인** 아래 나열 된 값을 클립보드에 복사 하 고 셸에서 실행 합니다.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. `oc whoami`콘솔에서를 실행 하 고 출력을로 기록 합니다 **\<aad-user>** .  이 값은이 문서의 뒷부분에서 사용 합니다.
4. OpenShift 웹 콘솔에서 로그 아웃 합니다. 로 레이블이 지정 된 브라우저 창의 오른쪽 위에 있는 단추를 선택 **\<aad-user>** 하 고 **로그 아웃** 을 선택 합니다.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Azure AD 사용자에 게 레지스트리 상호 작용에 필요한 역할을 부여 합니다.

1. 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 `kubeadmin` 합니다.
1. 위의 단계를 수행 하 여에 대 한 토큰을 사용 하 여 OpenShift CLI에 로그인 하 고,를 사용 하 여 `kubeadmin` `oc login` 웹 콘솔에 로그인 한 후에이를 수행 `kubeadmin` 합니다.
1. 다음 명령을 실행 하 여 **aad 사용자** 에 대 한 기본 제공 레지스트리에 대 한 액세스를 사용 하도록 설정 합니다.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>컨테이너 레지스트리 URL 가져오기

`oc get route`다음에 표시 된 것 처럼 명령을 사용 하 여 컨테이너 레지스트리 URL을 가져옵니다.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > **CONTAINER REGISTRY URL** 의 콘솔 출력을 확인 합니다. 이 가이드의 정규화 된 레지스트리 이름으로 사용 됩니다.

## <a name="next-steps"></a>다음 단계

이제 기본 제공 컨테이너 이미지 레지스트리를 설정 했으므로 OpenShift에 응용 프로그램을 배포 하 여 시작할 수 있습니다. Java 응용 프로그램의 경우 [Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty를 사용 하 여 java 응용 프로그램 배포](howto-deploy-java-liberty-app.md)를 확인 하세요.
