---
title: Kubernetes tools를 사용 하 여 SQL 관리 되는 인스턴스 만들기
description: Kubernetes tools를 사용 하 여 SQL 관리 되는 인스턴스 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 290745f89c7978fdcbda8be566938b58167a4f5a
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040913"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Kubernetes tools를 사용 하 여 Azure SQL 관리 되는 인스턴스 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>필수 조건

[Azure Arc data controller](./create-data-controller.md)를 이미 만들어야 합니다.

Kubernetes tools를 사용 하 여 SQL 관리 되는 인스턴스를 만들려면 Kubernetes 도구가 설치 되어 있어야 합니다.  이 문서의 예제에서는 `kubectl` 를 사용 하지만, 유사한 접근 방식은 Kubernetes 대시보드, 등의 다른 Kubernetes 도구와 함께 사용 `oc` 하거나 `helm` 이러한 도구와 Kubernetes yaml/json에 익숙한 경우에도 사용할 수 있습니다.

[Kubectl 도구 설치](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>개요

SQL 관리 되는 인스턴스를 만들려면 시스템 관리자 로그인 및 암호를 안전 하 게 저장 하 고 sqlmanagedinstance 사용자 지정 리소스 정의에 따라 SQL 관리 되는 인스턴스 사용자 지정 리소스를 저장 하는 Kubernetes 암호를 만들어야 합니다.

## <a name="create-a-yaml-file"></a>Yaml 파일 만들기

[템플릿 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml) 파일을 시작 지점으로 사용 하 여 사용자 지정 SQL 관리 되는 인스턴스 yaml 파일을 만들 수 있습니다.  이 파일을 로컬 컴퓨터에 다운로드 하 여 텍스트 편집기에서 엽니다.  Lint 등의 텍스트 [VS Code](https://code.visualstudio.com/download) 편집기를 사용 하 여 구문 강조 표시를 지원 하 고 yaml 파일에 대해를 사용 하는 것이 유용 합니다.

예제 yaml 파일입니다.

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>로그인 및 암호 사용자 지정

Kubernetes 암호는 b a s e 64로 인코딩된 문자열로 저장 되며, 사용자 이름 및 암호에 대해 하나입니다.  시스템 관리자 로그인 및 암호를 base64로 인코딩하고 및의 자리 표시자 위치에 배치 해야 합니다 `data.password` `data.username` .  `<`템플릿에 제공 된 및 기호를 포함 하지 마십시오 `>` .

> [!NOTE]
> 최적의 보안을 위해 로그인에는 ' sa ' 값을 사용할 수 없습니다.
> [암호 복잡성 정책](/sql/relational-databases/security/password-policy#password-complexity)을 따릅니다.

온라인 도구를 사용 하 여 원하는 사용자 이름과 암호를 base64로 인코드 하거나, 플랫폼에 따라 기본 제공 CLI 도구를 사용할 수 있습니다.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>이름 사용자 지정

템플릿의 이름 특성에 ' example ' 값이 있습니다.  이를 변경할 수 있지만 DNS 명명 표준을 따르는 문자 여야 합니다.  또한 일치 하는 암호의 이름을 변경 해야 합니다.  예를 들어 SQL 관리 되는 인스턴스의 이름을 ' sql1 '로 변경 하는 경우 암호의 이름을 ' example-sql1 '에서 ' '로 변경 해야 합니다.

### <a name="customizing-the-resource-requirements"></a>리소스 요구 사항 사용자 지정

필요에 따라 RAM 및 코어 제한과 요청 등의 리소스 요구 사항을 변경할 수 있습니다.  

> [!NOTE]
> [Kubernetes 리소스 관리](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)에 대해 자세히 알아볼 수 있습니다.

리소스 제한 및 요청에 대 한 요구 사항:
- 코어 제한 값은 요금 청구를 위해 **필요** 합니다.
- 리소스 요청 및 제한의 나머지 부분은 선택 사항입니다.
- 지정 된 경우 코어 제한과 요청은 양의 정수 값 이어야 합니다.
- 코어 요청 (지정 된 경우)에는 최소 2 개의 코어가 필요 합니다.
- 메모리 값 형식은 Kubernetes 표기법을 따릅니다.  
- 지정 된 경우 메모리 요청에 최소 2Gi가 필요 합니다.
- 일반적으로 프로덕션 사용 사례의 경우 1 코어 마다 4GB RAM이 필요 합니다.

### <a name="customizing-service-type"></a>서비스 유형 사용자 지정

원하는 경우 서비스 유형을 NodePort로 변경할 수 있습니다.  임의의 포트 번호가 할당 됩니다.

### <a name="customizing-storage"></a>저장소 사용자 지정

사용자 환경에 맞게 저장소의 저장소 클래스를 사용자 지정할 수 있습니다.  사용할 수 있는 저장소 클래스를 잘 모르는 경우 명령을 실행 하 여 해당 저장소 클래스를 볼 수 있습니다 `kubectl get storageclass` .  템플릿의 기본값은 ' 기본값 '입니다.  즉, ' 기본 ' _이라는_ 저장소 클래스가 있으며 _이는 기본_ 저장소 클래스가 없다는 것을 의미 합니다.  필요에 따라 저장소 크기를 변경할 수도 있습니다.  [저장소 구성](./storage-configuration.md)에 대 한 자세한 내용은을 참조 하세요.

## <a name="creating-the-sql-managed-instance"></a>SQL 관리 되는 인스턴스 만들기

이제 SQL 관리 되는 인스턴스 yaml 파일을 사용자 지정 했으므로 다음 명령을 실행 하 여 SQL 관리 되는 인스턴스를 만들 수 있습니다.

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>만들기 상태 모니터링

SQL 관리 되는 인스턴스를 만드는 것은 완료 하는 데 몇 분 정도 걸립니다. 다음 명령을 사용 하 여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령은 이름이 ' t r u e ' 인 ' sql1 ' 및 Kubernetes 네임 스페이스 라는 SQL 관리 되는 인스턴스를 만들었다고 가정 합니다.  다른 네임 스페이스/n e t/SQL 관리 되는 인스턴스 이름을 사용한 경우 ' arc ' 및 ' sqlmi '를 사용자의 이름으로 바꿀 수 있습니다.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행 하 여 특정 pod의 생성 상태를 확인할 수도 있습니다.  이 기능은 문제를 해결 하는 데 특히 유용 합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

만든 문제가 발생 하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Arc 사용 SQL Managed Instance에 연결](connect-managed-instance.md)
