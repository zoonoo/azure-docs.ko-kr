---
title: Azure Stack Ethereum 블록 체인 솔루션 템플릿
description: 사용자 지정 솔루션 템플릿을 사용 하 여 배포 하 고 Azure Stack에서 컨소시엄 Ethereum 블록 체인 네트워크를 구성 자습서
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/13/2018
ms.topic: tutorial
ms.service: azure-stack
ms.reviewer: seyadava
ms.custom: mvc
manager: femila
ms.openlocfilehash: acfa94799f36728f4e0041f1a51403edf6ffe37e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239475"
---
# <a name="tutorial-use-the-azure-stack-ethereum-blockchain-solution-template"></a>자습서: Azure Stack Ethereum 블록 체인 솔루션 템플릿을 사용 하 여

Ethereum 솔루션 템플릿에 쉽고 빠르게 배포 하 고 한 지식이 별로 없더라도 Azure 및 Ethereum 다중 멤버 컨소시엄 Ethereum 블록 체인 네트워크를 구성 하도록 설계 되었습니다.

소수의 사용자 입력 및 Azure Stack 테 넌 트 포털을 통해 한 번 클릭 배포의 각 멤버는 해당 네트워크 사용 공간을 프로비저닝할 수 있습니다. 각 구성원의 네트워크 사용 공간 트랜잭션 부하 분산 된 노드 집합으로 구성 하는 응용 프로그램이 나 사용자 상호 작용할 수 트랜잭션, 레코드 트랜잭션에서 마이닝 노드 집합 및 네트워크 가상 어플라이언스 (NVA)를 제출 하려면 사용 하 여 합니다. 후속 연결 단계를 완전히 구성 된 다중 멤버 블록 체인 네트워크를 만들려는 Nva를 연결 합니다.

이 설정 하려면 다음을 수행 해야 합니다.

> [!div class="checklist"]
> * 배포 아키텍처를 선택 합니다.
> * 독립 실행형, 컨소시엄 리더 또는 컨소시엄 멤버 네트워크 배포

## <a name="prerequisites"></a>필수 조건

최신 항목을 다운로드 [마켓플레이스에서](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS
* Windows Server 2016
* Linux 2.0에 대 한 사용자 지정 스크립트
* Windows용 사용자 지정 스크립트 확장

블록 체인 시나리오에 대 한 자세한 내용은 참조 하세요. [Ethereum 작업 증명 컨소시엄 솔루션 템플릿](../blockchain/templates/ethereum-deployment.md)합니다.

## <a name="deployment-architecture"></a>배포 아키텍처

이 솔루션 템플릿에 단일 또는 다중 멤버 Ethereum 컨소시엄 네트워크를 배포할 수 있습니다. 가상 네트워크는 네트워크 가상 어플라이언스 및 연결 리소스를 사용 하 여 체인 토폴로지에서 연결 됩니다. 

## <a name="deployment-use-cases"></a>배포 사용 사례

템플릿을 리더 및 다양 한 방법으로 멤버 조인에 대 한 Ethereum 컨소시엄을 배포할 수, 테스트를 거친 것 같습니다.

- Azure AD 또는 AD FS 사용 하 여 다중 노드 Azure Stack 배포 리더와 동일한 구독을 사용 하 여 멤버 또는 다른 구독을 사용 하 여 합니다.
- (사용 하 여 Azure AD)는 단일 노드 Azure Stack에서 잠재 고객 및 동일한 구독을 사용 하 여 멤버를 배포 합니다.

### <a name="standalone-and-consortium-leader-deployment"></a>독립 실행형 및 컨소시엄 리더 배포

컨소시엄 리더 템플릿 네트워크의 첫 번째 멤버의 공간을 구성합니다. 

1. 다운로드는 [리더 템플릿은 GitHub에서](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. Azure Stack 관리 포털에서 선택 **+ 리소스 만들기 > 템플릿 배포** 사용자 지정 템플릿에서 배포 합니다.
3. 선택 **템플릿 편집** 새 사용자 지정 템플릿을 편집 합니다.
4. 오른쪽의 편집 창에서 복사한 리더 템플릿을 이전에 다운로드 한 JSON를 붙여 넣습니다.
    
    ![리더 템플릿 편집](media/azure-stack-ethereum/edit-leader-template.png)

5. **저장**을 선택합니다.
6. 선택 **매개 변수 편집** 배포에 대 한 템플릿 매개 변수를 완료 합니다.
    
    ![리더 템플릿 매개 변수를 편집 합니다.](media/azure-stack-ethereum/edit-leader-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 배포 된 리소스 이름 지정에 대 한 기준으로 사용 하는 문자열입니다. | 길이가 1 ~ 6 영숫자 문자 | eth
    인증 유형 | 가상 머신을 인증하는 방법. | 암호 또는 SSH 공개 키 | 암호
    ADMINUSERNAME | 각 배포 된 VM의 관리자 사용자 이름 | 1-64자 | gethadmin
    ADMINPASSWORD (인증 유형 = 암호)| 배포된 각 가상 머신의 관리자 계정 암호. 암호는 다음 요구 사항 중 3 가지를 포함해야 합니다. 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자. <br />처음에는 모든 VM이 같은 암호를 갖지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|
    ADMINSSHKEY (인증 유형을 sshPublicKey =) | 원격 로그인에 사용하는 보안 셸 키. | |
    GENESISBLOCK | 사용자 지정 최초 블록을 나타내는 JSON 문자열입니다.  이 매개 변수 값을 지정 하는 것은 선택 사항입니다. | |
    ETHEREUMACCOUNTPSSWD | Ethereum 계정을 보호 하는 데 관리자 암호입니다. | |
    ETHEREUMACCOUNTPASSPHRASE | Ethereum 계정과 연결 된 개인 키를 생성 하는 데 암호입니다. | |
    ETHEREUMNETWORKID | 컨소시엄 네트워크 ID입니다. | 5에서 999999999 사이의 모든 값을 사용 하 여 | 72
    CONSORTIUMMEMBERID | 컨소시엄 네트워크의 각 멤버와 연결 된 ID입니다.   | 이 ID는 네트워크에서 고유 해야 합니다. | 0
    NUMMININGNODES | 마이닝 노드 수입니다. | 2 월 15 일 까지의 합니다. | 2
    MNNODEVMSIZE | 마이닝 노드의 VM 크기입니다. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 저장소 성능 마이닝 노드입니다. | | Standard_LRS
    NUMTXNODES | 트랜잭션 노드 수입니다. | 1 사이의 5입니다. | 1
    TXNODEVMSIZE | 트랜잭션 노드의 VM 크기입니다. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 트랜잭션 노드의 저장소 성능입니다. | | Standard_LRS
    BASEURL | 따라 템플릿을 가져오도록 기본 URL입니다. | 배포 템플릿을 사용자 지정 하려는 경우가 아니면 기본값을 사용 합니다. | 

7. **확인**을 선택합니다.
8. **사용자 지정 배포**를 지정 **구독**에 **리소스 그룹**, 및 **리소스 그룹 위치**합니다.
    
    ![리더 배포 매개 변수](media/azure-stack-ethereum/leader-deployment-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    구독 | 컨소시엄 네트워크를 배포하는 구독 | | 구독 사용
    리소스 그룹 | 컨소시엄 네트워크를 배포하는 리소스 그룹. | | EthereumResources
    위치 | 리소스 그룹에 대한 Azure 지역입니다. | | local

8. **만들기**를 선택합니다.

배포 20 분이 걸릴 수 이상입니다.

배포가 완료 되 면 배포에 대 한 요약을 검토할 수 있습니다 **Microsoft입니다. 템플릿** 리소스 그룹의 배포 섹션에서. 요약은 컨소시엄 멤버 가입 시키는 데 사용할 수 있는 출력 값을 포함 합니다.

리더의 배포를 확인 하려면 리더의 관리 사이트를 이동 합니다. 출력 섹션에서 관리자 사이트 주소를 찾을 수 있습니다 **Microsoft.Template** 배포 합니다.  

![리더 배포 요약](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>조인 컨소시엄 멤버 배포

1. 다운로드는 [컨소시엄 멤버 템플릿은 GitHub에서](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. Azure Stack 관리 포털에서 선택 **+ 리소스 만들기 > 템플릿 배포** 사용자 지정 템플릿에서 배포 합니다.
3. 선택 **템플릿 편집** 새 사용자 지정 템플릿을 편집 합니다.
4. 오른쪽의 편집 창에서 복사한 리더 템플릿을 이전에 다운로드 한 JSON를 붙여 넣습니다.
5. **저장**을 선택합니다.
6. 선택 **매개 변수 편집** 배포에 대 한 템플릿 매개 변수를 완료 합니다.

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | 배포 된 리소스 이름 지정에 대 한 기준으로 사용 하는 문자열입니다. | 길이가 1 ~ 6 영숫자 문자 | eth
    인증 유형 | 가상 머신을 인증하는 방법. | 암호 또는 SSH 공개 키 | 암호
    ADMINUSERNAME | 각 배포 된 VM의 관리자 사용자 이름 | 1-64자 | gethadmin
    ADMINPASSWORD (인증 유형 = 암호)| 배포된 각 가상 머신의 관리자 계정 암호. 암호는 다음 요구 사항 중 3 가지를 포함해야 합니다. 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자. <br />처음에는 모든 VM이 같은 암호를 갖지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|
    ADMINSSHKEY (인증 유형을 sshPublicKey =) | 원격 로그인에 사용하는 보안 셸 키. | |
    CONSORTIUMMEMBERID | 컨소시엄 네트워크의 각 멤버와 연결 된 ID입니다.   | 이 ID는 네트워크에서 고유 해야 합니다. | 0
    NUMMININGNODES | 마이닝 노드 수입니다. | 2 월 15 일 까지의 합니다. | 2
    MNNODEVMSIZE | 마이닝 노드의 VM 크기입니다. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | 저장소 성능 마이닝 노드입니다. | | Standard_LRS
    NUMTXNODES | 트랜잭션 노드 수입니다. | 1 사이의 5입니다. | 1
    TXNODEVMSIZE | 트랜잭션 노드의 VM 크기입니다. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | 트랜잭션 노드의 저장소 성능입니다. | | Standard_LRS
    CONSORTIUMDATA | 다른 멤버의 배포에서 제공 되는 관련 컨소시엄 구성 데이터를 가리키는 URL입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | |
    REMOTEMEMBERVNETADDRESSSPACE | 프로덕션에서는 리더의 NVA IP 주소입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | | 
    REMOTEMEMBERNVAPUBLICIP | 프로덕션에서는 리더의 NVA IP 주소입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | | 
    CONNECTIONSHAREDKEY | 연결을 설정 하는 컨소시엄 네트워크의 멤버 간 미리 설정 된 암호입니다. | |
    BASEURL | 템플릿에 대 한 기본 URL입니다. | 배포 템플릿을 사용자 지정 하려는 경우가 아니면 기본값을 사용 합니다. | 

7. **확인**을 선택합니다.
8. **사용자 지정 배포**를 지정 **구독**에 **리소스 그룹**, 및 **리소스 그룹 위치**합니다.

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    구독 | 컨소시엄 네트워크를 배포하는 구독 | | 구독 사용
    리소스 그룹 | 컨소시엄 네트워크를 배포하는 리소스 그룹. | | MemberResources
    위치 | 리소스 그룹에 대한 Azure 지역입니다. | | local

8. **만들기**를 선택합니다.

배포 20 분이 걸릴 수 이상입니다.

배포가 완료 되 면 배포에 대 한 요약을 검토할 수 있습니다 **Microsoft.Template** 리소스 그룹의 배포 섹션에서. 요약은 컨소시엄 멤버에 연결 하는 출력 값을 포함 합니다.

멤버의 배포를 확인 하려면 멤버의 관리 사이트를 이동 합니다. Microsoft.Template 배포의 출력 섹션에서 관리자 사이트 주소를 찾을 수 있습니다.

![멤버 배포 요약](media/azure-stack-ethereum/ethereum-node-status-2.png)

그림에 표시 된 것과 같이 멤버의 노드 상태가 **실행 되 고 있지**합니다. 즉, 멤버 및 리더 간의 연결이 설정 되지 됩니다. 멤버 및 리더 간의 연결이 양방향 연결 합니다. 멤버를 배포할 때 템플릿은 자동으로 만듭니다 연결 멤버에서 리더를 합니다. 만들려는 멤버 리더에서의 연결은 다음 단계로 이동 합니다.

### <a name="connect-member-and-leader"></a>멤버 및 리더를 연결 합니다.

이 템플릿은 원격 멤버로 리더에서 연결을 만듭니다. 

1. 다운로드는 [멤버 및 리더 템플릿은 GitHub에서 연결](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. Azure Stack 관리 포털에서 선택 **+ 리소스 만들기 > 템플릿 배포** 사용자 지정 템플릿에서 배포 합니다.
3. 선택 **템플릿 편집** 새 사용자 지정 템플릿을 편집 합니다.
4. 오른쪽의 편집 창에서 복사한 리더 템플릿을 이전에 다운로드 한 JSON를 붙여 넣습니다.
    
    ![편집 템플릿 연결](media/azure-stack-ethereum/edit-connect-template.png)

5. **저장**을 선택합니다.
6. 선택 **매개 변수 편집** 배포에 대 한 템플릿 매개 변수를 완료 합니다.
    
    ![템플릿 매개 변수를 연결 편집](media/azure-stack-ethereum/edit-connect-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | 리더의 이름 접두사입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다.  | 길이가 1 ~ 6 영숫자 문자 | |
    MEMBERROUTETABLENAME | 리더의 경로 테이블의 이름입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | 멤버의 공간을 해결 합니다. 이 값은 멤버의 배포 출력에서 찾을 수 있습니다. | |
    CONNECTIONSHAREDKEY | 연결을 설정 하는 컨소시엄 네트워크의 멤버 간 미리 설정 된 암호입니다.  | |
    REMOTEMEMBERNVAPUBLICIP | 멤버의 NVA IP 주소입니다. 이 값은 멤버의 배포 출력에서 찾을 수 있습니다. | |
    MEMBERNVAPRIVATEIP | 리더의 개인 NVA IP 주소입니다. 이 값은 리더의 배포 출력에서 찾을 수 있습니다. | |
    위치 | Azure Stack 환경과의 위치입니다. | | local
    BASEURL | 템플릿에 대 한 기본 URL입니다. | 배포 템플릿을 사용자 지정 하려는 경우가 아니면 기본값을 사용 합니다. | 

7. **확인**을 선택합니다.
8. **사용자 지정 배포**를 지정 **구독**에 **리소스 그룹**, 및 **리소스 그룹 위치**합니다.
    
    ![배포 매개 변수를 연결 합니다.](media/azure-stack-ethereum/connect-deployment-parameters.png)

    매개 변수 이름 | 설명 | 허용되는 값 | 샘플 값
    ---------------|-------------|----------------|-------------
    구독 | 리더의 구독입니다. | | 구독 사용
    리소스 그룹 | 리더의 리소스 그룹입니다. | | EthereumResources
    위치 | 리소스 그룹에 대한 Azure 지역입니다. | | local

8. **만들기**를 선택합니다.

배포를 완료 한 후 리더와 멤버 통신을 시작 하는 데 몇 분 정도 걸립니다. 배포를 확인 하려면 멤버의 관리 사이트를 새로 고칩니다. 멤버 노드의 상태는 실행 되어야 합니다. 

![배포 확인](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 배포 아키텍처를 선택 합니다.
> * 독립 실행형, 컨소시엄 리더 또는 컨소시엄 멤버 네트워크 배포

Ethereum 및 Azure에 대 한 자세한 내용은 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [블록 체인 기술 및 응용 프로그램](https://azure.microsoft.com/solutions/blockchain/)
