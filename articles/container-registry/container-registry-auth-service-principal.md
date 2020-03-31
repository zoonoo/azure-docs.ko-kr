---
title: 서비스 주체를 사용하여 인증
description: Azure Active Directory 서비스 주체를 사용하여 프라이빗 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공합니다.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455413"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>서비스 주체로 Azure Container Registry 인증

Azure AD(Azure Active Directory) 서비스 주체를 사용하여 컨테이너 레지스트리에 대해 컨테이너 이미지 `docker push` 및 `pull` 액세스 권한을 제공할 수 있습니다. 서비스 주체를 사용하면 "헤드리스" 서비스 및 애플리케이션에 대한 액세스를 제공할 수 있습니다.

## <a name="what-is-a-service-principal"></a>서비스 주체란?

Azure AD *서비스 주체*는 구독 내 Azure 리소스에 대한 액세스를 제공합니다. "서비스"가 리소스에 액세스해야 하는 모든 응용 프로그램, 서비스 또는 플랫폼인 서비스의 사용자 ID로 서비스 주체를 생각할 수 있습니다. 지정한 리소스에 대해서만 액세스 권한이 있는 서비스 주체를 구성할 수 있습니다. 그런 다음, 서비스 주체의 자격 증명을 사용하여 해당 리소스에 액세스하도록 애플리케이션이나 서비스를 구성합니다.

Azure Container Registry 컨텍스트에서, Azure의 프라이빗 레지스트리에 대한 풀, 푸시 및 풀 또는 기타 권한이 있는 Azure AD 서비스 주체를 만들 수 있습니다. 전체 목록에 대해서는 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.

## <a name="why-use-a-service-principal"></a>서비스 주체를 사용하는 이유

Azure AD 서비스 주체를 사용하면 프라이빗 컨테이너 레지스트리에 대해 범위가 지정된 액세스를 제공할 수 있습니다. 레지스트리에 대한 맞춤형 액세스 권한을 가진 각 응용 프로그램 또는 서비스에 대해 서로 다른 서비스 주체를 만듭니다. 또한 서비스와 애플리케이션 사이에 자격 증명을 공유하지 않아도 되기 때문에, 선택한 서비스 주체(및 애플리케이션)에 대해서만 자격 증명을 순환하거나 액세스 권한을 철회할 수 있습니다.

예를 들어 이미지 액세스만 제공하는 서비스 주체를 `pull` 사용하도록 웹 응용 프로그램을 구성하고 빌드 시스템에서는 `push` 둘 `pull` 다 및 액세스 권한을 제공하는 서비스 주체를 사용합니다. 응용 프로그램 개발이 변경되면 빌드 시스템에 영향을 주지 않고 서비스 주체 자격 증명을 회전할 수 있습니다.

## <a name="when-to-use-a-service-principal"></a>서비스 주체를 사용하는 경우

**헤드리스 시나리오**에서 레지스트리 액세스를 제공하려면 서비스 주체를 사용해야 합니다. 즉, 자동 또는 무인 방식으로 컨테이너 이미지를 푸시하거나 풀해야 하는 모든 애플리케이션, 서비스 또는 스크립트입니다. 예를 들어:

  * *끌어오기*: 레지스트리에서 Kubernetes, DC/OS 및 Docker 군단을 포함한 오케스트레이션 시스템에 컨테이너를 배포합니다. 또한 컨테이너 레지스트리에서 [AKS(Azure Kubernetes Service),](../aks/cluster-container-registry-integration.md) [Azure 컨테이너 인스턴스,](container-registry-auth-aci.md)앱 [서비스,](../app-service/index.yml) [일괄 처리,](../batch/index.yml) [서비스 패브릭](/azure/service-fabric/)등과 같은 관련 Azure 서비스로 가져올 수도 있습니다.

  * *푸시*: Azure 파이프라인 또는 Jenkins와 같은 지속적인 통합 및 배포 솔루션을 사용하여 컨테이너 이미지를 빌드하고 레지스트리로 푸시합니다.

개발 워크스테이션에 컨테이너 이미지를 수동으로 가져오는 경우와 같이 레지스트리에 대한 개별 액세스의 경우 레지스트리 액세스(예: [az acr 로그인)에][az-acr-login]대한 대신 고유한 [Azure AD ID를](container-registry-authentication.md#individual-login-with-azure-ad) 사용하는 것이 좋습니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>샘플 스크립트

GitHub에서 Azure CLI에 대한 이전 샘플 스크립트와 Azure PowerShell에 대한 버전을 찾을 수 있습니다.

* [Azure CLI][acr-scripts-cli]
* [Azure 파워쉘][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>서비스 주체로 인증

컨테이너 레지스트리에 대한 액세스 권한을 부여한 서비스 주체가 있으면 "headless" 서비스 및 응용 프로그램에 액세스할 수 있도록 `docker login` 자격 증명을 구성하거나 명령을 사용하여 입력할 수 있습니다. 다음 값을 사용합니다.

* **사용자 이름** - 서비스 주체 응용 프로그램 ID(클라이언트 *ID라고도*함)
* **암호** - 서비스 주체 *암호(클라이언트 암호라고도*함)

각 값은 양식의 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID입니다. 

> [!TIP]
> [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 명령을 실행하여 서비스 주체의 암호를 다시 생성할 수 있습니다.
>

### <a name="use-credentials-with-azure-services"></a>Azure 서비스에서 자격 증명 사용

Azure 컨테이너 레지스트리를 사용하여 인증하는 모든 Azure 서비스에서 서비스 주체 자격 증명을 사용할 수 있습니다.  다양한 시나리오에 대해 레지스트리의 관리자 자격 증명 대신 서비스 주체 자격 증명을 사용합니다.

예를 들어 자격 증명을 사용하여 Azure 컨테이너 레지스트리에서 [Azure 컨테이너 인스턴스로](container-registry-auth-aci.md)이미지를 가져옵니다.

### <a name="use-with-docker-login"></a>도커 로그인과 함께 사용

서비스 주체를 사용하여 실행할 `docker login` 수 있습니다. 다음 예제에서는 서비스 주체 응용 프로그램 ID가 `$SP_APP_ID`환경 변수 및 `$SP_PASSWD`변수의 암호에 전달됩니다. Docker 자격 증명을 관리하는 모범 사례는 [docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조하십시오.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

로그인하면 Docker는 자격 증명을 캐시합니다.

### <a name="use-with-certificate"></a>인증서와 함께 사용

서비스 주체에 인증서를 추가한 경우 인증서 기반 인증을 사용하여 Azure CLI에 로그인한 다음 [az acr 로그인][az-acr-login] 명령을 사용하여 레지스트리에 액세스할 수 있습니다. CLI를 사용할 때 암호 대신 인증서를 암호로 사용하여 추가 보안을 제공합니다. 

서비스 주체를 만들 때 자체 [서명된 인증서를 만들](/cli/azure/create-an-azure-service-principal-azure-cli)수 있습니다. 또는 기존 서비스 주체에 하나 이상의 인증서를 추가합니다. 예를 들어 이 문서의 스크립트 중 하나를 사용하여 레지스트리에서 이미지를 가져오거나 푸시할 수 있는 권한을 가진 서비스 주체를 만들거나 업데이트하는 경우 [az ad sp 자격 증명 재설정][az-ad-sp-credential-reset] 명령을 사용하여 인증서를 추가합니다.

인증서와 함께 서비스 주체를 사용하여 [Azure CLI에 로그인하려면](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)인증서가 PEM 형식이어야 하며 개인 키를 포함해야 합니다. 인증서가 필요한 형식이 아닌 경우 인증서를 변환하는 등의 `openssl` 도구를 사용합니다. [az 로그인을][az-login] 실행하여 서비스 주체를 사용하여 CLI에 로그인하면 서비스 주체의 응용 프로그램 ID와 Active Directory 테넌트 ID도 제공합니다. 다음 예제에서는 이러한 값을 환경 변수로 보여 주며 있습니다.

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

그런 다음 [az acr 로그인을][az-acr-login] 실행하여 레지스트리를 사용하여 인증합니다.

```azurecli
az acr login --name myregistry
```

CLI는 레지스트리를 사용하여 세션을 `az login` 인증하기 위해 실행했을 때 생성된 토큰을 사용합니다.

## <a name="next-steps"></a>다음 단계

* Azure 컨테이너 레지스트리를 사용하여 인증할 다른 시나리오에 대한 [인증 개요를](container-registry-authentication.md) 참조하십시오.

* Azure 키 자격 증명 모음을 사용하여 컨테이너 레지스트리에 대한 서비스 주체 자격 증명을 저장하고 검색하는 예는 [ACR 작업을 사용하여 컨테이너 이미지를 빌드하고 배포하는](container-registry-tutorial-quick-task.md)자습서를 참조하십시오.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
