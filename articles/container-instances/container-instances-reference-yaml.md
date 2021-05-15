---
title: 컨테이너 그룹에 대한 YAML 참조
description: 컨테이너 그룹 구성에 사용되며 Azure Container Instances에서 지원되는 YAML 파일에 대한 참조입니다.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084763"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 참조: Azure Container Instances

이 문서에서는 [컨테이너 그룹](container-instances-container-groups.md) 구성에 사용되며 Azure Container Instances에서 지원되는 YAML 파일의 구문과 속성을 살펴봅니다. YAML 파일은 Azure CLI에서 [az container create][az-container-create] 명령에 그룹 구성을 입력하는 데 사용됩니다. 

YAML 파일을 사용하면 편리하게 재현 가능한 배포에 필요한 컨테이너 그룹을 구성할 수 있습니다. [Resource Manager 템플릿](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) 또는 Azure Container Instances SDK를 사용하여 컨테이너 그룹을 만들거나 업데이트하는 방법의 간단한 대안이 되기도 합니다.

> [!NOTE]
> 이 참조는 Azure Container Instances REST API 버전 `2019-12-01`에 대한 YAML 파일에 적용됩니다.

## <a name="schema"></a>스키마 

YAML 파일 스키마는 다음과 같습니다. 키 속성을 강조 표시하는 주석이 포함되어 있습니다. 이 스키마의 속성에 대한 설명은 [속성 값](#property-values) 섹션을 참조하세요.

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
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
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>속성 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 컨테이너 그룹 이름입니다. |
|  apiVersion | enum | 예 | 2018-10-01 |
|  위치 | 문자열 | No | 리소스 위치입니다. |
|  tags | object | 예 | 리소스 태그입니다. |
|  identity | object | 예 | 구성된 경우 컨테이너 그룹 ID입니다. - [ContainerGroupIdentity 개체](#containergroupidentity-object) |
|  properties | object | 예 | [ContainerGroupProperties 개체](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  type | enum | 예 | 컨테이너 그룹에 사용되는 ID 유형입니다. ‘SystemAssigned, UserAssigned’ 유형에는 암시적으로 만들어진 ID와 사용자 할당 ID 세트가 모두 포함되어 있습니다. ‘None’ 유형은 컨테이너 그룹에서 모든 ID를 제거합니다. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | 예 | 컨테이너 그룹과 연결된 사용자 ID 목록입니다. 사용자 ID 사전 키 참조는 Azure Resource Manager 리소스 ID이며 형식은 ‘/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}’입니다. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  컨테이너 | array | 예 | 컨테이너 그룹 내 컨테이너입니다. - [Container 개체](#container-object) |
|  imageRegistryCredentials | array | 예 | 컨테이너 그룹을 만드는 데 사용할 이미지 레지스트리 자격 증명입니다. - [ImageRegistryCredential 개체](#imageregistrycredential-object) |
|  restartPolicy | enum | 예 | 컨테이너 그룹 내 모든 컨테이너에 대한 다시 시작 정책입니다. - `Always` 항상 다시 시작- `OnFailure` 실패 시 다시 시작- `Never` 다시 시작 안 함. - Always, OnFailure, Never |
|  ipAddress | object | 예 | 컨테이너 그룹의 IP 주소 유형입니다. - [IpAddress 개체](#ipaddress-object) |
|  osType | enum | 예 | 컨테이너 그룹의 컨테이너에 필요한 운영 체제 유형입니다. - Windows 또는 Linux |
|  volumes | array | 예 | 이 컨테이너 그룹의 컨테이너에서 탑재할 수 있는 볼륨의 목록입니다. - [Volume 개체](#volume-object) |
|  진단 | object | 예 | 컨테이너 그룹에 대한 진단 정보입니다. - [ContainerGroupDiagnostics 개체](#containergroupdiagnostics-object) |
|  networkProfile | object | 예 | 컨테이너 그룹에 대한 네트워크 프로필 정보입니다. - [ContainerGroupNetworkProfile 개체](#containergroupnetworkprofile-object) |
|  dnsConfig | object | 예 | 컨테이너 그룹에 대한 DNS 구성 정보입니다. - [DnsConfiguration 개체](#dnsconfiguration-object) |
| sku | enum | 예 | 컨테이너 그룹의 SKU입니다. - 표준 또는 전용 |
| encryptionProperties | object | 예 | 컨테이너 그룹의 암호화 속성입니다. - [EncryptionProperties 개체](#encryptionproperties-object) | 
| initContainers | array | 예 | 컨테이너 그룹의 init 컨테이너입니다. - [InitContainerDefinition 개체](#initcontainerdefinition-object) |




### <a name="container-object"></a>Container 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 컨테이너 인스턴스의 사용자 제공 이름입니다. |
|  properties | object | 예 | 컨테이너 인스턴스 속성입니다. - [ContainerProperties 개체](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  서버 | 문자열 | 예 | “http” 및 “https” 같은 프로토콜이 없는 Docker 이미지 레지스트리 서버입니다. |
|  사용자 이름 | 문자열 | 예 | 프라이빗 레지스트리의 사용자 이름입니다. |
|  password | 문자열 | No | 프라이빗 레지스트리의 암호입니다. |




### <a name="ipaddress-object"></a>IpAddress 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  ports | array | 예 | 컨테이너 그룹에서 노출되는 포트 목록입니다. - [Port 개체](#port-object) |
|  type | enum | 예 | IP가 공용 인터넷 또는 개인 VNET에 노출되는지 여부를 지정합니다. - Public 또는 Private |
|  ip | 문자열 | No | 공용 인터넷에 노출되는 IP입니다. |
|  dnsNameLabel | 문자열 | No | IP에 대한 DNS 이름 레이블입니다. |




### <a name="volume-object"></a>Volume 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 볼륨의 이름입니다. |
|  azureFile | object | 예 | Azure 파일 볼륨입니다. - [AzureFileVolume 개체](#azurefilevolume-object) |
|  emptyDir | object | 예 | 빈 디렉터리 볼륨입니다. |
|  secret | object | 예 | 비밀 볼륨입니다. |
|  gitRepo | object | 예 | Git 리포지토리 볼륨입니다. - [GitRepoVolume 개체](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | 예 | 컨테이너 그룹 로그 분석 정보입니다. - [LogAnalytics 개체](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  id | 문자열 | 예 | 네트워크 프로필 식별자입니다. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | 예 | 컨테이너 그룹에 대한 DNS 서버입니다. - string |
|  searchDomains | 문자열 | No | 컨테이너 그룹의 호스트 이름 조회에 대한 DNS 검색 도메인입니다. |
|  옵션 | 문자열 | No | 컨테이너 그룹에 대한 DNS 옵션입니다. |


### <a name="encryptionproperties-object"></a>EncryptionProperties 개체

| 이름  | 유형  | 필수  | 값 |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | 문자열    | 예   | keyvault 기준 URL입니다. |
| keyName   | 문자열    | 예   | 암호화 키 이름입니다. |
| keyVersion    | 문자열    | 예   | 암호화 키 버전입니다. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition 개체

| 이름  | 유형  | 필수  | 값 |
|  ---- | ---- | ---- | ---- |
| name  | 문자열 |  예 | init 컨테이너의 이름입니다. |
| properties    | object    | 예   | init 컨테이너의 속성입니다. - [InitContainerPropertiesDefinition 개체](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  이미지 | 문자열 | 예 | 컨테이너 인스턴스를 만드는 데 사용되는 이미지의 이름입니다. |
|  명령을 사용합니다. | array | 예 | 실행 형식으로 된 컨테이너 인스턴스 내 실행 명령입니다. - string |
|  ports | array | 예 | 컨테이너 인스턴스에서 노출되는 포트입니다. - [ContainerPort 개체](#containerport-object) |
|  environmentVariables | array | 예 | 컨테이너 인스턴스에서 설정할 환경 변수입니다. - [EnvironmentVariable 개체](#environmentvariable-object) |
|  리소스 | object | 예 | 컨테이너 인스턴스의 리소스 요구 사항입니다. - [ResourceRequirements 개체](#resourcerequirements-object) |
|  volumeMounts | array | 예 | 컨테이너 인스턴스에 사용할 수 있는 볼륨 탑재입니다. - [VolumeMount 개체](#volumemount-object) |
|  livenessProbe | object | 예 | 활동성 프로브입니다. - [ContainerProbe 개체](#containerprobe-object) |
|  readinessProbe | object | 예 | 준비 상태 프로브입니다. - [ContainerProbe 개체](#containerprobe-object) |




### <a name="port-object"></a>Port 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 예 | 포트와 연결된 프로토콜입니다. - TCP 또는 UDP |
|  포트 | 정수 | 예 | 포트 번호. |




### <a name="azurefilevolume-object"></a>AzureFileVolume 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  shareName | 문자열 | 예 | 볼륨으로 탑재할 Azure 파일 공유의 이름입니다. |
|  readOnly | boolean | 예 | 볼륨으로 탑재된 Azure 파일 공유가 읽기 전용인지 여부를 나타내는 플래그입니다. |
|  storageAccountName | 문자열 | 예 | Azure 파일 공유가 포함된 스토리지 계정의 이름입니다. |
|  storageAccountKey | 문자열 | No | Azure 파일 공유에 액세스하는 데 사용되는 스토리지 계정 액세스 키입니다. |




### <a name="gitrepovolume-object"></a>GitRepoVolume 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  directory | 문자열 | No | 대상 디렉터리 이름입니다. ‘..’를 포함하거나 ‘..’로 시작해서는 안 됩니다.  ‘.’가 제공되면 볼륨 디렉터리가 git 리포지토리가 됩니다.  그렇지 않고 지정되면 볼륨에는 지정된 이름의 하위 디렉터리에 git 리포지토리가 포함됩니다. |
|  리포지토리 | 문자열 | 예 | 리포지토리 URL |
|  수정 | 문자열 | No | 지정된 수정 버전에 대한 해시를 커밋합니다. |



### <a name="loganalytics-object"></a>LogAnalytics 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  workspaceId | 문자열 | 예 | 로그 분석에 대한 작업 영역 ID입니다. |
|  workspaceKey | 문자열 | 예 | 로그 분석에 대한 작업 영역 키입니다. |
|  logType | enum | 예 | 사용할 로그 유형입니다. - ContainerInsights 또는 ContainerInstanceLogs |
|  metadata | object | 예 | 로그 분석에 대한 메타데이터입니다. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition 개체

| 이름  | 유형  | 필수  | 값 |
|  ---- | ---- | ---- | ---- |
| 이미지 | 문자열    | No    | init 컨테이너의 이미지입니다. |
| 명령을 사용합니다.   | array | 예    | 실행 형식으로 된 init 컨테이너 내 실행 명령입니다. - string |
| environmentVariables | array  | 예 |init 컨테이너에서 설정할 환경 변수입니다. - [EnvironmentVariable 개체](#environmentvariable-object)
| volumeMounts |array   | 예    | init 컨테이너에 사용할 수 있는 볼륨 탑재입니다. - [VolumeMount 개체](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 예 | 포트와 연결된 프로토콜입니다. - TCP 또는 UDP |
|  포트 | 정수 | 예 | 컨테이너 그룹 내에서 노출되는 포트 번호입니다. |




### <a name="environmentvariable-object"></a>EnvironmentVariable 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 환경 변수의 이름입니다. |
|  값 | 문자열 | No | 환경 변수의 값입니다. |
|  secureValue | 문자열 | No | 보안 환경 변수 값입니다. |




### <a name="resourcerequirements-object"></a>ResourceRequirements 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  requests | object | 예 | 이 컨테이너 인스턴스의 리소스 요청입니다. - [ResourceRequests 개체](#resourcerequests-object) |
|  제한 | object | 예 | 이 컨테이너 인스턴스의 리소스 한도입니다. - [ResourceLimits 개체](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | 예 | 볼륨 탑재의 이름입니다. |
|  mountPath | 문자열 | 예 | 볼륨이 탑재되어야 하는 컨테이너 내 경로입니다. 콜론(:)은 포함되면 안 됩니다. |
|  readOnly | boolean | 예 | 볼륨 탑재가 읽기 전용인지 여부를 나타내는 플래그입니다. |




### <a name="containerprobe-object"></a>ContainerProbe 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  exec | object | 예 | 검색할 실행 명령입니다. - [ContainerExec 개체](#containerexec-object) |
|  httpGet | object | 예 | 검색할 Http Get 설정입니다. - [ContainerHttpGet 개체](#containerhttpget-object) |
|  initialDelaySeconds | 정수 | 예 | 초기 지연(초)입니다. |
|  periodSeconds | 정수 | 예 | 기간(초)입니다. |
|  failureThreshold | 정수 | 예 | 실패 임계값입니다. |
|  successThreshold | 정수 | 예 | 성공 임계값입니다. |
|  timeoutSeconds | 정수 | 예 | 시간 제한(초)입니다. |




### <a name="resourcerequests-object"></a>ResourceRequests 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 예 | 이 컨테이너 인스턴스의 메모리 요청(GB)입니다. |
|  cpu | number | 예 | 이 컨테이너 인스턴스의 CPU 요청입니다. |
|  gpu | object | 예 | 이 컨테이너 인스턴스의 GPU 요청입니다. - [GpuResource 개체](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 예 | 이 컨테이너 인스턴스의 메모리 한도(GB)입니다. |
|  cpu | number | 예 | 이 컨테이너 인스턴스의 CPU 한도입니다. |
|  gpu | object | 예 | 이 컨테이너 인스턴스의 GPU 한도입니다. - [GpuResource 개체](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  명령을 사용합니다. | array | 예 | 컨테이너 내 실행 명령입니다. - string |




### <a name="containerhttpget-object"></a>ContainerHttpGet 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  path | 문자열 | No | 검색할 경로입니다. |
|  포트 | 정수 | 예 | 검색할 포트 번호입니다. |
|  scheme | enum | 예 | 체계입니다. - http 또는 https |




### <a name="gpuresource-object"></a>GpuResource 개체

|  이름 | 유형 | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  count | 정수 | 예 | GPU 리소스 수입니다. |
|  sku | enum | 예 | GPU 리소스 SKU입니다. - K80, P100, V100 |


## <a name="next-steps"></a>다음 단계

자습서 [YAML 파일을 사용하여 다중 컨테이너 그룹 배포](container-instances-multi-container-yaml.md)를 참조하세요.

YAML 파일을 사용하여 [가상 네트워크](container-instances-vnet.md)에서 컨테이너 그룹을 배포하거나 [외부 볼륨을 탑재](container-instances-volume-azure-files.md)하는 예제를 참조하세요.

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

