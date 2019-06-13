---
title: Azure Dev Space로 작업할 때 비밀을 관리하는 방법
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.openlocfilehash: 900529d54a26729d9d0fb949d9217d5e2d618254
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515285"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Azure Dev Space로 작업할 때 비밀을 관리하는 방법

서비스에는 데이터베이스 또는 기타 보안 Azure 서비스에 대한 특정 암호, 연결 문자열 및 기타 비밀이 필요할 수 있습니다. 구성 파일에서 이러한 비밀의 값을 설정하여 코드에서 환경 변수로 사용할 수 있습니다.  이러한 항목은 비밀의 보안을 손상시키지 하도록 주의해서 처리되어야 합니다.

Azure 개발 공간 클라이언트 도구에서 생성 된 Helm 차트에 비밀을 저장 하기 위한 두 가지 권장 되는, 간소화 된 옵션을 제공 하는 azure 개발 공간: azds.yaml에서 직접 인라인으로 고 values.dev.yaml 파일입니다. values.yaml에 비밀을 저장하지 않는 것이 좋습니다. Helm에 대 한 두 가지 방법 외부 클라이언트 도구에서 생성 된 차트에에서 정의 된이 문서를 만드는 경우 고유한 Helm 차트, Helm 차트를 사용 하 여 직접 관리 하 고 암호를 저장 합니다.

## <a name="method-1-valuesdevyaml"></a>방법 1: values.dev.yaml
1. Azure Dev Spaces에 대해 활성화된 프로젝트로 VS Code를 엽니다.
2. 이라는 파일을 추가 _values.dev.yaml_ 기존와 동일한 폴더에 _azds.yaml_ 에 비밀 키와 값을 다음 예제와 같이 정의 합니다.

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_ 이미 참조 하는 _values.dev.yaml_ 존재 하는 경우. 다른 파일 이름을 원한다 면 install.values 섹션을 업데이트 합니다.

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. 다음 예제와 같이 환경 변수로 이러한 비밀을 참조하도록 서비스 코드를 수정합니다.

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. 이러한 변경 내용으로 클러스터에서 실행되는 서비스를 업데이트합니다. 명령줄에서 다음 명령을 실행합니다.

    ```
    azds up
    ```
 
6. (선택 사항) 명령줄에서 이러한 비밀이 만들어졌는지 확인합니다.

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. 원본 제어에서 비밀을 커밋하지 않도록 _.gitignore_ 파일에 _values.dev.yaml_을 추가해야 합니다.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>방법 2: azds.yaml에서 직접 인라인
1.  _azds.yaml_의 yaml 섹션 구성/개발/설치 아래에서 비밀을 설정합니다. 해당 위치에서 비밀 값을 직접 입력할 수도 있지만 _azds.yaml_이 원본 제어로 확인되므로 권장되지 않습니다. 대신 "$PLACEHOLDER" 구문을 사용하여 자리 표시자를 추가합니다.

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  _azds.yaml_과 동일한 폴더에 _.env_ 파일을 만듭니다. 표준 키=값 표기법을 사용하여 비밀을 입력합니다. 원본 제어로 _.env_ 파일을 커밋하지 마십시오. (git 기반 버전 제어 시스템의 원본 제어에서 생략하려면 _.gitignore_ 파일에 추가합니다.) 다음 예제는 _.env_ 파일을 보여줍니다.

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  다음 예제와 같이 코드에서 이러한 비밀을 참조하도록 서비스 원본 코드를 수정합니다.

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  이러한 변경 내용으로 클러스터에서 실행되는 서비스를 업데이트합니다. 명령줄에서 다음 명령을 실행합니다.

    ```
    azds up
    ```

4.  (선택 사항) kubectl에서 비밀을 봅니다.

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>다음 단계

이러한 방법을 사용하여 이제 안전하게 데이터베이스, Azure Redis Cache에 연결하거나 보안 Azure 서비스에 액세스할 수 있습니다.
 
