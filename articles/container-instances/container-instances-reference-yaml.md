---
title: 컨테이너 그룹에 대 한 YAML 참조
description: 컨테이너 그룹을 구성 하는 Azure Container Instances에서 지원 되는 YAML 파일에 대 한 참조
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: be78c7d498187486a1502da17faa2b8faa5a0982
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730529"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 참조: Azure Container Instances

이 문서에서는 [컨테이너 그룹](container-instances-container-groups.md)을 구성 하는 Azure Container Instances에서 지원 되는 yaml 파일에 대 한 구문 및 속성을 설명 합니다. YAML 파일을 사용 하 여 그룹 구성을 Azure CLI의 [az container create][az-container-create] 명령에 입력 합니다. 

YAML 파일은 재현 가능한 배포를 위해 컨테이너 그룹을 구성 하는 편리한 방법입니다. [리소스 관리자 템플릿](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) 또는 Azure Container Instances sdk를 사용 하 여 컨테이너 그룹을 만들거나 업데이트 하는 방법에 대 한 간단한 대안입니다.

> [!NOTE]
> 이 참조는 Azure Container Instances REST API 버전에 대 한 YAML 파일에 적용 됩니다 `2018-10-01` .

## <a name="schema"></a>스키마 

YAML 파일에 대 한 스키마는 키 속성을 강조 표시 하는 주석을 포함 하 여 다음을 수행 합니다. 이 스키마의 속성에 대 한 설명은 [속성 값](#property-values) 섹션을 참조 하십시오.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>속성 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>ContainerInstance/containerGroups 개체

|  Name | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | string | 예 | 컨테이너 그룹의 이름입니다. |
|  apiVersion | enum | 예 | 2018-10-01 |
|  위치 | 문자열 | No | 리소스 위치입니다. |
|  tags | 개체 | 아니요 | 리소스 태그입니다. |
|  identity | 개체 | 아니요 | 구성 된 경우 컨테이너 그룹의 id입니다. - [ContainerGroupIdentity 개체](#containergroupidentity-object) |
|  properties | object | 예 | [ContainerGroupProperties 개체](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity 개체

|  Name | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  형식 | enum | 아니요 | 컨테이너 그룹에 사용 되는 id 유형입니다. ' SystemAssigned 됨, UserAssigned 됨 ' 형식에는 암시적으로 생성 된 id와 사용자 할당 id 집합이 모두 포함 됩니다. ' 없음 ' 유형은 컨테이너 그룹에서 모든 id를 제거 합니다. -SystemAssigned 됨, UserAssigned 됨, SystemAssigned 됨, UserAssigned 안 함 |
|  userAssignedIdentities | 개체 | 아니요 | 컨테이너 그룹과 연결 된 사용자 id 목록입니다. 사용자 id 사전 키 참조는 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} ' 형식으로 리소스 Id를 Azure Resource Manager 됩니다. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties 개체

|  Name | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  컨테이너 | array | 예 | 컨테이너 그룹 내의 컨테이너입니다. - [컨테이너 개체](#container-object) |
|  imageRegistryCredentials | array | 아니요 | 컨테이너 그룹을 만들 때 기준이 되는 이미지 레지스트리 자격 증명입니다. - [ImageRegistryCredential 개체](#imageregistrycredential-object) |
|  restartPolicy | enum | 아니요 | 컨테이너 그룹 내의 모든 컨테이너에 대 한 다시 시작 정책입니다. - `Always``OnFailure`오류 발생 시 항상 다시 시작- `Never` 다시 시작 안 함 -Always, OnFailure, Never |
|  ipAddress | 개체 | 아니요 | 컨테이너 그룹의 IP 주소 유형입니다. - [IpAddress 개체](#ipaddress-object) |
|  osType | enum | 예 | 컨테이너 그룹의 컨테이너에 필요한 운영 체제 유형입니다. -Windows 또는 Linux |
|  volumes | array | 아니요 | 이 컨테이너 그룹의 컨테이너로 탑재할 수 있는 볼륨의 목록입니다. - [볼륨 개체](#volume-object) |
|  진단 | 개체 | 아니요 | 컨테이너 그룹에 대 한 진단 정보입니다. - [ContainerGroupDiagnostics 개체](#containergroupdiagnostics-object) |
|  networkProfile | 개체 | 아니요 | 컨테이너 그룹에 대 한 네트워크 프로필 정보입니다. - [ContainerGroupNetworkProfile 개체](#containergroupnetworkprofile-object) |
|  dnsConfig | 개체 | 아니요 | 컨테이너 그룹에 대 한 DNS 구성 정보입니다. - [DnsConfiguration 개체](#dnsconfiguration-object) |




### <a name="container-object"></a>컨테이너 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 컨테이너 인스턴스의 사용자 제공 이름입니다. |
|  properties | object | 예 | 컨테이너 인스턴스의 속성입니다. - [ContainerProperties 개체](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  서버 | string | 예 | 프로토콜이 없는 Docker 이미지 레지스트리 서버 (예: "http" 및 "https") |
|  사용자 이름 | string | 예 | 개인 레지스트리의 사용자 이름입니다. |
|  password | 문자열 | No | 개인 레지스트리에 대 한 암호입니다. |




### <a name="ipaddress-object"></a>IpAddress 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  ports | array | 예 | 컨테이너 그룹에 노출 된 포트 목록입니다. - [포트 개체](#port-object) |
|  형식 | enum | 예 | IP가 공용 인터넷 또는 개인 VNET에 노출 되는지 여부를 지정 합니다. -공용 또는 개인 |
|  ip | 문자열 | No | 공용 인터넷에 노출 되는 IP입니다. |
|  dnsNameLabel | 문자열 | No | IP의 Dns 이름 레이블입니다. |




### <a name="volume-object"></a>볼륨 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 볼륨의 이름입니다. |
|  azureFile | 개체 | 아니요 | Azure 파일 볼륨입니다. - [AzureFileVolume 개체](#azurefilevolume-object) |
|  emptyDir | 개체 | 아니요 | 빈 디렉터리 볼륨입니다. |
|  secret | 개체 | 아니요 | 비밀 볼륨입니다. |
|  gitRepo | 개체 | 아니요 | Git 리포지토리 볼륨입니다. - [GitRepoVolume 개체](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | 개체 | 아니요 | 컨테이너 그룹 로그 분석 정보입니다. - [LogAnalytics 개체](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  id | 문자열 | 예 | 네트워크 프로필의 식별자입니다. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  이름 서버 | array | 예 | 컨테이너 그룹에 대 한 DNS 서버입니다. -문자열 |
|  searchDomains | 문자열 | No | 컨테이너 그룹에서 호스트 이름 조회에 대 한 DNS 검색 도메인입니다. |
|  옵션 | 문자열 | No | 컨테이너 그룹에 대 한 DNS 옵션입니다. |




### <a name="containerproperties-object"></a>ContainerProperties 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  이미지 | string | 예 | 컨테이너 인스턴스를 만드는 데 사용 되는 이미지의 이름입니다. |
|  명령을 사용합니다. | array | 아니요 | Exec 형식의 컨테이너 인스턴스 내에서 실행할 명령입니다. -문자열 |
|  ports | array | 아니요 | 컨테이너 인스턴스의 노출 된 포트입니다. - [ContainerPort 개체](#containerport-object) |
|  environmentVariables | array | 아니요 | 컨테이너 인스턴스에서 설정할 환경 변수입니다. - [고 environmentvariable 개체](#environmentvariable-object) |
|  리소스 | 개체 | 예 | 컨테이너 인스턴스의 리소스 요구 사항입니다. - [ResourceRequirements 개체](#resourcerequirements-object) |
|  volumeMounts | array | 아니요 | 컨테이너 인스턴스에 사용할 수 있는 볼륨 탑재입니다. - [VolumeMount 개체](#volumemount-object) |
|  livenessProbe | 개체 | 아니요 | 선거의 프로브입니다. - [ContainerProbe 개체](#containerprobe-object) |
|  readinessProbe | 개체 | 아니요 | 준비 프로브입니다. - [ContainerProbe 개체](#containerprobe-object) |




### <a name="port-object"></a>포트 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 아니요 | 포트와 연결 된 프로토콜입니다. -TCP 또는 UDP |
|  포트 | integer | 예 | 포트 번호. |




### <a name="azurefilevolume-object"></a>AzureFileVolume 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  공유 | string | 예 | 볼륨으로 탑재할 Azure 파일 공유의 이름입니다. |
|  readOnly | boolean | 아니요 | 볼륨으로 탑재 된 Azure 파일 공유를 읽기 전용인 지 여부를 나타내는 플래그입니다. |
|  storageAccountName | string | 예 | Azure 파일 공유를 포함 하는 저장소 계정의 이름입니다. |
|  storageAccountKey | 문자열 | No | Azure 파일 공유에 액세스 하는 데 사용 되는 저장소 계정 액세스 키입니다. |




### <a name="gitrepovolume-object"></a>GitRepoVolume 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  directory | 문자열 | No | 대상 디렉터리 이름입니다. '.. '를 포함 하거나 시작 해서는 안 됩니다.  '. '가 제공 되 면 볼륨 디렉터리는 git 리포지토리가 됩니다.  그렇지 않으면 지정 된 경우 볼륨은 지정 된 이름의 하위 디렉터리에 git 리포지토리를 포함 합니다. |
|  리포지토리 | string | 예 | 리포지토리 URL |
|  수정 | 문자열 | No | 지정 된 수정 버전에 대 한 해시를 커밋합니다. |




### <a name="loganalytics-object"></a>LogAnalytics 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | 예 | Log analytics에 대 한 작업 영역 id |
|  workspaceKey | string | 예 | Log analytics에 대 한 작업 영역 키 |
|  logType | enum | 아니요 | 사용할 로그 유형입니다. -ContainerInsights 또는 ContainerInstanceLogs |
|  metadata | 개체 | 아니요 | Log analytics에 대 한 메타 데이터입니다. |




### <a name="containerport-object"></a>ContainerPort 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 아니요 | 포트와 연결 된 프로토콜입니다. -TCP 또는 UDP |
|  포트 | integer | 예 | 컨테이너 그룹 내에서 노출 되는 포트 번호입니다. |




### <a name="environmentvariable-object"></a>고 environmentvariable 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 환경 변수의 이름입니다. |
|  값 | 문자열 | No | 환경 변수의 값입니다. |
|  secureValue | 문자열 | No | 보안 환경 변수의 값입니다. |




### <a name="resourcerequirements-object"></a>ResourceRequirements 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  requests | 개체 | 예 | 이 컨테이너 인스턴스의 리소스 요청입니다. - [ResourceRequests 개체](#resourcerequests-object) |
|  제한 | 개체 | 아니요 | 이 컨테이너 인스턴스의 리소스 제한입니다. - [ResourceLimits 개체](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 볼륨 탑재의 이름입니다. |
|  mountPath | string | 예 | 볼륨이 탑재 되어야 하는 컨테이너 내의 경로입니다. 콜론 (:)을 포함 하지 않아야 합니다. |
|  readOnly | boolean | 아니요 | 볼륨 탑재를 읽기 전용인 지 여부를 나타내는 플래그입니다. |




### <a name="containerprobe-object"></a>ContainerProbe 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  exec | 개체 | 아니요 | [ContainerExec 개체](#containerexec-object) 에 대 한 실행 명령 |
|  httpGet | 개체 | 아니요 | [ContainerHttpGet 개체](#containerhttpget-object) 에 대 한 Http 설정 가져오기 |
|  initialDelaySeconds | integer | 아니요 | 초기 지연 시간 (초)입니다. |
|  periodSeconds | integer | 아니요 | 기간 (초)입니다. |
|  카운터가 failurethreshold | integer | 아니요 | 오류 임계값입니다. |
|  successThreshold | integer | 아니요 | 성공 임계값입니다. |
|  timeoutSeconds | integer | 아니요 | 시간 제한 (초)입니다. |




### <a name="resourcerequests-object"></a>ResourceRequests 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 예 | 이 컨테이너 인스턴스의 메모리 요청 (GB)입니다. |
|  cpu | number | 예 | 이 컨테이너 인스턴스의 CPU 요청입니다. |
|  gpu | 개체 | 아니요 | 이 컨테이너 인스턴스의 GPU 요청입니다. - [GpuResource 개체](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 아니요 | 이 컨테이너 인스턴스의 메모리 한도 (GB)입니다. |
|  cpu | number | 아니요 | 이 컨테이너 인스턴스의 CPU 한도입니다. |
|  gpu | 개체 | 아니요 | 이 컨테이너 인스턴스의 GPU 제한입니다. - [GpuResource 개체](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  명령을 사용합니다. | array | 아니요 | 컨테이너 내에서 실행할 명령입니다. -문자열 |




### <a name="containerhttpget-object"></a>ContainerHttpGet 개체

|  Name | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  경로 | 문자열 | No | 프로브 경로입니다. |
|  포트 | integer | 예 | 프로브 할 포트 번호입니다. |
|  scheme | enum | 아니요 | 체계입니다. -http 또는 https |




### <a name="gpuresource-object"></a>GpuResource 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  count | integer | 예 | GPU 리소스의 수입니다. |
|  sku | enum | 예 | GPU 리소스의 SKU입니다. -K80, P100, V100 |


## <a name="next-steps"></a>다음 단계

[YAML 파일을 사용 하 여 다중 컨테이너 그룹 배포](container-instances-multi-container-yaml.md)자습서를 참조 하세요.

YAML 파일을 사용 하 여 [가상 네트워크](container-instances-vnet.md) 에 컨테이너 그룹을 배포 하거나 [외부 볼륨을 탑재](container-instances-volume-azure-files.md)하는 예제를 참조 하세요.

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

