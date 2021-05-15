---
title: Azure Blockchain Service 컨소시엄 멤버 관리 - PowerShell
description: Azure PowerShell을 사용하여 Azure Blockchain Service 컨소시엄 멤버를 관리하는 방법을 알아봅니다.
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: d40e55f177bda9edb40383b6e2c61c32633cd005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85211343"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>PowerShell을 사용하여 Azure Blockchain Service의 컨소시엄 구성원 관리

PowerShell을 사용하여 Azure Blockchain Service의 블록체인 컨소시엄 멤버를 관리할 수 있습니다. 관리자 권한이 있는 멤버는 블록체인 컨소시엄의 모든 참가자를 초대, 추가, 제거하고 역할을 변경할 수 있습니다. 사용자 권한이 있는 멤버는 블록체인 컨소시엄의 모든 참가자를 보고 멤버 표시 이름을 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Portal](create-member.md)을 사용하여 블록체인 멤버를 만듭니다.
* 컨소시엄, 멤버, 노드에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell 열기

Azure Cloud Shell은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

또한 [shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하고 Cloud Shell에 붙여넣은 다음 **입력** 을 선택하여 실행합니다.

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

PowerShell 갤러리에서 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 패키지를 설치합니다.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>정보 기본 설정 지정

정보 기본 설정 변수를 설정하여 cmdlet을 실행할 때 자세한 정보를 얻을 수 있습니다. 기본적으로 *$InformationPreference* 은 *SilentlyContinue* 로 설정되어 있습니다.

cmdlet의 자세한 정보를 보려면 다음과 같이 PowerShell에서 기본 설정을 지정합니다.

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3 연결 설정

컨소시엄 멤버를 관리하려면 Blockchain Service 멤버 엔드포인트에 대한 Web3 연결을 설정합니다. 해당 스크립트를 사용하여 컨소시엄 관리 cmdlet을 호출하는 전역 변수를 설정할 수 있습니다.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

*\<Member account password\>* 를 멤버를 만들 때 사용한 멤버 계정 암호로 바꿉니다.

Azure Portal에서 다른 값을 찾습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 기본 Blockchain Service 멤버 **개요** 페이지로 이동합니다.

    ![멤버 개요](./media/manage-consortium-powershell/member-overview.png)

    *\<Member account\>* 및 *\<RootContract address\>* 를 포털의 값으로 바꿉니다.

1. 엔드포인트 주소의 경우 **트랜잭션 노드** 를 선택한 다음 **기본 트랜잭션 노드** 를 선택합니다. 기본 노드와 블록체인 멤버는 이름이 동일합니다.
1. **연결 문자열** 을 선택합니다.

    ![연결 문자열](./media/manage-consortium-powershell/connection-strings.png)

    *\<Endpoint address\>* 를 **HTTPS(액세스 키 1)** 또는 **HTTPS(액세스 키 2)** 로 바꿉니다.

## <a name="manage-the-network-and-smart-contracts"></a>네트워크 및 스마트 계약 관리

네트워크 및 스마트 계약 cmdlet을 사용하여 컨소시엄 관리를 담당하는 블록체인 엔드포인트의 스마트 계약에 대한 연결을 설정합니다.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

해당 cmdlet을 사용하여 컨소시엄 관리의 스마트 계약에 연결합니다. 이러한 계약은 컨소시엄 내에서 멤버를 관리하고 적용하는 데 사용됩니다.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| RootContractAddress | 컨소시엄 관리 스마트 계약의 루트 계약 주소 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

해당 cmdlet을 사용하여 원격 노드의 관리 계정에 대한 정보를 저장하는 개체를 만듭니다.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 블록체인 멤버 계정 주소 | 예 |
| ManagedAccountPassword | 계정 주소 암호 | 예 |

#### <a name="example"></a>예제

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

해당 cmdlet을 사용하여 트랜잭션 노드의 RPC 엔드포인트에 대한 연결을 설정합니다.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 블록체인 멤버 엔드포인트 주소 | 예 |

#### <a name="example"></a>예제

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>컨소시엄 멤버 관리

컨소시엄 멤버 관리 cmdlet을 사용하여 컨소시엄 내에서 멤버를 관리합니다. 사용 가능한 작업은 컨소시엄 역할에 따라 다릅니다.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

해당 cmdlet을 사용하여 컨소시엄의 멤버 세부 정보를 가져오거나 멤버를 나열합니다.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| 이름 | 세부 정보를 검색할 Blockchain Service 멤버의 이름입니다. 이름을 입력하면 멤버의 세부 정보를 반환합니다. 이름을 생략하면 모든 컨소시엄 멤버 목록을 반환합니다. | 예 |
| 구성원 | Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>예제 출력

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

해당 cmdlet을 사용하여 블록체인 멤버를 제거합니다.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| 이름 | 제거할 멤버 이름 | 예 |
| 구성원 | Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Account | Import-Web3Account에서 가져온 Web3Account 개체 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

해당 cmdlet을 사용하여 표시 이름 및 컨소시엄 역할을 포함한 블록체인 멤버 특성을 설정할 수 있습니다.

컨소시엄 관리자는 모든 멤버에 대해 **DisplayName** 및 **역할** 을 설정할 수 있습니다. 사용자 역할의 컨소시엄 멤버는 자신의 멤버 표시 이름만 변경할 수 있습니다.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| 이름 | 블록체인 멤버의 이름 | 예 |
| DisplayName | 새 표시 이름 | 예 |
| AccountAddress | 계정 주소 | 예 |
| 구성원 | Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Account | Import-Web3Account에서 가져온 Web3Account 개체 | 예 |
| Web3Client |  New-Web3Connection에서 가져온 Web3Client 개체| 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>컨소시엄 멤버 초대 관리

컨소시엄 멤버 초대 관리 cmdlet을 사용하여 컨소시엄 회원 초대를 관리합니다. 사용 가능한 작업은 컨소시엄 역할에 따라 다릅니다.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

해당 cmdlet을 사용하여 새 멤버를 컨소시엄에 초대합니다.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 멤버의 Azure 구독 ID | 예 |
| 역할 | 컨소시엄 역할입니다. 값은 관리자 또는 사용자일 수 있습니다. 관리자는 컨소시엄 관리자 역할입니다. 사용자는 컨소시엄 멤버 역할입니다. | 예 |
| 구성원 | Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Account | Import-Web3Account에서 가져온 Web3Account 개체 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

해당 cmdlet을 사용하여 컨소시엄 멤버의 초대 상태를 검색하거나 나열합니다.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 멤버의 Azure 구독 ID입니다. 구독 ID를 제공하는 경우 구독 ID의 초대 세부 정보를 반환합니다. 구독 ID를 생략하는 경우 모든 멤버 초대의 목록을 반환합니다. | 예 |
| 구성원 | Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>예제 출력

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

해당 cmdlet을 사용하여 컨소시엄 멤버의 초대를 철회합니다.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 해지할 멤버의 Azure 구독 ID | 예 |
| 구성원 | Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Account | Import-Web3Account에서 가져온 Web3Account 개체 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

해당 cmdlet을 사용하여 기존 초대에 대한 **역할** 을 설정합니다. 컨소시엄 관리자만 초대를 변경할 수 있습니다.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | Description | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 멤버의 Azure 구독 ID | 예 |
| 역할 | 초대에 대한 새 컨소시엄 역할입니다. 값은 **사용자** 또는 **관리자** 일 수 있습니다. | 예 |
| 구성원 |  Import-ConsortiumManagementContracts에서 가져온 멤버 개체 | 예 |
| Web3Account | Import-Web3Account에서 가져온 Web3Account 개체 | 예 |
| Web3Client | New-Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예제

[Web3 연결을 설정](#establish-a-web3-connection)하여 $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>다음 단계

컨소시엄, 멤버, 노드에 대한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조하세요.
