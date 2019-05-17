---
title: PowerShell을 사용 하 여 azure Blockchain Service 컨소시엄 관리
description: PowerShell을 사용 하 여 Azure Blockchain Service 컨소시엄 멤버를 관리 하는 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: f15fa3b4972a2ac54d1d9bce916fdd42c2951d2f
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550876"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>PowerShell을 사용 하 여 Azure Blockchain 서비스에서 컨소시엄 멤버 관리

Azure Blockchain 서비스용 blockchain 컨소시엄 멤버를 관리 하려면 PowerShell을 사용할 수 있습니다. 멤버는 관리자 권한으로 수 초대, 추가, 제거 하 고 blockchain 컨소시엄에 모든 참가자에 대 한 역할을 변경 합니다. 멤버 사용자 권한으로 blockchain 컨소시엄의 모든 참가자가 볼 수 및 멤버 표시 이름만 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Azure portal을 사용 하 여 블록 체인 멤버를 만들려면](create-member.md)
* Consortia, 멤버 및 노드에 대 한 자세한 내용은 참조 하세요. [Azure Blockchain Service 컨소시엄](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="install-powershell-module"></a>PowerShell 모듈 설치

PowerShell 갤러리에서 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 패키지를 설치 합니다.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-information-preference"></a>설정 정보 기본 설정

자세한 내용은 기본 설정 변수를 설정 하 여 cmdlet을 실행 하는 경우 얻을 수 있습니다. 기본적으로 *$InformationPreference* 로 설정 된 *SilentlyContinue*합니다.

Cmdlet에서 더 자세한 내용은 powershell에서 기본 설정이 다음과 같이 설정 합니다.

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3 연결

컨소시엄 멤버를 관리 하려면 Azure Blockchain 서비스 멤버 엔드포인트에 Web3 연결 해야 합니다. 컨소시엄 관리 cmdlet을 호출할 때 사용할 수 있는 전역 변수를 설정 하려면이 스크립트를 사용할 수 있습니다.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

바꿉니다 \<멤버 계정 암호\> 멤버를 만들 때 사용한 멤버 계정 암호를 사용 합니다.

Azure portal에서 다른 값을 찾습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 기본 Azure Blockchain 서비스 멤버를 이동할 **개요** 페이지입니다.

    ![멤버 개요](./media/manage-consortium-powershell/member-overview.png)

    바꿉니다 \<회원 계정\>, 및 \<RootContract 주소\> 포털의 값을 사용 하 여 합니다.

1. 끝점 주소에 대해 선택 **트랜잭션 노드** 선택 합니다 **기본** 트랜잭션 노드. 기본 트랜잭션 노드는 blockchain 멤버와 동일한 이름이 있습니다.
1. **연결 문자열**을 선택합니다.

    ![연결 문자열](./media/manage-consortium-powershell/connection-strings.png)

    바꿉니다 \<끝점 주소\> 의 값으로 **HTTPS (액세스 키 1)** 하거나 **HTTPS (액세스 키 2)** 합니다.

## <a name="network-and-smart-contract-management"></a>네트워크 및 스마트 계약 관리

에 연결 하 여 블록 체인 끝점 smart contracts 컨소시엄 관리를 담당 하는 네트워크 및 스마트 계약 cmdlet을 사용 합니다.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

컨소시엄 관리 스마트 계약, 관리 및 적용할 컨소시엄 내에서 멤버에 사용 되는에 연결 합니다.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| RootContractAddress | 컨소시엄 관리 스마트 계약의 계약 루트 주소 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

이 cmdlet을 사용 하 여 원격 노드 관리 계정 정보를 저장 하는 개체를 만듭니다.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 블록 체인 멤버 계정 주소 | 예. |
| ManagedAccountPassword | 계정 주소 암호 | 예. |

**예제**

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

트랜잭션 노드 RPC 끝점에 연결을 설정합니다.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 블록 체인 멤버 끝점 주소 | 예. |

**예제**

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>컨소시엄 멤버 관리

컨소시엄 내에서 멤버를 관리 하려면 컨소시엄 멤버 관리 cmdlet을 사용 합니다. 사용 가능한 작업 컨소시엄 역할에 따라 달라 집니다.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

컨소시엄의 목록 멤버 또는 멤버 정보를 가져옵니다.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| 이름 | 세부 정보를 검색 하려는 Azure Blockchain 서비스 멤버의 이름입니다. 멤버 이름을 제공 하면 멤버의 세부 정보 반환 됩니다. 이름이 생략 된 경우 모든 컨소시엄 멤버 목록이 반환 됩니다. | 아닙니다. |
| 구성원 | 가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**예제 출력**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

블록 체인 멤버를 제거 합니다.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| 이름 | 제거할 멤버 이름 | 예. |
| 구성원 | 가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Account | 가져오기-Web3Account에서 가져온 Web3Account 개체 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

멤버 특성 표시 이름 및 컨소시엄 역할을 포함 하 여 블록 체인을 설정 합니다.

컨소시엄 관리자 설정할 수 있습니다 **DisplayName** 하 고 **역할** 모든 멤버에 대 한 합니다. 사용자 역할을 사용 하 여 컨소시엄 멤버 자체 멤버의 표시 이름을 변경할 수 있습니다.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| 이름 | 블록 체인 멤버의 이름 | 예. |
| DisplayName | 새 표시 이름 | 아닙니다. |
| AccountAddress | 계정 주소 | 아닙니다. |
| 구성원 | 가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Account | 가져오기-Web3Account에서 가져온 Web3Account 개체 | 예. |
| Web3Client |  새로 만들기-Web3Connection에서 가져온 Web3Client 개체| 예. |

**예제**

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>컨소시엄 멤버 초대 관리

컨소시엄 멤버 초대 관리 cmdlet를 사용 하 여 컨소시엄 멤버 초대를 관리할 수 있습니다. 사용 가능한 작업 컨소시엄 역할에 따라 달라 집니다.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

컨소시엄에 새 구성원을 초대 합니다.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대 된 구성원의 azure 구독 ID | 예. |
| 역할 | 컨소시엄 역할입니다. 값에는 관리자 또는 사용자 수 있습니다. 관리자는 컨소시엄 관리자 역할입니다. 사용자는 컨소시엄 멤버 역할입니다. | 예. |
| 구성원 | 가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Account | 가져오기-Web3Account에서 가져온 Web3Account 개체 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

검색 하거나 컨소시엄 멤버 초대 상태를 나열 합니다.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대 된 구성원의 azure 구독 ID입니다. SubscriptionID를 제공 하는 경우 구독 ID의 초대 세부 정보가 반환 됩니다. SubscriptionID를 생략 하면 모든 멤버 초대 목록이 반환 됩니다. | 아닙니다. |
| 구성원 | 가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**예제 출력**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

컨소시엄 멤버 초대를 취소합니다.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 멤버에 게 서 취소할의 azure 구독 ID | 예. |
| 구성원 | 가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Account | 가져오기-Web3Account에서 가져온 Web3Account 개체 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

설정 된 **역할** 기존 초대 합니다. 컨소시엄 관리자만 초대를 변경할 수 있습니다.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대 된 구성원의 azure 구독 ID | 예. |
| 역할 | 초대에 대 한 새 컨소시엄 역할입니다. 값은 **사용자** 또는 **관리자** | 예. |
| 구성원 |  가져오기-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예. |
| Web3Account | 가져오기-Web3Account에서 가져온 Web3Account 개체 | 예. |
| Web3Client | 새로 만들기-Web3Connection에서 가져온 Web3Client 개체 | 예. |

**예제**

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>다음 단계

Consortia, 멤버 및 노드에 대 한 자세한 내용은 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Azure Blockchain 서비스 컨소시엄](consortium.md)