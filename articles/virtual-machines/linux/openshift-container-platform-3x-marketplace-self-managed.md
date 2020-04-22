---
title: Azure에서 OpenShift 컨테이너 플랫폼 3.11 자체 관리형 마켓플레이스 오퍼 배포
description: Azure에서 OpenShift 컨테이너 플랫폼 3.11 자체 관리형 마켓플레이스 오퍼를 배포합니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759581"
---
# <a name="configure-prerequisites"></a>필수 조건 구성

마켓플레이스 오퍼를 사용하여 Azure에서 자체 관리되는 OpenShift 컨테이너 플랫폼 3.11 클러스터를 배포하려면 몇 가지 필수 구성 을 구성해야 합니다.  Ssh 키(암호 없이), Azure 키 자격 증명 모음, 키 자격 증명 모음 및 서비스 주체를 만드는 방법에 대한 [지침은 OpenShift 필수 구성 조건](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) 문서를 참조하십시오.

 
## <a name="deploy-using-the-marketplace-offer"></a>마켓플레이스 오퍼를 사용하여 배포

자체 관리되는 OpenShift 컨테이너 플랫폼 3.11 클러스터를 Azure에 배포하는 가장 간단한 방법은 [Azure Marketplace 오퍼를](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)사용하는 것입니다.

이 옵션은 가장 간단하지만 사용자 지정 기능도 제한되어 있습니다. 마켓플레이스 오퍼는 OpenShift 컨테이너 플랫폼 3.11.82를 배포하며 다음과 같은 구성 옵션을 포함합니다.

- **마스터 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 마스터 노드입니다.
- **인프라 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 인프라 노드입니다.
- **노드**: 노드 수(1에서 9 사이)와 인스턴스 유형을 구성할 수 있습니다.
- **디스크 유형**: 관리 디스크가 사용됩니다.
- **네트워킹**: 신규 또는 기존 네트워크 및 사용자 정의 CIDR 범위 지원.
- **CNS**: CNS를 사용하도록 설정할 수 있습니다.
- **메트릭**: 매큘러 메트릭을 사용할 수 있습니다.
- **로깅**: EFK 로깅을 활성화할 수 있습니다.
- **Azure 클라우드 공급자**: 기본적으로 활성화되어 있으며 비활성화할 수 있습니다.

Azure 포털의 왼쪽 상단에서 **리소스 만들기를**클릭하고 검색 상자에 '오픈 시프트 컨테이너 플랫폼'을 입력하고 Enter를 누르고 이에 대해 누르고 있습니다.

   ![새 리소스 검색](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

결과 페이지는 레드 **햇 오픈 시프트 컨테이너 플랫폼 3.11 목록에서 자체 관리와** 함께 열립니다. 

   ![새 리소스 검색 결과](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

오퍼의 세부 정보를 보려면 쿠폰을 클릭하십시오. 이 제안을 배포하려면 **만들기를**클릭합니다. 필요한 매개 변수를 입력하는 UI가 나타납니다. 첫 번째 화면은 **기본 블레이드입니다.**

   ![오퍼 타이틀 페이지](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**기본 사항**

입력 매개 변수에 대한 도움말을 얻으려면 매개 변수 이름 옆에 있는 ***i*** 위로 마우스를 가져갑니다.

입력 매개 변수에 대한 값을 입력하고 **확인을**클릭합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| VM 관리자 사용자 이름 | 모든 VM 인스턴스에서 만들 관리자 사용자 |
| 관리자 사용자를 위한 SSH 공개 키 | VM에 로그인하는 데 사용되는 SSH 공개 키 - 암호가 없어야 합니다. |
| Subscription | 클러스터를 배포하는 Azure 구독 |
| 리소스 그룹 | 새 리소스 그룹을 만들거나 클러스터 리소스에 대한 기존 빈 리소스 그룹을 선택합니다. |
| 위치 | 클러스터를 배포할 Azure 지역 |

   ![기본 블레이드 제공](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**인프라 설정**

입력 매개 변수에 대한 값을 입력하고 **확인을**클릭합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| OCP 클러스터 이름 접두사 | 모든 노드에 대한 호스트 이름을 구성하는 데 사용되는 클러스터 접두사입니다. 1~20자 사이 |
| 마스터 노드 크기 | 기본 VM 크기를 수락하거나 **크기 변경을** 클릭하여 다른 VM 크기를 선택합니다.  작업 부하에 적합한 VM 크기 선택 |
| 인프라 노드 크기 | 기본 VM 크기를 수락하거나 **크기 변경을** 클릭하여 다른 VM 크기를 선택합니다.  작업 부하에 적합한 VM 크기 선택 |
| 응용 프로그램 노드 수 | 기본 VM 크기를 수락하거나 **크기 변경을** 클릭하여 다른 VM 크기를 선택합니다.  작업 부하에 적합한 VM 크기 선택 |
| 응용 프로그램 노드 크기 | 기본 VM 크기를 수락하거나 **크기 변경을** 클릭하여 다른 VM 크기를 선택합니다.  작업 부하에 적합한 VM 크기 선택 |
| 요새 호스트 크기 | 기본 VM 크기를 수락하거나 **크기 변경을** 클릭하여 다른 VM 크기를 선택합니다.  작업 부하에 적합한 VM 크기 선택 |
| 신규 또는 기존 가상 네트워크 | 새 vNet(기본값) 만들기 또는 기존 vNet 사용 |
| 기본 CIDR 설정을 선택하거나 IP 범위(CIDR)를 사용자 지정합니다. | 기본 CIDR 범위를 수락하거나 **사용자 지정 IP 범위를** 선택하고 사용자 지정 CIDR 정보를 입력합니다.  기본 설정은 CIDR이 10.0.0.0/14, 마스터 서브넷이 10.1.0.0/16, 인프라 서브넷이 10.2.0.0/16인 인프라 서브넷, 10.3.0.0/16의 계산 및 cns 서브넷이 있는 vNet을 생성합니다. |
| 키 볼트 리소스 그룹 이름 | 키 자격 증명 모음이 포함된 리소스 그룹의 이름 |
| Key Vault 이름 | ssh 개인 키와 함께 비밀을 포함 하는 키 볼트의 이름입니다.  숫자 문자와 대시만 허용되며 3~24자 사이여야 합니다. |
| 비밀 이름 | ssh 개인 키가 포함된 비밀의 이름입니다.  숫자 문자와 대시만 허용됩니다. |

   ![인프라 블레이드 제공](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**크기 변경**

다른 VM 크기를 선택하려면 ***크기 변경을***클릭합니다.  VM 선택 창이 열립니다.  원하는 VM 크기를 선택하고 **을 클릭합니다.**

   ![VM 크기 선택](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**기존 가상 네트워크**

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| 기존 가상 네트워크 이름 | 기존 vNet의 이름 |
| 마스터 노드의 서브넷 이름 | 마스터 노드에 대한 기존 서브넷의 이름입니다.  16개 이상의 IP 주소를 포함하고 RFC 1918을 따라야 합니다. |
| 인프라 노드의 서브넷 이름 | 인프라 노드의 기존 서브넷 이름입니다.  32개 이상의 IP 주소를 포함하고 RFC 1918을 따라야 합니다. |
| 계산 및 cns 노드의 서브넷 이름 | 계산 및 cns 노드에 대한 기존 서브넷의 이름입니다.  32개 이상의 IP 주소를 포함하고 RFC 1918을 따라야 합니다. |
| 기존 가상 네트워크에 대한 리소스 그룹 | 기존 vNet을 포함하는 리소스 그룹의 이름 |

   ![기존 vnet 인프라 제공](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**사용자 지정 IP 범위**

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| 가상 네트워크의 주소 범위 | vNet에 대한 사용자 지정 CIDR |
| 마스터 노드를 포함하는 서브넷의 주소 범위 | 마스터 서브넷용 사용자 지정 CIDR |
| 인프라 노드를 포함하는 서브넷의 주소 범위 | 인프라 서브넷을 위한 사용자 지정 CIDR |
| 계산 및 cns 노드를 포함하는 서브넷의 주소 범위 | 컴퓨팅 및 cns 노드를 위한 사용자 지정 CIDR |

   ![인프라 맞춤형 IP 범위 제공](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

입력 매개 변수에 대한 값을 입력하고 **확인을** 클릭합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| 오픈 시프트 관리자 사용자 암호 | 초기 OpenShift 사용자의 암호입니다.  이 사용자는 클러스터 관리자이기도 합니다. |
| 오픈 시프트 관리자 사용자 암호 확인 | OpenShift 관리자 사용자 암호 다시 입력 |
| 레드 햇 구독 관리자 사용자 이름 | Red Hat 구독 또는 조직 ID에 액세스하는 사용자 이름입니다.  이 자격 증명은 RHEL 인스턴스를 구독에 등록하는 데 사용되며 Microsoft 또는 Red Hat에 저장되지 않습니다. |
| 레드 햇 구독 관리자 사용자 암호 | Red Hat 구독 또는 활성화 키에 액세스하는 암호입니다.  이 자격 증명은 RHEL 인스턴스를 구독에 등록하는 데 사용되며 Microsoft 또는 Red Hat에 저장되지 않습니다. |
| 레드 햇 구독 관리자 오픈 시프트 풀 ID | OpenShift 컨테이너 플랫폼 사용 권한을 포함하는 풀 ID입니다. 클러스터 설치를 위한 OpenShift 컨테이너 플랫폼의 충분한 사용 권한이 있는지 확인 |
| 브로커 / 마스터 노드에 대한 레드 햇 서브스크립션 관리자 오픈 시프트 풀 ID | 브로커 / 마스터 노드에 대한 OpenShift 컨테이너 플랫폼 자격을 포함하는 풀 ID입니다. 클러스터 설치를 위한 OpenShift 컨테이너 플랫폼의 충분한 사용 권한이 있는지 확인합니다. 브로커 / 마스터 풀 ID를 사용하지 않는 경우 응용 프로그램 노드의 풀 ID를 입력하십시오. |
| Azure 클라우드 공급자 구성 | Azure 클라우드 공급자를 사용하도록 OpenShift를 구성합니다. 영구 볼륨에 Azure 디스크 연결 을 사용하는 경우 필요합니다.  기본값은 예입니다. |
| Azure AD 서비스 주체 클라이언트 ID GUID | Azure AD 서비스 주체 클라이언트 ID GUID - AppID라고도 합니다. Azure 클라우드 공급자 구성이 **예로** 설정된 경우에만 필요합니다. |
| Azure AD 서비스 주체 클라이언트 ID 비밀 | Azure AD 서비스 주체 클라이언트 ID 보안 검색대입니다. Azure 클라우드 공급자 구성이 **예로** 설정된 경우에만 필요합니다. |
 
   ![오픈시프트 블레이드 제공](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**추가 설정**

추가 설정 블레이드를 사용하면 글루스터프 스토리지, 로깅, 메트릭 및 라우터 하위 도메인에 대한 CNS구성을 수행할 수 있습니다.  기본값은 이러한 옵션을 설치하지 않으며 테스트 목적으로 nip.io 라우터 하위 도메인으로 사용합니다. CNS를 사용하도록 설정하면 글루스터프 포드를 호스트하는 3개의 추가 연결된 디스크가 있는 3개의 추가 계산 노드가 설치됩니다.  

입력 매개 변수에 대한 값을 입력하고 **확인을** 클릭합니다.

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| 컨테이너 네이티브 저장소 구성(CNS) | OpenShift 클러스터에 CNS를 설치하고 저장소로 사용하도록 설정합니다. Azure 공급자를 사용할 수 없습니다 경우 기본값입니다. |
| 클러스터 로깅 구성 | EFK 로깅 기능을 클러스터에 설치합니다.  EFK 포드를 호스트하기에 적합한 인프라 노드 크기 |
| 클러스터에 대한 메트릭 구성 | OpenShift 클러스터에 호크큘러 메트릭을 설치합니다.  호크큘러 메트릭 포드를 호스트하기에 적합한 크기 인프라 노드 |
| 기본 라우터 하위 도메인 | 테스트를 위해 니피오를 선택하거나 프로덕션을 위해 자체 하위 도메인을 입력하려면 사용자 지정 |
 
   ![추가 블레이드 제공](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**추가 설정 - 추가 매개 변수**

| 입력 매개 변수 | 매개 변수 설명 |
|-----------------------|-----------------|
| (CNS) 노드 크기 | 기본 노드 크기를 수락하거나 **크기 변경을** 선택하여 새 VM 크기를 선택합니다. |
| 사용자 지정 하위 도메인 입력 | OpenShift 클러스터의 라우터를 통해 응용 프로그램을 노출하는 데 사용할 사용자 지정 라우팅 도메인입니다.  적절한 와일드카드 DNS 항목을 만들어야 합니다.] |
 
   ![추가 CNS 설치 제공](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**요약**

검증은 코어 할당량이 클러스터에 대해 선택된 총 VM 수를 배포하기에 충분한지 확인하기 위해 이 단계에서 발생한다.  입력된 모든 매개 변수를 검토합니다.  입력이 허용되는 경우 **확인을** 클릭하여 계속합니다.

   ![오퍼 요약 블레이드](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**구입**

구매 페이지에서 연락처 정보를 확인하고 **구매를** 클릭하여 사용 약관에 동의하고 OpenShift 컨테이너 플랫폼 클러스터의 배포를 시작합니다.

   ![오퍼 구매 블레이드](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. OpenShift 콘솔 URL을 사용하여 브라우저와 **오픈 시프트 콘솔에**연결합니다. 또한 SSH를 Bastion 호스트로 이동시킬 수도 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

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