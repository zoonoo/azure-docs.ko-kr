---
title: Azure에서 OpenShift Container Platform 3.11 자체 관리 Marketplace 제품 배포 | Microsoft Docs
description: Azure에서 OpenShift Container Platform 3.11 자체 관리 Marketplace 제품을 배포 합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d5028ff6378fec5939aee3218071fe6f4eb1e843
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791621"
---
# <a name="configure-prerequisites"></a>필수 조건 구성

Marketplace 제품을 사용 하 여 Azure에서 자체 관리 되는 OpenShift 컨테이너 플랫폼 3.11 클러스터를 배포 하기 전에 몇 가지 필수 구성 요소를 구성 해야 합니다.  Ssh 키 (암호 제외), Azure key vault, key vault 암호 및 서비스 주체를 만드는 방법에 대 한 자세한 내용은 [Openshift 필수 조건](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) 문서를 참조 하세요.

 
## <a name="deploy-using-the-marketplace-offer"></a>Marketplace 제품을 사용 하 여 배포

자체 관리 되는 OpenShift Container Platform 3.11 클러스터를 Azure에 배포 하는 가장 간단한 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)을 사용 하는 것입니다.

이 옵션은 가장 간단 하지만 사용자 지정 기능을 제한 하기도 합니다. Marketplace 제품은 OpenShift Container Platform 3.11.82를 배포 하 고 다음과 같은 구성 옵션을 포함 합니다.

- **마스터 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 마스터 노드입니다.
- **인프라 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 인프라 노드입니다.
- **노드**: 노드 수 (1 ~ 007e; 9)와 인스턴스 유형 (구성 가능).
- **디스크 유형**: 관리 디스크가 사용됩니다.
- **네트워킹**: 새 네트워크 및 기존 네트워크 및 사용자 지정 CIDR 범위에 대 한 지원.
- **CNS**: CNS를 사용하도록 설정할 수 있습니다.
- **메트릭**: Hawkular 메트릭을 사용 하도록 설정할 수 있습니다.
- **로깅**: efk 로깅은 사용 하도록 설정할 수 있습니다.
- **Azure Cloud Provider**: 기본적으로 사용 하도록 설정 되어 있으므로 사용 하지 않도록 설정할 수 있습니다.

Azure Portal 왼쪽 위에서 **리소스 만들기**를 클릭 하 고 검색 상자에 ' openshift container platform '을 입력 하 고 enter 키를 누릅니다.

   ![새 리소스 검색](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

결과 페이지는 목록에서 **Red Hat OpenShift Container Platform 3.11 자체 관리** 를 사용 하 여 열립니다. 

   ![새 리소스 검색 결과](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

제품을 클릭 하 여 제품의 세부 정보를 볼 수 있습니다. 이 제품을 배포 하려면 **만들기**를 클릭 합니다. 필요한 매개 변수를 입력 하는 UI가 나타납니다. 첫 번째 화면은 **기본 사항** 블레이드입니다.

   ![제안 제목 페이지](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**기본 사항**

입력 매개 변수에 대 한 도움말을 보려면 매개 변수 이름 옆에 있는 ***i*** 를 가리킵니다.

입력 매개 변수에 대 한 값을 입력 하 고 **확인을**클릭 합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| VM 관리 사용자 이름 | 모든 VM 인스턴스에서 만들 관리자 사용자 |
| 관리 사용자에 대 한 SSH 공개 키 | VM에 로그인 하는 데 사용 되는 SSH 공개 키-암호가 없어야 합니다. |
| Subscription | 클러스터를 배포할 Azure 구독 |
| 리소스 그룹 | 새 리소스 그룹을 만들거나 클러스터 리소스에 대 한 기존 빈 리소스 그룹을 선택 합니다. |
| 위치 | 클러스터를 배포할 Azure 지역 |

   ![제안 기본 사항 블레이드](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**인프라 설정**

입력 매개 변수에 대 한 값을 입력 하 고 **확인을**클릭 합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| OCP 클러스터 이름 접두사 | 모든 노드에 대 한 호스트 이름을 구성 하는 데 사용 되는 클러스터 접두사입니다. 1 ~ 007e; 20 자 |
| 마스터 노드 크기 | 기본 VM 크기를 그대로 적용 하거나 **크기 변경** 을 클릭 하 여 다른 vm 크기를 선택 합니다.  작업 부하에 적절 한 VM 크기를 선택 합니다. |
| 인프라 노드 크기 | 기본 VM 크기를 그대로 적용 하거나 **크기 변경** 을 클릭 하 여 다른 vm 크기를 선택 합니다.  작업 부하에 적절 한 VM 크기를 선택 합니다. |
| 응용 프로그램 노드 수 | 기본 VM 크기를 그대로 적용 하거나 **크기 변경** 을 클릭 하 여 다른 vm 크기를 선택 합니다.  작업 부하에 적절 한 VM 크기를 선택 합니다. |
| 응용 프로그램 노드 크기 | 기본 VM 크기를 그대로 적용 하거나 **크기 변경** 을 클릭 하 여 다른 vm 크기를 선택 합니다.  작업 부하에 적절 한 VM 크기를 선택 합니다. |
| 요새 호스트 크기 | 기본 VM 크기를 그대로 적용 하거나 **크기 변경** 을 클릭 하 여 다른 vm 크기를 선택 합니다.  작업 부하에 적절 한 VM 크기를 선택 합니다. |
| 신규 또는 기존 Virtual Network | 새 vNet 만들기 (기본값) 또는 기존 vNet 사용 |
| 기본 CIDR 설정 선택 또는 IP 범위 사용자 지정 (CIDR) | 기본 CIDR 범위를 수락 하거나 **사용자 지정 IP 범위** 를 선택 하 고 사용자 지정 cidr 정보를 입력 합니다.  기본 설정은 10.0.0.0/14 CIDR, 10.1.0.0/16이 포함 된 마스터 서브넷, 10.2.0.0/16의 인프라 subnet 및 10.3.0.0/16을 사용 하는 계산 및 cn 서브넷을 사용 하 여 vNet을 만듭니다. |
| Key Vault 리소스 그룹 이름 | Key Vault를 포함 하는 리소스 그룹의 이름입니다. |
| Key Vault 이름 | Ssh 개인 키를 사용 하 여 암호를 포함 하는 Key Vault의 이름입니다.  영숫자 문자와 대시만 사용할 수 있으며 3 자에서 24 자 사이 여야 합니다. |
| 비밀 이름 | Ssh 개인 키를 포함 하는 암호의 이름입니다.  영숫자 문자와 대시만 사용할 수 있습니다. |

   ![제공 인프라 블레이드](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**크기 변경**

다른 VM 크기를 선택 하려면 ***크기 변경***을 클릭 합니다.  VM 선택 창이 열립니다.  원하는 VM 크기를 선택 하 고 **선택**을 클릭 합니다.

   ![VM 크기 선택](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**기존 Virtual Network**

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| 기존 Virtual Network 이름 | 기존 vNet의 이름 |
| 마스터 노드의 서브넷 이름 | 마스터 노드에 대 한 기존 서브넷의 이름입니다.  하나 이상의 IP 주소를 포함 해야 하 고 RFC 1918을 준수 해야 합니다. |
| 인프라 노드의 서브넷 이름 | 인프라 노드에 대 한 기존 서브넷의 이름입니다.  IP 주소가 32 개 이상 있어야 하 고 RFC 1918을 따라야 합니다. |
| 계산 및 cn 노드의 서브넷 이름 | 계산 및 cn 노드에 대 한 기존 서브넷의 이름입니다.  IP 주소가 32 개 이상 있어야 하 고 RFC 1918을 따라야 합니다. |
| 기존 Virtual Network에 대 한 리소스 그룹 | 기존 vNet을 포함 하는 리소스 그룹의 이름입니다. |

   ![제품 인프라 기존 vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**사용자 지정 IP 범위**

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| Virtual Network의 주소 범위 | VNet에 대 한 사용자 지정 CIDR |
| 마스터 노드를 포함 하는 서브넷의 주소 범위 | 마스터 서브넷에 대 한 사용자 지정 CIDR |
| 인프라 노드를 포함 하는 서브넷의 주소 범위 | 인프라 서브넷에 대 한 사용자 지정 CIDR |
| Compute 및 cn 노드를 포함 하는 서브넷의 주소 범위 | 계산 및 cn 노드에 대 한 사용자 지정 CIDR |

   ![제공 인프라 사용자 지정 IP 범위](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

입력 매개 변수의 값을 입력 하 고 **확인을** 클릭 합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| OpenShift 관리 사용자 암호 | 초기 OpenShift 사용자에 대 한 암호입니다.  이 사용자는 클러스터 관리자 이기도 합니다. |
| OpenShift 관리자 사용자 암호 확인 | OpenShift 관리 사용자 암호를 다시 입력 합니다. |
| Red Hat 구독 관리자 사용자 이름 | Red Hat 구독 또는 조직 ID에 액세스 하기 위한 사용자 이름입니다.  이 자격 증명은 구독에 RHEL 인스턴스를 등록 하는 데 사용 되며 Microsoft 또는 Red Hat에 의해 저장 되지 않습니다. |
| Red Hat 구독 관리자 사용자 암호 | Red Hat 구독 또는 활성화 키에 액세스 하기 위한 암호입니다.  이 자격 증명은 구독에 RHEL 인스턴스를 등록 하는 데 사용 되며 Microsoft 또는 Red Hat에 의해 저장 되지 않습니다. |
| Red Hat Subscription Manager OpenShift 풀 ID | OpenShift Container Platform 자격을 포함 하는 풀 ID입니다. 클러스터 설치를 위한 OpenShift Container Platform의 충분 한 권한이 있는지 확인 합니다. |
| Broker/마스터 노드에 대 한 Red Hat Subscription Manager OpenShift 풀 ID | Broker/마스터 노드에 대 한 OpenShift Container Platform 자격을 포함 하는 풀 ID입니다. 클러스터 설치를 위한 OpenShift Container Platform의 충분 한 권한이 있는지 확인 합니다. Broker/master 풀 ID를 사용 하지 않는 경우 응용 프로그램 노드에 대 한 풀 ID를 입력 합니다. |
| Azure Cloud Provider 구성 | Azure Cloud Provider를 사용 하도록 OpenShift를 구성 합니다. 영구적 볼륨에 대해 Azure disk attach를 사용 하는 경우 필요 합니다.  기본값은 예입니다. |
| Azure AD 서비스 사용자 클라이언트 ID GUID | Azure AD 서비스 주체 클라이언트 ID GUID (AppID 라고도 함) Azure Cloud Provider 구성을 **예** 로 설정 하는 경우에만 필요 합니다. |
| Azure AD 서비스 주체 클라이언트 ID 암호 | Azure AD 서비스 주체 클라이언트 ID 암호입니다. Azure Cloud Provider 구성을 **예** 로 설정 하는 경우에만 필요 합니다. |
 
   ![제안 OpenShift 블레이드](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**추가 설정**

추가 설정 블레이드에서 glusterfs 저장소, 로깅, 메트릭 및 라우터 하위 도메인에 대해 CN을 구성할 수 있습니다.  기본값은 이러한 옵션을 설치 하지 않으며 테스트 목적으로 nip.io을 라우터 하위 도메인으로 사용 합니다. CN을 사용 하도록 설정 하면 세 개의 추가 계산 노드 3 개가 glusterfs pod을 호스트 하는 추가 연결 된 디스크로 설치 됩니다.  

입력 매개 변수의 값을 입력 하 고 **확인을** 클릭 합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| CN (Container Native Storage) 구성 | OpenShift 클러스터에 CN을 설치 하 고 저장소로 사용 하도록 설정 합니다. Azure 공급자를 사용 하지 않도록 설정 하는 경우 기본값 |
| 클러스터 로깅 구성 | 클러스터에 EFK 로깅 기능을 설치 합니다.  인프라 노드의 크기를 적절 하 게 조정 하 여 EFK pod를 호스트 합니다. |
| 클러스터에 대 한 메트릭 구성 | OpenShift 클러스터에 Hawkular 메트릭을 설치 합니다.  인프라 노드의 크기를 조정 하 여 Hawkular 메트릭 pod를 호스트 합니다. |
| 기본 라우터 하위 도메인 | 테스트를 위한 nipio를 선택 하거나 사용자 지정을 선택 하 여 프로덕션에 사용할 자체 하위 도메인을 입력 합니다. |
 
   ![추가 블레이드 제공](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**추가 설정-추가 매개 변수**

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| CN 노드 크기 | 기본 노드 크기를 그대로 적용 하거나 **크기 변경** 을 선택 하 여 새 VM 크기를 선택 합니다. |
| 사용자 지정 하위 도메인 입력 | OpenShift 클러스터에서 라우터를 통해 응용 프로그램을 노출 하는 데 사용할 사용자 지정 라우팅 도메인입니다.  적절 한 와일드 카드 DNS 항목을 만들어야 합니다. |
 
   ![추가 cn 설치 제공](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**요약**

이 단계에서 유효성 검사를 수행 하 여 코어 할당량을 확인 하면 클러스터에 대해 선택한 총 Vm 수를 충분히 배포할 수 있습니다.  입력 한 모든 매개 변수를 검토 합니다.  입력이 허용 되는 경우 **확인** 을 클릭 하 여 계속 합니다.

   ![제안 요약 블레이드](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**구입**

구입 페이지에서 연락처 정보를 확인 하 고 **구매** 를 클릭 하 여 사용 약관에 동의 하 고 Openshift Container Platform 클러스터의 배포를 시작 합니다.

   ![제품 구매 블레이드](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. **Openshift 콘솔 URL**을 사용 하 여 브라우저에서 openshift 콘솔에 연결 합니다. 또한 방호 호스트로 SSH 할 수 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com)
- 