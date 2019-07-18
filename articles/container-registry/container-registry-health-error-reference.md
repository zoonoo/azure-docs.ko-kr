---
title: 상태 확인-Azure Container Registry에 대 한 오류 참조
description: 오류 코드 및 Azure Container Registry에서 az acr 확인 상태 진단 명령을 실행 하 여 발견 된 문제를 해결
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555112"
---
# <a name="health-check-error-reference"></a>상태 확인 오류 참조

다음은 반환한 오류 코드에 대 한 세부 정보를 [az acr 확인 상태][az-acr-check-health] 명령입니다. 각 오류에 대 한 가능한 해결 방법은 나열 됩니다.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

이 오류는 CLI에 대 한 Docker 클라이언트를 찾을 수 없습니다를 의미 합니다. 결과적으로 다음과 같은 추가 검사는 실행 되지 않습니다: Docker를 평가 하는 Docker 버전, 디먼 상태를 찾아서 Docker 실행 끌어오기 명령입니다.

*잠재적 해결 방법을*: Docker 클라이언트를 설치 합니다. Docker 경로 시스템 변수를 추가 합니다.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

이 오류는 Docker 데몬 상태를 사용할 수 없는 것 없습니다 수에 도달 하지 않았음을 CLI를 사용 하 여 의미 합니다. 결과적으로 Docker 작업 (같은 `docker login` 고 `docker pull`)는 CLI를 통해 사용할 수 없습니다.

*잠재적 해결 방법을*: Docker 디먼을 다시 시작 하거나 제대로 설치 되어 있는지 확인 합니다.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

이 오류는 CLI 명령을 실행할 수 없음을 의미 `docker --version`합니다.

*잠재적 해결 방법을*: 명령을 수동으로 실행을 시도 하세요. 최신 CLI 버전을 하 고 오류 메시지를 조사 해야 합니다.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

이 오류는 CLI 환경에 샘플 이미지를 끌어올 수 없음을 의미 합니다.

*잠재적 해결 방법을*: 이미지를 풀 하는 데 필요한 모든 구성 요소가 제대로 실행 되 고 있는지 확인 합니다.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

이 오류는 Helm 클라이언트 Helm 작업도 속하는 CLI에서 찾을 수 없습니다 것을 의미 합니다.

*잠재적 해결 방법을*: Helm 클라이언트 설치 되어 있는지와 해당 경로 시스템 환경 변수에 추가 되어 있는지 확인 합니다.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

이 오류는 CLI 설치 Helm 버전을 확인할 수 없음을 의미 합니다. 이 경우에 발생할 수 있습니다 Azure CLI 버전 (또는 경우 Helm 버전) 되는 사용 되지 않습니다.

*잠재적 해결 방법을*: 권장 되는 Helm 버전; 또는 최신 Azure CLI 버전 업데이트 명령을 수동으로 실행 하 고 오류 메시지를 조사 합니다.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

이 오류는 지정 된 레지스트리 로그인 서버에 대 한 DNS ping이 발송 된 응답 하지 않습니다, 즉, 사용할 수 없는 않았음을 의미 합니다. 이 일부 연결 문제를 나타낼 수 있습니다. 또는 레지스트리, 사용자 수 있는 권한이 없습니다 (해당 로그인 서버를 올바르게 검색) 하는 레지스트리를 없거나 대상 레지스트리는 Azure CLI에서 사용 하는 것 보다 여러 클라우드에서입니다.

*잠재적 해결 방법을*: 연결; 유효성 검사 레지스트리의 철자를 확인 하 고 해당 레지스트리가 있는지; 사용자의 적절 한 권한이 있는지 및 레지스트리의 cloud가 Azure CLI에서 사용 되는 동일한 지 확인 합니다.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

이 오류는 특정된 레지스트리에 대 한 챌린지 끝점 403 사용할 수 없음 HTTP 상태로 응답를 의미 합니다. 이 오류는 사용자가 가상 네트워크 구성으로 인해 가장 가능성이 높은 레지스트리에 액세스할 수 없는 의미 합니다.

*잠재적 해결 방법을*: 가상 네트워크 규칙을 제거 하거나 현재 클라이언트 IP 주소를 허용된 목록에 추가 합니다.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

이 오류는 대상 레지스트리 챌린지 끝점 챌린지를 실행 하지 않았으므로 의미 합니다.

*잠재적 해결 방법을*: 잠시 후 다시 시도 하세요. 오류가 계속 발생 하는 경우에 문제를 제기 https://aka.ms/acr/issues 합니다.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

이 오류 대상 레지스트리 챌린지 끝점 챌린지 발급 레지스트리는 Azure Active Directory 인증을 지원 하지 않았음을 의미 합니다.

*잠재적 해결 방법을*: 예를 들어, 관리자 자격 증명을 사용 하 여 인증 하는 다른 방법을 시도 합니다. 사용자를 Azure Active Directory를 사용 하 여 인증 해야 하는 경우에 문제를 제기 https://aka.ms/acr/issues 합니다.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

이 오류는 대상 레지스트리에 액세스할 수 없습니다 있도록 레지스트리 로그인 서버 새로 고침 토큰을 사용 하 여 응답 하지 않았습니다 의미 합니다. 사용자는 레지스트리 권한이 없는 경우 또는 Azure CLI에 대 한 사용자 자격 증명이 유효 하지 않은 경우이 오류가 발생할 수 있습니다.

*잠재적 해결 방법을*: 레지스트리를에서 사용자에 게 적절 한 권한이 있는지 확인 실행 `az login` 권한, 토큰 및 자격 증명을 새로 고쳐야 합니다.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

이 오류는 대상 레지스트리에 액세스할 수 없습니다 있도록 액세스 토큰을 사용 하 여 레지스트리 로그인 서버 응답 하지 않았습니다 의미 합니다. 사용자는 레지스트리 권한이 없는 경우 또는 Azure CLI에 대 한 사용자 자격 증명이 유효 하지 않은 경우이 오류가 발생할 수 있습니다.

*잠재적 해결 방법을*: 레지스트리를에서 사용자에 게 적절 한 권한이 있는지 확인 실행 `az login` 권한, 토큰 및 자격 증명을 새로 고쳐야 합니다.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

이 오류는 CLI 지정 레지스트리의 로그인 서버를 찾을 수 없습니다. 현재 클라우드에 대 한 기본 접미사가 없습니다를 찾을 수 있음을 의미 합니다. 이 오류는 레지스트리에 존재 하지 않는 사용자에 없는 경우 적절 한 권한이 레지스트리를에서 레지스트리의 클라우드와 현재 Azure CLI cloud 일치 하지 않는 경우 또는 Azure CLI 버전은 사용 되지 않는 경우에 발생할 수 있습니다.

*잠재적 해결 방법을*: 맞춤법 올바르고가; 레지스트리에 있는지 확인 확인 레지스트리에 해당 사용자의 권한 및 레지스트리 및 CLI 환경 클라우드 일치; Azure CLI 최신 버전으로 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

레지스트리의 상태를 확인 하는 옵션에 대해서 [Azure container registry의 상태를 확인](container-registry-check-health.md)합니다.

참조 된 [FAQ](container-registry-faq.md) 질문과 대답 및 Azure Container Registry에 대 한 알려진된 기타 문제에 대 한 합니다.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
