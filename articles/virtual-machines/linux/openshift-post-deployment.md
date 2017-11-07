---
title: "Azure의 OpenShift 배포 후 작업 | Microsoft Docs"
description: "OpenShift 배포 후 작업"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 12e6785358f5f412326418b0c64eeaeabdaa3b5f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="post-deployment-tasks"></a>배포 후 작업

OpenShift 클러스터를 배포한 후 구성할 수 있는 추가 항목이 있습니다. 이 문서에서는 다음 내용을 설명합니다.

- AAD(Azure Active Directory)를 사용하여 Single Sign On 구성
- OpenShift를 모니터링하도록 OMS 구성
- 메트릭 및 로깅 구성

## <a name="single-sign-on-using-aad"></a>AAD를 사용한 Single Sign On

AAD를 인증에 사용하려면 먼저 Azure AD 앱 등록을 만들어야 합니다. 이 프로세스는 앱 등록 만들기와 권한 구성이라는 두 단계로 이루어집니다.

### <a name="create-app-registration"></a>앱 등록 만들기

Azure CLI를 사용하여 앱 등록과 GUI(Portal)를 만들어서 권한을 설정합니다. 앱 등록을 만들려면 다섯 가지 정보가 필요합니다.

- 표시 이름: 앱 등록 이름(예: OCPAzureAD)
- 홈페이지: OpenShift 콘솔 URL(예: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- 식별자 URI: OpenShift 콘솔 URL(예: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- 회신 URL: 마스터 공용 URL 및 앱 등록 이름(예: https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- 암호: 보안 암호(강력한 암호 사용)

다음 예제는 위의 정보를 사용하여 앱 등록을 만듭니다.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

명령이 성공하면 다음과 유사한 JSON 출력이 표시됩니다.

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

이후 단계를 위해 명령에서 반환되는 appId 속성을 기록해 둡니다.

**Azure Portal**에서:

1.  **Azure Active Directory** --> **앱 등록**을 선택합니다.
2.  앱 등록(예: OCPAzureAD)을 검색합니다.
3.  결과에서 앱 등록을 클릭합니다.
4.  설정 블레이드에서 **필요한 권한**을 선택합니다.
5.  필요한 권한 블레이드에서 **추가**를 클릭합니다.

  ![앱 등록](media/openshift-post-deployment/app-registration.png)

6.  1단계: API 선택을 클릭한 다음 **Windows Azure Active Directory(Microsoft.Azure.ActiveDirectory)**를 클릭하고 아래쪽의 **선택**을 클릭합니다.

  ![앱 등록 API 선택](media/openshift-post-deployment/app-registration-select-api.png)

7.  2단계: 권한을 선택하고 **위임된 권한**에서 **로그인 및 사용자 프로필 읽기**를 선택하고 **선택**을 클릭합니다.

  ![앱 등록 액세스](media/openshift-post-deployment/app-registration-access.png)

8.  **완료**를 클릭합니다.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Azure AD 인증을 위한 OpenShift 구성

Azure AD를 인증 공급자로 사용하도록 OpenShift를 구성하려면 모든 마스터 노드에서 **/etc/origin/master/master-config.yaml** 파일을 편집해야 합니다.

테넌트 ID는 다음 CLI 명령을 사용하여 찾을 수 있습니다.

```azurecli
az account show
```

yaml 파일에서 다음 줄을 찾습니다.

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

위의 줄 바로 뒤에 다음 줄을 삽입합니다.

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

테넌트 ID는 다음 CLI 명령을 사용하여 찾을 수 있습니다. ```az account show```

모든 마스터 노드에서 OpenShift 마스터 서비스를 다시 시작합니다.

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**여러 마스터가 있는 OpenShift Container Platform**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**단일 마스터가 있는 OpenShift Container Platform**

```bash
sudo systemctl restart atomic-openshift-master
```

OpenShift 콘솔에 htpasswd_auth 및 **[앱 등록]**이라는 두 가지 인증 옵션이 표시됩니다.

## <a name="monitor-openshift-with-oms"></a>OMS로 OpenShift 모니터링

OMS로 OpenShift를 모니터링하려면 다음 두 가지 옵션을 사용합니다. VM 호스트 또는 OMS 컨테이너에 OMS 에이전트 설치. 이 문서에서는 OMS 컨테이너 배포에 대한 지침을 제공합니다.

## <a name="create-an-openshift-project-for-oms-and-set-user-access"></a>OMS용 OpenShift 프로젝트 만들기 및 사용자 액세스 설정

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-daemon-set-yaml-file"></a>디먼 집합 yaml 파일 만들기

ocp-omsagent.yml이라는 파일을 만듭니다.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-secret-yaml-file"></a>비밀 yaml 파일 만들기

비밀 yaml 파일을 만들려면 OMS 작업 영역 ID와 OMS 작업 영역 공유 키라는 두 가지 정보가 필요합니다. 

샘플 ocp-secret.yml 파일 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

wsid_data를 Base64로 인코딩된 OMS 작업 영역 ID로 바꾸고 key_data를 Base64로 인코딩된 OMS 작업 영역 공유 키로 바꿉니다.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-secret-and-daemon-set"></a>비밀 및 디먼 집합 만들기

비밀 파일 배포

```bash
oc create -f ocp-secret.yml
```

OMS 에이전트 디먼 집합 배포

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>메트릭 및 로깅 구성

OCP(OpenShift Container Platform) Resource Manager 템플릿에는 메트릭 및 로깅을 활성화하는 입력 매개 변수가 제공됩니다. OpenShift Container Platform 마켓플레이스 제품 및 OpenShift Origin Resource Manager 템플릿에는 제공되지 않습니다.

OCP Resource Manager 템플릿이 사용되고 메트릭 및 로깅이 설치 시 활성화되지 않았거나 OCP 마켓플레이스 제품이 사용된 경우 사후에 쉽게 활성화할 수 있습니다. OpenShift Origin Resource Manager 템플릿을 사용하는 경우 사전 작업이 필요합니다.

### <a name="openshift-origin-template-pre-work"></a>OpenShift Origin 템플릿 사전 작업

포트 2200을 사용하여 첫 번째 마스터 노드에 SSH 실행

예제

```bash
ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
```

**/etc/ansible/hosts 파일**을 편집하고 ID 공급자 섹션 뒤에 다음 줄을 추가합니다(# Enable HTPasswdPasswordIdentityProvider).

```yaml
# Setup metrics
openshift_hosted_metrics_deploy=false
openshift_metrics_cassandra_storage_type=dynamic
openshift_metrics_start_cluster=true
openshift_metrics_hawkular_nodeselector={"type":"infra"}
openshift_metrics_cassandra_nodeselector={"type":"infra"}
openshift_metrics_heapster_nodeselector={"type":"infra"}
openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

# Setup logging
openshift_hosted_logging_deploy=false
openshift_hosted_logging_storage_kind=dynamic
openshift_logging_fluentd_nodeselector={"logging":"true"}
openshift_logging_es_nodeselector={"type":"infra"}
openshift_logging_kibana_nodeselector={"type":"infra"}
openshift_logging_curator_nodeselector={"type":"infra"}
openshift_master_logging_public_url=https://kibana.$ROUTING
```

$ROUTING을 동일한 **/etc/ansible/hosts** 파일에서 **openshift_master_default_subdomain** 옵션에 사용된 문자열로 대체합니다.

### <a name="azure-cloud-provider-in-use"></a>사용 중인 Azure Cloud Provider

첫 번째 마스터 노드(원점) 또는 요새 노드(OCP)에서 배포 중에 제공된 자격 증명을 사용하여 SSH를 실행합니다. 다음 명령을 실행합니다.

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>사용되지 않는 Azure Cloud Provider

첫 번째 마스터 노드(원점) 또는 요새 노드(OCP)에서 배포 중에 제공된 자격 증명을 사용하여 SSH를 실행합니다. 다음 명령을 실행합니다.

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>다음 단계

- [OpenShift Container Platform 시작](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [OpenShift Origin 시작](https://docs.openshift.org/latest/getting_started/index.html)