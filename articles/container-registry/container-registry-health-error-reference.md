---
title: 상태 확인에 대한 오류 참조
description: Azure 컨테이너 레지스트리에서 az acr 확인 상태 진단 명령을 실행하여 발견된 문제에 대한 오류 코드 및 가능한 해결 방법
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289144"
---
# <a name="health-check-error-reference"></a>상태 확인 오류 참조

다음은 [az acr 확인 상태][az-acr-check-health] 명령에서 반환되는 오류 코드에 대한 세부 정보입니다. 각 오류에 대해 가능한 해결 솔루션이 나열됩니다.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

이 오류는 CLI에 대한 Docker 클라이언트를 찾을 수 없다는 것을 의미합니다. 따라서 Docker 버전 찾기, Docker 데몬 상태 평가 및 Docker 끌어오기 명령 실행과 같은 추가 검사가 실행되지 않습니다.

*잠재적인 솔루션*: Docker 클라이언트 설치; 시스템 변수에 Docker 경로를 추가합니다.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

이 오류는 Docker 데몬 상태를 사용할 수 없거나 CLI를 사용하여 이 에 도달할 수 없음을 의미합니다. 따라서 CLI를 통해 Docker `docker login` `docker pull`작업(예: 및)을 사용할 수 없습니다.

*잠재적인 해결 방법*: Docker 데몬을 다시 시작하거나 올바르게 설치되어 있는지 확인합니다.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

이 오류는 CLI가 명령을 `docker --version`실행할 수 없다는 것을 의미합니다.

*잠재적인 해결 방법:* 명령을 수동으로 실행하고 최신 CLI 버전이 있는지 확인하고 오류 메시지를 조사합니다.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

이 오류는 CLI가 사용자 환경으로 샘플 이미지를 가져올 수 없다는 것을 의미합니다.

*잠재적인 해결 방법*: 이미지를 가져오는 데 필요한 모든 구성 요소가 제대로 실행되고 있는지 확인합니다.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

이 오류는 다른 Helm 작업을 배제하는 CLI에서 Helm 클라이언트를 찾을 수 없다는 것을 의미합니다.

*잠재적인 솔루션:* Helm 클라이언트가 설치되어 있고 해당 경로가 시스템 환경 변수에 추가되었는지 확인합니다.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

이 오류는 CLI가 설치된 Helm 버전을 확인할 수 없음을 의미합니다. 사용 중인 Azure CLI 버전(또는 헬름 버전)이 더 이상 사용되지 않는 경우 이러한 일이 발생할 수 있습니다.

*잠재적인 해결 방법*: 최신 Azure CLI 버전 또는 권장 Helm 버전으로 업데이트합니다. 명령을 수동으로 실행하고 오류 메시지를 조사합니다.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

이 오류는 지정된 레지스트리 로그인 서버의 DNS가 ping되었지만 응답하지 않았음을 의미하므로 사용할 수 없습니다. 이는 일부 연결 문제를 나타낼 수 있습니다. 또는 레지스트리가 존재하지 않거나, 사용자가 레지스트리에 대한 사용 권한이 없거나(로그인 서버를 올바르게 검색하기 위해) 대상 레지스트리가 Azure CLI에 사용된 것과 다른 클라우드에 있을 수 있습니다.

*잠재적 인 솔루션*: 연결의 유효성을 검사; 레지스트리의 맞춤법 확인 및 해당 레지스트리가 존재합니다. 사용자에게 올바른 사용 권한이 있는지, 레지스트리의 클라우드가 Azure CLI에서 사용되는 것과 동일한지 확인합니다.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

이 오류는 지정된 레지스트리의 챌린지 끝점이 403 금지된 HTTP 상태로 응답됨을 의미합니다. 이 오류는 사용자가 가상 네트워크 구성으로 인해 레지스트리에 액세스할 수 없다는 것을 의미합니다. 현재 구성된 방화벽 규칙을 보려면 `az acr show --query networkRuleSet --name <registry>`을 실행합니다.

*잠재적인 해결 방법*: 가상 네트워크 규칙을 제거하거나 허용된 목록에 현재 클라이언트 IP 주소를 추가합니다.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

이 오류는 대상 레지스트리의 챌린지 끝점이 챌린지를 발행하지 않았음을 의미합니다.

*잠재적인 해결 방법*: 잠시 후 다시 시도하십시오. 오류가 지속되면 에서 https://aka.ms/acr/issues문제를 엽니다.

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

이 오류는 대상 레지스트리의 챌린지 끝점에서 챌린지를 발급했지만 레지스트리가 Azure Active Directory 인증을 지원하지 않음을 의미합니다.

*잠재적인 해결 방법*: 관리자 자격 증명을 사용하여 다른 방식으로 인증해 보십시오. 사용자가 Azure Active Directory를 사용하여 인증해야 하는 https://aka.ms/acr/issues경우 에서 문제를 엽니다.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

이 오류는 레지스트리 로그인 서버가 새로 고침 토큰으로 응답하지 않았므로 대상 레지스트리에 대한 액세스가 거부됨을 의미합니다. 이 오류는 사용자가 레지스트리에 대한 올바른 사용 권한이 없거나 Azure CLI에 대한 사용자 자격 증명이 오래된 경우 발생할 수 있습니다.

*잠재적인 해결 방법*: 사용자가 레지스트리에 적합한 권한이 있는지 확인합니다. 실행하여 `az login` 사용 권한, 토큰 및 자격 증명을 새로 고칩니다.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

이 오류는 레지스트리 로그인 서버가 액세스 토큰으로 응답하지 않아 대상 레지스트리에 대한 액세스가 거부됨을 의미합니다. 이 오류는 사용자가 레지스트리에 대한 올바른 사용 권한이 없거나 Azure CLI에 대한 사용자 자격 증명이 오래된 경우 발생할 수 있습니다.

*잠재적인 해결 방법*: 사용자가 레지스트리에 적합한 권한이 있는지 확인합니다. 실행하여 `az login` 사용 권한, 토큰 및 자격 증명을 새로 고칩니다.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

이 오류는 클라이언트가 컨테이너 레지스트리에 대한 보안 연결을 설정할 수 없음을 의미합니다. 이 오류는 일반적으로 프록시 서버를 실행 하거나 사용 하는 경우 발생 합니다.

*잠재적인 해결 방법*: 프록시 뒤에서 작업하는 방법에 대한 자세한 내용은 [여기에서 찾을](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)수 있습니다.

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

이 오류는 CLI가 지정된 레지스트리의 로그인 서버를 찾을 수 없고 현재 클라우드에 대한 기본 접미사를 찾을 수 없음을 의미합니다. 이 오류는 레지스트리가 존재하지 않거나, 사용자가 레지스트리에 대한 올바른 사용 권한이 없는 경우, 레지스트리의 클라우드와 현재 Azure CLI 클라우드가 일치하지 않는 경우 또는 Azure CLI 버전이 더 이상 사용되지 않는 경우 발생할 수 있습니다.

*잠재적해결:* 맞춤법이 올바른지, 레지스트리가 있는지 확인합니다. 사용자가 레지스트리에 대한 올바른 사용 권한이 있는지, 레지스트리 및 CLI 환경의 클라우드가 일치하는지 확인합니다. Azure CLI를 최신 버전으로 업데이트합니다.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

이 오류는 CLI가 현재 설치된 Docker/공증인 버전과 호환되지 않음을 의미합니다. 이 문제를 해결하기 위해 Docker 설치의 공증인 클라이언트를 수동으로 교체하여 공증인 버전을 0.6.0 이전 버전으로 다운그레이드해 보십시오.

## <a name="next-steps"></a>다음 단계

레지스트리의 상태를 확인하는 옵션은 [Azure 컨테이너 레지스트리의 상태 확인을](container-registry-check-health.md)참조하십시오.

자주 묻는 질문 및 Azure 컨테이너 레지스트리에 대한 기타 알려진 문제는 [FAQ를](container-registry-faq.md) 참조하십시오.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
