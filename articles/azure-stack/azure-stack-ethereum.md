---
title: Azure 스택 Ethereum 솔루션 템플릿
description: 사용자 지정 솔루션 템플릿을 사용 하 여 배포 하 고 Azure 스택에 컨소시엄 Ethereum 네트워크 구성
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114730"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Azure 스택 Ethereum 솔루션 템플릿

Ethereum 솔루션 템플릿은 쉽고 빠르게 배포 하 고 Azure와 Ethereum 한 지식이 별로 없더라도 다중 멤버 컨소시엄 Ethereum 네트워크를 구성할 수 있도록 설계 되었습니다.

각 멤버는 사용자 입력 및 Azure 스택 관리자 포털을 통해 단일 클릭 배포는 소수의 해당 네트워크 사용 공간을 구성할 수 있습니다. 부하 분산 된 트랜잭션 집합의 각 구성원의 네트워크 사용 공간으로 구성 됩니다는 응용 프로그램이 나 사용자 상호 작용할 수 트랜잭션과 트랜잭션 레코드, 마이닝 노드 집합이 네트워크 가상 어플라이언스 (NVA)를 제출 하려면 사용 합니다. 후속 연결 단계는 완전히 구성 된 다중 멤버 blockchain 네트워크를 만드는 NVAs를 연결 합니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 다운로드 [마켓플레이스에서](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS 16.04.201802220 버전
* Windows Server 2016 
* Linux 2.0에 대 한 사용자 지정 스크립트 
* 사용자 지정 스크립트 확장 

Azure에서 blockchain 시나리오에 대 한 자세한 내용은 참조 [Ethereum 작업 증명 컨소시엄 솔루션 템플릿을](../blockchain-workbench/ethereum-deployment-guide.md)합니다.

여러 가상 컴퓨터 배포를 지원할 수 있는 Azure 구독이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

## <a name="deployment-architecture"></a>배포 아키텍처

이 솔루션 템플릿은 단일 컴퓨터 또는 다중 멤버 Ethereum 컨소시엄 네트워크를 배포할 수 있습니다. 가상 네트워크는 네트워크 가상 어플라이언스 및 연결 리소스를 사용 하 여 체인 토폴로지에서 연결 되어 있습니다. 

## <a name="deployment-use-cases"></a>배포 사용 사례

서식 파일 및 여러 가지 방법으로 멤버 조인에 대 한 Ethereum 컨소시엄을 배포할 수, 사전에 테스트 하는 것 같습니다.
- Azure AD 또는 AD FS, multi-node Azure 스택, 요소와 동일한 구독을 사용 하 여 멤버를 배포 하거나 다른 구독으로 합니다.
- 단일 노드 Azure 스택 (Azure AD)와 요소와 동일한 구독을 사용 하 여 멤버를 배포 합니다.

### <a name="standalone-and-consortium-leader-deployment"></a>독립 실행형 및 컨소시엄 리더 배포

컨소시엄 리더 템플릿을 네트워크에 있는 첫 번째 멤버의 사용 공간을 구성합니다. 

1. 다운로드는 [GitHub에서 리더 서식 파일](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. Azure 스택 관리 포털에서 선택 **새로 만들기 > 템플릿 배포** 사용자 지정 서식 파일에서 배포 하도록 합니다.
3. 선택 **템플릿 편집** 새 사용자 지정 서식 파일을 편집 합니다.
4. 오른쪽에 편집 창에서 복사한 리더 템플릿을 이전에 다운로드 하는 JSON을 붙여 넣습니다.
    
    ![리더 템플릿 편집](media/azure-stack-ethereum/edit-leader-template.png)

5. **저장**을 선택합니다.
6. 선택 **매개 변수 편집** 배포에 대 한 템플릿 매개 변수를 완료 합니다.
    
    ![리더 템플릿 매개 변수 편집](media/azure-stack-ethereum/edit-leader-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 배포 된 리소스 이름 지정에 대 한 기준으로 사용 하는 문자열입니다. | 길이가 1 ~ 6 영숫자 문자 | eth
    인증 유형 | 가상 머신을 인증하는 방법. | 암호 또는 SSH 공개 키 | 암호
    ADMINUSERNAME | 배포 된 각 VM의 관리자 사용자 이름 | 1-64자 | gethadmin
    ADMINPASSWORD (인증 유형 = 암호)| 배포된 각 가상 머신의 관리자 계정 암호. 암호는 다음 요구 사항 중 3 가지를 포함해야 합니다. 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자. <br />처음에는 모든 VM이 같은 암호를 갖지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|
    ADMINSSHKEY (인증 유형을 sshPublicKey =) | 원격 로그인에 사용하는 보안 셸 키. | |
    GENESISBLOCK | 사용자 지정 최초 블록을 나타내는 JSON 문자열입니다. | |
    ETHEREUMACCOUNTPSSWD | Ethereum 계정을 보호 하는 데 사용 하는 관리자 암호입니다. | |
    ETHEREUMACCOUNTPASSPHRASE | Ethereum 계정과 연결 된 개인 키를 생성 하는 데 사용 되는 암호입니다. | |
    ETHEREUMNETWORKID | 컨소시엄의 네트워크 ID입니다. | 5에서 999999999 사이의 모든 값을 사용 하 여 | 72
    CONSORTIUMMEMBERID | 컨소시엄 네트워크의 각 멤버와 연결 된 ID입니다.   | 이 ID는 네트워크에서 고유 해야 합니다. | 0
    NUMMININGNODES | 마이닝 노드 수입니다. | 2 사이의 15입니다. | 2
    MNNODEVMSIZE | 마이닝 노드의 VM 크기입니다. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 마이닝 노드의 스토리지 성능입니다. | | Standard-LRS
    NUMTXNODES | 트랜잭션 노드 수입니다. | 1과 5 사이의. | 1
    TXNODEVMSIZE | 트랜잭션 노드 VM 크기입니다. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 트랜잭션 노드의 스토리지 성능입니다. | | Standard-LRS
    BASEURL | 따라 템플릿을 가져오도록 기준 URL입니다. | 배포 템플릿을 사용자 지정 하려는 경우가 아니면 기본값을 사용 합니다. | 

7. **확인**을 선택합니다.
8. **사용자 지정 배포**, 지정 **구독**, **리소스 그룹**, 및 **리소스 그룹 위치**합니다.
    
    ![리더 배포 매개 변수](media/azure-stack-ethereum/leader-deployment-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    구독 | 컨소시엄 네트워크를 배포하는 구독 | | 소비 구독
    리소스 그룹 | 컨소시엄 네트워크를 배포하는 리소스 그룹. | | EthereumResources
    위치 | 리소스 그룹에 대한 Azure 지역입니다. | | 로컬

8. **만들기**를 선택합니다.

배포 20 분이 걸릴 수 또는 완료 하는데 더 합니다.

배포가 완료 된 후에 배포에 대 한 요약을 검토할 수 있습니다 **Microsoft 합니다. 서식 파일** 리소스 그룹의 배포 항목의 합니다. 요약은 컨소시엄 멤버 참여 사용할 수 있는 출력 값을 포함 합니다.

리더의 배포를 확인 하려면 리더의 관리 사이트를 찾습니다. 출력 섹션에서 관리자 사이트 주소를 찾을 수 있습니다 **Microsoft.Template** 배포 합니다.  

![리더 배포 요약](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>컨소시엄 멤버 배포에 조인

1. 다운로드는 [GitHub에서 컨소시엄 멤버 템플릿](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. Azure 스택 관리 포털에서 선택 **새로 만들기 > 템플릿 배포** 사용자 지정 서식 파일에서 배포 하도록 합니다.
3. 선택 **템플릿 편집** 새 사용자 지정 서식 파일을 편집 합니다.
4. 오른쪽에 편집 창에서 복사한 이전에 다운로드 한 JSON 리더 서식 파일을 붙여 넣습니다.
5. **저장**을 선택합니다.
6. 선택 **매개 변수 편집** 배포에 대 한 템플릿 매개 변수를 완료 합니다.

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 배포 된 리소스 이름 지정에 대 한 기준으로 사용 하는 문자열입니다. | 길이가 1 ~ 6 영숫자 문자 | eth
    인증 유형 | 가상 머신을 인증하는 방법. | 암호 또는 SSH 공개 키 | 암호
    ADMINUSERNAME | 배포 된 각 VM의 관리자 사용자 이름 | 1-64자 | gethadmin
    ADMINPASSWORD (인증 유형 = 암호)| 배포된 각 가상 머신의 관리자 계정 암호. 암호는 다음 요구 사항 중 3 가지를 포함해야 합니다. 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자. <br />처음에는 모든 VM이 같은 암호를 갖지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|
    ADMINSSHKEY (인증 유형을 sshPublicKey =) | 원격 로그인에 사용하는 보안 셸 키. | |
    CONSORTIUMMEMBERID | 컨소시엄 네트워크의 각 멤버와 연결 된 ID입니다.   | 이 ID는 네트워크에서 고유 해야 합니다. | 0
    NUMMININGNODES | 마이닝 노드 수입니다. | 2 사이의 15입니다. | 2
    MNNODEVMSIZE | 마이닝 노드의 VM 크기입니다. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 마이닝 노드의 스토리지 성능입니다. | | Standard-LRS
    NUMTXNODES | 트랜잭션 노드 수입니다. | 1과 5 사이의. | 1
    TXNODEVMSIZE | 트랜잭션 노드 VM 크기입니다. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 트랜잭션 노드의 스토리지 성능입니다. | | Standard-LRS
    CONSORTIUMDATA | 다른 멤버의 배포에서 제공 된 관련 컨소시엄 구성 데이터를 가리키는 URL입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | |
    REMOTEMEMBERVNETADDRESSSPACE | 리더의 NVA IP 주소입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | | 
    REMOTEMEMBERNVAPUBLICIP | 리더의 NVA IP 주소입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | | 
    CONNECTIONSHAREDKEY | 연결을 설정 하는 컨소시엄 네트워크 구성원 간의 사전 설정 된 암호입니다. | |
    BASEURL | 서식 파일에 대 한 기본 URL입니다. | 배포 템플릿을 사용자 지정 하려는 경우가 아니면 기본값을 사용 합니다. | 

7. **확인**을 선택합니다.
8. **사용자 지정 배포**, 지정 **구독**, **리소스 그룹**, 및 **리소스 그룹 위치**합니다.

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    구독 | 컨소시엄 네트워크를 배포하는 구독 | | 소비 구독
    리소스 그룹 | 컨소시엄 네트워크를 배포하는 리소스 그룹. | | MemberResources
    위치 | 리소스 그룹에 대한 Azure 지역입니다. | | 로컬

8. **만들기**를 선택합니다.

배포 20 분이 걸릴 수 또는 완료 하는데 더 합니다.

배포가 완료 된 후에 배포에 대 한 요약을 검토할 수 있습니다 **Microsoft.Template** 리소스 그룹의 배포 항목의 합니다. 요약은 컨소시엄 멤버를 연결 하는 데 사용할 수 있는 출력 값을 포함 합니다.

멤버의 배포를 확인 하려면 멤버의 관리 사이트를 찾습니다. Microsoft.Template 배포 [출력] 섹션에서 관리자 사이트 주소를 찾을 수 있습니다.

![멤버 배포 요약](media/azure-stack-ethereum/ethereum-node-status-2.png)

멤버의 노드 상태는 그림에 나와 있는 것 처럼 **실행 되 고 있지**합니다. 즉, 멤버 및 리더 간의 연결은 설정 되지 않습니다. 멤버와 리더 간의 연결이 양방향 연결 합니다. 멤버를 배포할 때 서식 파일에서에서 자동으로 만듭니다 연결 멤버 리더에 있습니다. 만들려는 멤버에 대 한 리더 연결 다음 단계로 이동 합니다.

### <a name="connect-member-and-leader"></a>멤버 및 리더 연결

이 서식 파일은 리더에서 원격 멤버에 연결을 만듭니다. 

1. 다운로드는 [GitHub에서 멤버와 리더 템플릿 연결](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. Azure 스택 관리 포털에서 선택 **새로 만들기 > 템플릿 배포** 사용자 지정 서식 파일에서 배포 하도록 합니다.
3. 선택 **템플릿 편집** 새 사용자 지정 서식 파일을 편집 합니다.
4. 오른쪽에 편집 창에서 복사한 이전에 다운로드 한 JSON 리더 서식 파일을 붙여 넣습니다.
    
    ![편집 서식 파일에 연결](media/azure-stack-ethereum/edit-connect-template.png)

5. **저장**을 선택합니다.
6. 선택 **매개 변수 편집** 배포에 대 한 템플릿 매개 변수를 완료 합니다.
    
    ![템플릿 매개 변수를 연결 편집](media/azure-stack-ethereum/edit-connect-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | 리더의 이름 접두사입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다.  | 길이가 1 ~ 6 영숫자 문자 | |
    MEMBERROUTETABLENAME | 리더의 경로 테이블의 이름입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | 주소 공간 멤버의 합니다. 이 값은 멤버의 배포 출력에서 찾을 수 있습니다. | |
    CONNECTIONSHAREDKEY | 연결을 설정 하는 컨소시엄 네트워크 구성원 간의 사전 설정 된 암호입니다.  | |
    REMOTEMEMBERNVAPUBLICIP | 멤버의 NVA IP 주소입니다. 이 값은 멤버의 배포 출력에서 찾을 수 있습니다. | |
    MEMBERNVAPRIVATEIP | 리더의 개인 NVA IP 주소입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | |
    위치 | Azure 스택 환경의 위치입니다. | | 로컬
    BASEURL | 서식 파일에 대 한 기본 URL입니다. | 배포 템플릿을 사용자 지정 하려는 경우가 아니면 기본값을 사용 합니다. | 

7. **확인**을 선택합니다.
8. **사용자 지정 배포**, 지정 **구독**, **리소스 그룹**, 및 **리소스 그룹 위치**합니다.
    
    ![배포 매개 변수를 연결 합니다.](media/azure-stack-ethereum/connect-deployment-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    구독 | 리더의 구독입니다. | | 소비 구독
    리소스 그룹 | 리더의 리소스 그룹입니다. | | EthereumResources
    위치 | 리소스 그룹에 대한 Azure 지역입니다. | | 로컬

8. **만들기**를 선택합니다.

배포가 완료 된 후 리더와 멤버 통신을 시작 하는 데 몇 분 정도 걸리는 합니다. 배포를 확인 하려면 멤버의 관리 사이트를 새로 고칩니다. 멤버의 노드의 상태는 실행 되어야 합니다. 

![배포 확인](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>다음 단계

- Ethereum 및 Azure에 대 한 자세한 참조 [Blockchain 기술 및 응용 프로그램 | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/)합니다.
- Azure에서 blockchain 시나리오에 대 한 자세한 내용은 참조 [Ethereum 작업 증명 컨소시엄 솔루션 템플릿을](../blockchain-workbench/ethereum-deployment-guide.md)합니다.