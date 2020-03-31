---
title: 컨테이너 그룹에 대한 YAML 참조
description: 컨테이너 그룹을 구성하기 위해 Azure 컨테이너 인스턴스에서 지원하는 YAML 파일에 대한 참조
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896565"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 참조: Azure 컨테이너 인스턴스

이 문서에서는 [컨테이너 그룹을](container-instances-container-groups.md)구성하기 위해 Azure 컨테이너 인스턴스에서 지원하는 YAML 파일에 대한 구문 및 속성을 다룹니다. YAML 파일을 사용하여 Azure CLI에서 [az 컨테이너 만들기][az-container-create] 명령에 그룹 구성을 입력합니다. 

YAML 파일은 재현 가능한 배포를 위해 컨테이너 그룹을 구성하는 편리한 방법입니다. [리소스 관리자 템플릿](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) 또는 Azure 컨테이너 인스턴스 SDK를 사용하여 컨테이너 그룹을 만들거나 업데이트하는 대신 간결한 대안입니다.

> [!NOTE]
> 이 참조는 Azure 컨테이너 인스턴스 REST API `2018-10-01`버전에 대한 YAML 파일에 적용됩니다.

## <a name="schema"></a>스키마 

YAML 파일에 대한 스키마는 주요 속성을 강조 표시하는 주석을 포함하여 다음과 같습니다. 이 스키마의 속성에 대한 설명은 [속성 값](#property-values) 섹션을 참조하십시오.

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

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.컨테이너 인스턴스/컨테이너개체그룹

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | yes | 컨테이너 그룹의 이름입니다. |
|  apiVersion | enum | yes | 2018-10-01 |
|  위치 | 문자열 | 예 | 리소스 위치입니다. |
|  tags | object | 예 | 리소스 태그입니다. |
|  ID | object | 예 | 구성된 경우 컨테이너 그룹의 ID입니다. - [컨테이너그룹Identity 개체](#ContainerGroupIdentity) |
|  properties | object | yes | [컨테이너그룹속성 개체](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>컨테이너그룹Identity 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  type | enum | 예 | 컨테이너 그룹에 사용되는 ID 유형입니다. 'SystemAssigned, UserAssigned' 형식에는 암시적으로 생성된 ID와 사용자 할당된 ID 집합이 모두 포함됩니다. '없음' 형식은 컨테이너 그룹에서 ID를 제거합니다. - 시스템 할당, 사용자 할당, 시스템 할당, 사용자 할당, 없음 |
|  userAssignedIdentities | object | 예 | 컨테이너 그룹과 연결된 사용자 ID 목록입니다. 사용자 ID 사전 키 참조는 '/구독/{구독Id}/리소스 그룹/{리소스그룹이름}/공급자/Microsoft.ManagedIdentity/userAssignedIdentity/{identityName}의 형태로 Azure 리소스 관리자 리소스 ID입니다. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>컨테이너그룹속성 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  컨테이너 | array | yes | 컨테이너 그룹 내의 컨테이너입니다. - [컨테이너 개체](#Container) |
|  이미지레지스트리 자격 증명 | array | 예 | 컨테이너 그룹이 만들어지는 이미지 레지스트리 자격 증명입니다. - [이미지레지스트리자격증명 개체](#ImageRegistryCredential) |
|  다시 시작정책 | enum | 예 | 컨테이너 그룹 내의 모든 컨테이너에 대한 정책을 다시 시작합니다. - `Always`항상 다시 `OnFailure` 시작 - 실패 `Never` 시 다시 시작 - 다시 시작하지 마십시오. - 항상, 실패, 결코 |
|  ipAddress | object | 예 | 컨테이너 그룹의 IP 주소 유형입니다. - [IpAddress 개체](#IpAddress) |
|  osType | enum | yes | 컨테이너 그룹의 컨테이너에 필요한 운영 체제 유형입니다. - 윈도우 또는 리눅스 |
|  volumes | array | 예 | 이 컨테이너 그룹의 컨테이너에서 사용할 수 있는 볼륨 목록입니다. - [볼륨 개체](#Volume) |
|  진단 | object | 예 | 컨테이너 그룹에 대한 진단 정보입니다. - [컨테이너그룹진단 대상](#ContainerGroupDiagnostics) |
|  네트워크 프로필 | object | 예 | 컨테이너 그룹에 대한 네트워크 프로필 정보입니다. - [컨테이너그룹네트워크프로파일 개체](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | 예 | 컨테이너 그룹에 대한 DNS 구성 정보입니다. - [Dns구성 개체](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>컨테이너 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | yes | 컨테이너 인스턴스의 사용자가 제공한 이름입니다. |
|  properties | object | yes | 컨테이너 인스턴스의 속성입니다. - [컨테이너속성 개체](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>이미지레지스트리자격증명 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  서버 | 문자열 | yes | "http" 및 "https"와 같은 프로토콜이 없는 Docker 이미지 레지스트리 서버입니다. |
|  사용자 이름 | 문자열 | yes | 개인 레지스트리의 사용자 이름입니다. |
|  password | 문자열 | 예 | 개인 레지스트리의 암호입니다. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  ports | array | yes | 컨테이너 그룹에 노출된 포트 목록입니다. - [포트 개체](#Port) |
|  type | enum | yes | IP가 공용 인터넷 또는 개인 VNET에 노출되는지 지정합니다. - 공개 또는 비공개 |
|  ip | 문자열 | 예 | 공용 인터넷에 노출된 IP입니다. |
|  dnsName레이블 | 문자열 | 예 | IP의 Dns 이름 레이블입니다. |


<a id="Volume" />

### <a name="volume-object"></a>볼륨 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | yes | 볼륨의 이름입니다. |
|  azureFile | object | 예 | Azure 파일 볼륨입니다. - [AzureFileVolume 개체](#AzureFileVolume) |
|  emptyDir | object | 예 | 빈 디렉터리 볼륨입니다. |
|  secret | object | 예 | 비밀 볼륨입니다. |
|  gitRepo | object | 예 | git 리포지토리 볼륨입니다. - [GitRepoVolume 개체](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>컨테이너그룹진단 대상

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  로그애그 애널리틱스 | object | 예 | 컨테이너 그룹 로그 분석 정보입니다. - [로그애널리틱스 개체](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>컨테이너그룹네트워크프로파일 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  id | 문자열 | yes | 네트워크 프로필의 식별자입니다. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Dns구성 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  네임서버 | array | yes | 컨테이너 그룹에 대한 DNS 서버입니다. - 문자열 |
|  검색 도메인 | 문자열 | 예 | 컨테이너 그룹의 호스트 이름 조회에 대 한 DNS 검색 도메인입니다. |
|  옵션 | 문자열 | 예 | 컨테이너 그룹에 대한 DNS 옵션입니다. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>컨테이너속성 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  이미지 | 문자열 | yes | 컨테이너 인스턴스를 만드는 데 사용되는 이미지의 이름입니다. |
|  command | array | 예 | exec 형식의 컨테이너 인스턴스 내에서 실행되는 명령입니다. - 문자열 |
|  ports | array | 예 | 컨테이너 인스턴스의 노출된 포트입니다. - [컨테이너포트 개체](#ContainerPort) |
|  environmentVariables | array | 예 | 컨테이너 인스턴스에서 설정할 환경 변수입니다. - [환경변수 개체](#EnvironmentVariable) |
|  리소스 | object | yes | 컨테이너 인스턴스의 리소스 요구 사항입니다. - [리소스요구 사항 개체](#ResourceRequirements) |
|  볼륨마운트 | array | 예 | 컨테이너 인스턴스에서 사용할 수 있는 볼륨 마운트입니다. - [볼륨마운트 개체](#VolumeMount) |
|  생생감프로브 | object | 예 | 생생도 프로브. - [컨테이너 프로브 개체](#ContainerProbe) |
|  준비 프로브 | object | 예 | 준비 프로브입니다. - [컨테이너 프로브 개체](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>포트 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 예 | 포트와 연결된 프로토콜입니다. - TCP 또는 UDP |
|  포트 | integer | yes | 포트 번호. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  공유 이름 | 문자열 | yes | 볼륨으로 탑재할 Azure File 공유의 이름입니다. |
|  readOnly | boolean | 예 | 볼륨으로 탑재된 Azure File 공유 여부를 나타내는 플래그는 읽기 전용입니다. |
|  storageAccountName | 문자열 | yes | Azure File 공유를 포함하는 저장소 계정의 이름입니다. |
|  storageAccountKey | 문자열 | 예 | Azure File 공유에 액세스하는 데 사용되는 저장소 계정 액세스 키입니다. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  directory | 문자열 | 예 | 대상 디렉터리 이름입니다. '..'를 포함하거나 시작해서는 안 됩니다.  '.'가 제공되면 볼륨 디렉터리는 git 리포지토리가 됩니다.  그렇지 않으면 지정된 경우 볼륨에는 지정된 이름이 있는 하위 디렉터리에서 git 리포지토리가 포함됩니다. |
|  리포지토리 | 문자열 | yes | 리포지토리 URL |
|  revision | 문자열 | 예 | 지정된 개정에 대해 해시를 커밋합니다. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>로그애널리틱스 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  workspaceId | 문자열 | yes | 로그 분석을 위한 작업 영역 ID |
|  작업 공간키 | 문자열 | yes | 로그 분석을 위한 작업 영역 키 |
|  로그 유형 | enum | 예 | 사용할 로그 유형입니다. - 컨테이너 인사이트 또는 컨테이너인스턴스로그 |
|  metadata | object | 예 | 로그 분석을 위한 메타데이터. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>컨테이너포트 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 예 | 포트와 연결된 프로토콜입니다. - TCP 또는 UDP |
|  포트 | integer | yes | 컨테이너 그룹 내에 노출된 포트 번호입니다. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>환경변수 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | yes | 환경 변수의 이름입니다. |
|  value | 문자열 | 예 | 환경 변수의 값입니다. |
|  보안 값 | 문자열 | 예 | 보안 환경 변수의 값입니다. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>리소스요구 사항 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  requests | object | yes | 이 컨테이너 인스턴스의 리소스 요청입니다. - [리소스 요청 개체](#ResourceRequests) |
|  제한 | object | 예 | 이 컨테이너 인스턴스의 리소스 제한입니다. - [리소스제한 개체](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>볼륨마운트 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  name | 문자열 | yes | 볼륨 마운트의 이름입니다. |
|  마운트 패스 | 문자열 | yes | 볼륨을 마운트해야 하는 컨테이너 내의 경로입니다. 콜론(:)을 포함해서는 안 됩니다. |
|  readOnly | boolean | 예 | 볼륨 마운트가 읽기 전용인지 여부를 나타내는 플래그입니다. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>컨테이너 프로브 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  exec | object | 예 | 프로브할 실행 명령 - [ContainerExec 개체](#ContainerExec) |
|  httpGet | object | 예 | http Get 설정 프로브 - [ContainerHttpGet 개체](#ContainerHttpGet) |
|  초기 지연 초 | integer | 예 | 초기 지연 초입니다. |
|  기간 초 | integer | 예 | 마침표 초입니다. |
|  오류 임계값 | integer | 예 | 실패 임계값입니다. |
|  성공임계값 | integer | 예 | 성공 임계값입니다. |
|  시간 시간 시간 초 | integer | 예 | 시간 시간 시간 초입니다. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>리소스 요청 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  메모리인비그 | number | yes | 이 컨테이너 인스턴스의 GB에 있는 메모리 요청입니다. |
|  cpu | number | yes | 이 컨테이너 인스턴스의 CPU 요청입니다. |
|  Gpu | object | 예 | 이 컨테이너 인스턴스의 GPU 요청입니다. - [GpuResource 개체](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>리소스제한 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  메모리인비그 | number | 예 | 이 컨테이너 인스턴스의 GB의 메모리 제한입니다. |
|  cpu | number | 예 | 이 컨테이너 인스턴스의 CPU 제한입니다. |
|  Gpu | object | 예 | 이 컨테이너 인스턴스의 GPU 제한입니다. - [GpuResource 개체](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>컨테이너엑섹 오브젝트

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  command | array | 예 | 컨테이너 내에서 실행할 명령입니다. - 문자열 |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>컨테이너HttpGet 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  경로 | 문자열 | 예 | 프로브 경로입니다. |
|  포트 | integer | yes | 조사할 포트 번호입니다. |
|  scheme | enum | 예 | 체계입니다. - http 또는 https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource 개체

|  이름 | Type | 필수 | 값 |
|  ---- | ---- | ---- | ---- |
|  count | integer | yes | GPU 리소스의 수입니다. |
|  sku | enum | yes | GPU 리소스의 SKU입니다. - K80, P100, V100 |


## <a name="next-steps"></a>다음 단계

[YAML 파일을 사용하여 다중 컨테이너 그룹 배포](container-instances-multi-container-yaml.md)자습서를 참조하십시오.

YAML 파일을 사용하여 [가상 네트워크에](container-instances-vnet.md) 컨테이너 그룹을 배포하거나 [외부 볼륨을 탑재하는 예제를](container-instances-volume-azure-files.md)참조하십시오.

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

