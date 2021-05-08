---
title: 레지스트리 상태 검사에 대한 오류 참조
description: Azure Container Registry에서 az acr check-health 진단 명령을 실행하여 발견된 문제에 대한 오류 코드 및 가능한 해결 방법
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773444"
---
# <a name="health-check-error-reference"></a>상태 검사 오류 참조

다음은 [az acr check-health][az-acr-check-health] 명령에서 반환된 오류 코드에 대한 세부 정보입니다. 각 오류에 대해 가능한 해결 방법이 나열되어 있습니다.

`az acr check-healh` 실행에 대한 자세한 내용은 [Azure Container Registry의 상태 확인](container-registry-check-health.md)을 참조하세요.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

이 오류는 CLI용 Docker 클라이언트를 찾을 수 없다는 의미입니다. 따라서 Docker 버전 찾기, Docker 데몬 상태 평가, Docker pull 명령 실행과 같은 추가 검사가 실행되지 않습니다.

잠재적 해결 방법: Docker 클라이언트를 설치하거나 시스템 변수에 Docker 경로를 추가합니다.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

이 오류는 Docker 디먼 상태를 사용할 수 없거나 CLI를 사용하여 연결할 수 없다는 의미입니다. 따라서 CLI를 통해 Docker 작업(예: `docker login` 및 `docker pull`)을 사용할 수 없습니다.

잠재적 해결 방법: Docker 디먼을 다시 시작하거나 제대로 설치되었는지 확인합니다.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

이 오류는 CLI에서 `docker --version` 명령을 실행할 수 없음을 의미합니다.

잠재적 해결 방법: 명령을 수동으로 실행하고, 최신 CLI 버전이 있는지 확인하고, 오류 메시지를 조사합니다.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

이 오류는 CLI가 샘플 이미지를 환경으로 끌어올 수 없다는 의미입니다.

잠재적 해결 방법: 이미지를 가져오는 데 필요한 모든 구성 요소가 제대로 실행되고 있는지 확인합니다.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

이 오류는 다른 Helm 작업을 차단하는 CLI에서 Helm 클라이언트를 찾을 수 없다는 의미입니다.

잠재적 해결 방법: Helm 클라이언트가 설치되어 있고 그 경로가 시스템 환경 변수에 추가되었는지 확인합니다.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

이 오류는 CLI가 설치된 Helm 버전을 확인할 수 없다는 의미입니다. 이 문제는 사용 중인 Azure CLI 버전(또는 Helm 버전)이 더 이상 사용되지 않는 경우에 발생할 수 있습니다.

잠재적 해결 방법: 최신 Azure CLI 버전 또는 권장되는 Helm 버전으로 업데이트합니다. 명령을 수동으로 실행하고 오류 메시지를 조사합니다.

## <a name="cmk_error"></a>CMK_ERROR

이 오류는 레지스트리가 고객이 관리하는 키로 레지스트리 암호화를 구성하는 데 사용되는 사용자 할당 또는 시스템 할당 관리 ID에 액세스할 수 없음을 의미합니다. 관리 ID가 삭제되었을 수 있습니다.  

잠재적 해결 방법: 문제를 해결하고 다른 관리 ID를 사용하여 키를 회전하려면 [사용자 할당 ID](container-registry-customer-managed-keys.md#troubleshoot) 문제 해결 단계를 참조하세요.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

이 오류는 지정된 레지스트리 로그인 서버에 대한 DNS를 ping했으나 응답하지 않았음, 즉 사용할 수 없음을 의미합니다. 이는 일부 연결 문제를 나타낼 수 있습니다. 또는 레지스트리가 없거나, 사용자에게 레지스트리에 대한(로그인 서버를 제대로 검색하기 위한) 권한이 없거나, 대상 레지스트리가 Azure CLI에 사용된 것과 다른 클라우드에 있는 것일 수 있습니다.

잠재적 해결 방법: 연결 유효성 검사 레지스트리의 철자와 레지스트리가 있는지 확인합니다. 사용자에게 이에 대한 적절한 권한이 있는지 확인하고, 레지스트리의 클라우드가 Azure CLI에 사용된 것과 동일한지 확인합니다.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

이 오류는 지정된 레지스트리에 대한 챌린지 엔드포인트가 403 사용할 수 없음 HTTP 상태로 응답했다는 의미입니다. 이 오류는 사용자가 레지스트리에 액세스할 수 없다는 의미입니다. 가상 네트워크 구성으로 인해 또는 레지스트리의 공용 엔트포인트에 대한 액세스가 허용되지 않았기 때문일 가능성이 가장 높습니다. 현재 구성된 방화벽 규칙을 보려면 `az acr show --query networkRuleSet --name <registry>`를 실행합니다.

잠재적 해결 방법: 가상 네트워크 규칙을 제거하거나 현재 클라이언트 IP 주소를 허용 목록에 추가합니다.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

이 오류는 대상 레지스트리의 챌린지 엔드포인트에서 챌린지를 발행하지 않았다는 의미입니다.

잠재적 해결 방법: 잠시 후에 다시 시도합니다. 오류가 계속 발생하면 https://aka.ms/acr/issues 에서 문제를 엽니다.

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

이 오류는 대상 레지스트리의 챌린지 엔드포인트에서 챌린지를 발행했지만 레지스트리가 Azure Active Directory 인증을 지원하지 않는다는 의미입니다.

잠재적 해결 방법: 관리자 자격 증명을 사용하는 등의 다른 방법으로 인증을 시도합니다. 사용자가 Azure Active Directory를 사용하여 인증해야 하는 경우 https://aka.ms/acr/issues 에서 문제를 여세요.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

이 오류는 레지스트리 로그인 서버에서 새로 고침 토큰으로 응답하지 않아 대상 레지스트리에 대한 액세스가 거부되었다는 의미입니다. 이 오류는 사용자에게 레지스트리에 대한 적절한 권한이 없거나 Azure CLI에 대한 사용자 자격 증명이 유효하지 않은 경우에 발생할 수 있습니다.

잠재적 해결 방법: 사용자에게 레지스트리에 대한 적절한 권한이 있는지 확인합니다. `az login`을 실행하여 사용 권한, 토큰, 자격 증명을 새로 고칩니다.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

이 오류는 레지스트리 로그인 서버에서 액세스 토큰으로 응답하지 않아 대상 레지스트리에 대한 액세스가 거부되었다는 의미입니다. 이 오류는 사용자에게 레지스트리에 대한 적절한 권한이 없거나 Azure CLI에 대한 사용자 자격 증명이 유효하지 않은 경우에 발생할 수 있습니다.

잠재적 해결 방법: 사용자에게 레지스트리에 대한 적절한 권한이 있는지 확인합니다. `az login`을 실행하여 사용 권한, 토큰, 자격 증명을 새로 고칩니다.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

이 오류는 클라이언트에서 컨테이너 레지스트리에 대한 보안 연결을 설정할 수 없다는 의미입니다. 이 오류는 일반적으로 프록시 서버를 사용하거나 실행하는 경우 발생합니다.

잠재적 해결 방법: 프록시를 사용하는 방법에 대한 자세한 내용은 [여기를 참조](/cli/azure/use-cli-effectively)하세요.

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

이 오류는 CLI가 지정된 레지스트리의 로그인 서버를 찾을 수 없고 현재 클라우드에 대한 기본 접미사를 찾지 못했다는 의미입니다. 이 오류는 사용자에게 레지스트리에 대한 적절한 권한이 없거나, 레지스트리의 클라우드와 현재 Azure CLI 클라우드가 일치하지 않거나 Azure CLI 버전이 사용되지 않는 경우에 발생할 수 있습니다.

잠재적 해결 방법: 철자가 올바른지와 레지스트리가 있는지 확인합니다. 사용자에게 레지스트리에 대한 올바른 사용 권한이 있는지 확인하고 레지스트리 및 CLI 환경의 클라우드가 일치하는지 확인합니다. Azure CLI를 최신 버전으로 업데이트합니다.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

이 오류는 CLI가 현재 설치된 Docker/Notary 버전과 호환되지 않는다는 의미입니다. 이 문제를 해결하려면 Docker 설치의 Notary 클라이언트를 수동으로 교체하여 notary.exe 버전을 0.6.0 이전 버전으로 다운그레이드하세요.

## <a name="next-steps"></a>다음 단계

레지스트리의 상태를 확인하는 옵션은 [Azure Container Registry의 상태 확인](container-registry-check-health.md)을 참조하세요.

Azure Container Registry에 대한 질문과 대답 및 알려진 문제는 [FAQ](container-registry-faq.md)를 참조하세요.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
