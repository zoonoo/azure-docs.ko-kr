---
title: Azure Red Hat OpenShift 4 용 기본 제공 컨테이너 레지스트리 구성
description: Azure Red Hat OpenShift 4 용 기본 제공 컨테이너 레지스트리 구성
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414804"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 용 기본 제공 컨테이너 레지스트리 구성

이 문서에서는 Azure Red Hat OpenShift (ARO) 4 클러스터에 대 한 기본 제공 컨테이너 이미지 레지스트리를 구성 합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure AD 설정
> * Openid connect Connect 설정
> * 기본 제공 컨테이너 이미지 레지스트리 액세스

## <a name="prerequisites"></a>필수 조건

* [Azure Red Hat OpenShift 4 클러스터 만들기](/azure/openshift/tutorial-create-cluster)의 단계를 수행 하 여 클러스터를 만듭니다. 에 대 한 인수를 사용 하 여 클러스터를 만들어야 `--pull-secret` `az aro create` 합니다.  이 문서에서 요구 하는 대로 로그인을 위해 Azure AD를 설정 하려는 경우에 필요 합니다.
* [Azure Red Hat OpenShift 4 클러스터에 연결](/azure/openshift/tutorial-connect-cluster)의 단계를 수행 하 여 클러스터에 연결 합니다.
   * 이 문서의 뒷부분에 나오는 명령을 사용 하기 때문에 "OpenShift CLI 설치"의 단계를 수행 해야 `oc` 합니다.
   * 클러스터 콘솔 URL (예:)을 기록해 둡니다 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . 및의 값 `<random>` 은 `<region>` 이 문서의 뒷부분에서 사용 됩니다.
   * `kubeadmin`자격 증명을 확인 합니다. 이러한 항목은이 문서의 뒷부분에서 사용 됩니다.

## <a name="set-up-azure-active-directory"></a>Azure Active Directory 설정

Azure AD (Azure Active Directory)는 OIDC (Openid connect Connect)를 구현 합니다. OIDC를 사용 하면 Azure AD를 사용 하 여 ARO 클러스터에 로그인 할 수 있습니다. Azure AD를 설정 하려면 다음 단계를 수행 합니다.

1. [빠른 시작: 테 넌 트 설정](/azure/active-directory/develop/quickstart-create-new-tenant)의 단계를 수행 하 여 Azure AD 테 넌 트를 설정 합니다. Azure 계정에 테 넌 트가 이미 있을 수 있습니다. 이 경우 테 넌 트에서 단계를 수행 하는 데 충분 한 권한이 있는 경우 하나 만들기를 건너뛸 수 있습니다. **테 넌 트 ID** 를 적어 둡니다. 이 값은 나중에 사용 됩니다.
2. [Azure Active Directory를 사용 하 여 사용자 추가 또는 삭제](/azure/active-directory/fundamentals/add-users-azure-active-directory)의 단계에 따라 Azure AD 사용자를 하나 이상 만듭니다. 이러한 계정을 사용 하 여 응용 프로그램을 테스트할 수 있습니다. 로그인 하려면 이러한 계정의 메일 주소와 암호를 기록해 두세요.
3. [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](/azure/active-directory/develop/quickstart-register-app)의 단계를 수행 하 여 Azure AD 테 넌 트에서 새 응용 프로그램 등록을 만듭니다. 
   1. 및의 값에 대 한 사전 요구 사항에 `<random>` 유의 `<region>` 하십시오. 이러한 값을 사용 하 여 다음 수식에 따라 URI를 만듭니다.

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. **리디렉션 uri** 필드를 처리 하는 단계에 도달 하면 이전 단계의 uri를 입력 합니다.
   1. **등록** 을 선택합니다.
   1. 앱에 대 한 **개요** 페이지에서 여기에 표시 된 것 처럼 **응용 프로그램 (클라이언트) ID** 에 대해 표시 된 값을 확인 합니다.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Azure AD 응용 프로그램의 개요 페이지.":::

4. 새 클라이언트 암호를 만듭니다. 
   1. 새로 만든 응용 프로그램 등록의 왼쪽 탐색 창에서 **인증서 & 암호** 를 선택 합니다.
   1. **새 클라이언트 비밀** 을 선택합니다.
   1. **설명을** 입력 하 고 **추가** 를 선택 합니다.
   1. 생성 된 **클라이언트 암호** 값을 따로 저장 합니다. 이 값은이 문서의 뒷부분에서 사용 됩니다.

### <a name="add-openid-connect-identity-provider"></a>Openid connect Connect id 공급자 추가

ARO는 기본 제공 컨테이너 레지스트리를 제공 합니다.  이 파일에 액세스 하려면 다음 단계에 따라 Azure AD OIDC를 사용 하 여 id 공급자 (IDP)를 구성 합니다.

1. 브라우저에서로 OpenShift 웹 콘솔에 로그인 `kubeadmin` 합니다.  이 절차는 [자습서: Azure Red Hat OpenShift 4 클러스터에 연결](/azure/openshift/tutorial-connect-cluster)의 단계를 실행할 때 사용 되는 절차와 같습니다.
1. 왼쪽 탐색 창에서 **관리**  >  **클러스터 설정** 을 선택 합니다.
1. 페이지 중간에서 **전역 구성** 을 선택 합니다.
1. 테이블의 **구성 리소스** 열에서 **OAuth** 를 찾아 선택 합니다.
1. **Id 공급자** 에서 **추가** 를 선택 하 고 **openid connect 연결** 을 선택 합니다.
1. **이름** 을 **openid connect** 로 설정 합니다.  이 값은 이미 채워져 있을 수 있습니다.
1. **클라이언트 ID** 및 **클라이언트 암호** 를 이전 단계의 값으로 설정 합니다.
1. **발급자 URL** 의 값을 찾으려면 다음 단계를 수행 합니다.
   1. 를 **\<tenant-id>** URL에서 **Azure Active Directory 설정** 하는 동안 저장 된 항목으로 바꿉니다 `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Azure Portal와 상호 작용 하는 데 사용한 것과 동일한 브라우저에서 URL을 엽니다.
   1. 반환 된 JSON 본문의 속성 **발급자** 값을 복사 하 여 **발급자 URL** 레이블이 지정 된 텍스트 상자에 붙여넣습니다.  **발급자** 값은와 같습니다 `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. 페이지 아래쪽으로 이동 하 고 **추가** 를 선택 합니다.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenShift에서 Connect를 Openid connect 합니다.":::
1. 브라우저 창의 오른쪽 위에 있는 **kube: admin** 단추를 선택 하 고 **로그 아웃** 을 선택 하 여 Openshift 웹 콘솔에서 로그 아웃 합니다.

### <a name="access-the-built-in-container-image-registry"></a>기본 제공 컨테이너 이미지 레지스트리 액세스

다음 지침에 따라 기본 제공 레지스트리에 액세스할 수 있습니다.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>관리자가 될 Azure AD 사용자 정의

1. Azure AD 사용자의 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 합니다.

   1. InPrivate, Incognito 또는 기타 해당 브라우저 창 기능을 사용 하 여 콘솔에 로그인 합니다.
   1. 이 창은 OIDC를 사용 하도록 설정한 후 다르게 보입니다.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Openid connect Connect 활성화 된 로그인 창":::
   1. **Openid connect** 선택

   > [!NOTE]
   > 여기에서 로그인 하는 데 사용 하는 사용자 이름 및 암호를 기록해 둡니다. 이 사용자 이름 및 암호는이 문서 및 다른 문서에서 다른 작업의 관리자 역할을 합니다.
1. 다음 단계를 사용 하 여 OpenShift CLI로 로그인 합니다.  논의를 위해이 프로세스를 라고 `oc login` 합니다.
   1. 웹 콘솔의 맨 오른쪽에서 로그인 한 사용자의 상황에 맞는 메뉴를 확장 하 고 **로그인 명령 복사** 를 선택 합니다.
   1. 필요한 경우 동일한 사용자를 사용 하 여 새 탭 창에 로그인 합니다.
   1. **토큰 표시** 를 선택 합니다.
   1. 아래와 같이 **이 토큰을 사용 하 여 로그인** 아래 나열 된 값을 클립보드에 복사 하 고 셸에서 실행 합니다.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. `oc whoami`콘솔에서를 실행 하 고 출력을로 기록 합니다 **\<aad-user>** .  이 값은이 문서의 뒷부분에서 사용 합니다.
1. OpenShift 웹 콘솔에서 로그 아웃 합니다. 로 레이블이 지정 된 브라우저 창의 오른쪽 위에 있는 단추를 선택 **\<aad-user>** 하 고 **로그 아웃** 을 선택 합니다.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Azure AD 사용자에 게 레지스트리 상호 작용에 필요한 역할을 부여 합니다.

1. 자격 증명을 사용 하 여 브라우저에서 OpenShift 웹 콘솔에 로그인 `kubeadmin` 합니다.
1. 위의 단계를 수행 하 여에 대 한 토큰을 사용 하 여 OpenShift CLI에 로그인 하 고,를 사용 하 여 `kubeadmin` `oc login` 웹 콘솔에 로그인 한 후에이를 수행 `kubeadmin` 합니다.
1. 다음 명령을 실행 하 여 **aad 사용자** 에 대 한 기본 제공 레지스트리에 대 한 액세스를 사용 하도록 설정 합니다.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   출력은 다음과 유사 합니다.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   출력은 다음과 유사 합니다.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   출력은 다음과 유사 합니다.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   출력은 다음과 유사 합니다.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
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

OpenShift에 응용 프로그램을 배포 하 여 기본 제공 컨테이너 이미지 레지스트리를 사용 합니다.  Java 응용 프로그램의 경우 [Azure Red Hat OpenShift 4 클러스터에서 방법 가이드, Open Liberty/WebSphere Liberty를 사용 하 여 Java 응용 프로그램 배포](howto-deploy-java-liberty-app.md)를 참조 하세요.
