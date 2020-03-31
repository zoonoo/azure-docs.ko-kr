---
title: Azure 블록 체인 서비스 컨소시엄 구성원 관리 - PowerShell
description: Azure PowerShell을 사용하여 Azure 블록 체인 서비스 컨소시엄 구성원을 관리하는 방법을 알아봅니다.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505989"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>PowerShell을 사용하여 Azure 블록 체인 서비스에서 컨소시엄 구성원 관리

PowerShell을 사용하여 Azure 블록 체인 서비스에 대한 블록 체인 컨소시엄 멤버를 관리 할 수 있습니다. 관리자 권한이 있는 구성원은 블록 체인 컨소시엄의 모든 참가자에 대한 역할을 초대, 추가, 제거 및 변경할 수 있습니다. 사용자 권한이 있는 회원은 블록 체인 컨소시엄의 모든 참가자를 보고 구성원 표시 이름을 변경할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure 포털을](create-member.md)사용하여 블록 체인 멤버를 만듭니다.
* 컨소시엄, 멤버 및 노드에 대한 자세한 내용은 [Azure 블록체인 서비스 컨소시엄을](consortium.md)참조하십시오.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell 열기

Azure Cloud Shell은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

shell.azure.com/powershell [이동하여](https://shell.azure.com/powershell)별도의 브라우저 탭에서 클라우드 셸을 열 수도 있습니다. **복사를** 선택하여 코드 블록을 복사하고 클라우드 셸에 붙여넣은 다음 **Enter를** 선택하여 실행합니다.

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

PowerShell 갤러리에서 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 패키지를 설치합니다.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>정보 기본 설정

정보 기본 설정 변수를 설정하여 cmdlet을 실행할 때 자세한 정보를 얻을 수 있습니다. 기본적으로 *$InformationPreference* *자동으로 설정됩니다.*

cmdlet의 자세한 내용은 PowerShell에서 다음과 같이 기본 설정을 설정합니다.

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3 연결 설정

컨소시엄 멤버를 관리하려면 블록체인 서비스 멤버 엔드포인트에 대한 Web3 연결을 설정합니다. 이 스크립트를 사용하여 컨소시엄 관리 cmdlet을 호출하기 위한 전역 변수를 설정할 수 있습니다.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

* \<회원 계정\> 암호를 회원 계정* 생성 시 사용한 멤버 계정 암호로 바꿉습니다.

Azure 포털에서 다른 값을 찾습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 기본 블록체인 서비스 회원 **개요** 페이지로 이동합니다.

    ![회원 개요](./media/manage-consortium-powershell/member-overview.png)

    * \<멤버 계정\> * 및 * \<RootContract 주소를\> * 포털의 값으로 바꿉습니다.

1. 끝점 주소의 경우 **트랜잭션 노드를**선택한 다음 **기본 트랜잭션 노드를**선택합니다. 기본 노드의 이름은 블록 체인 멤버와 같습니다.
1. **연결 문자열을 선택합니다.**

    ![연결 문자열](./media/manage-consortium-powershell/connection-strings.png)

    * \<끝점 주소를\> * **HTTPS(액세스 키 1)** 또는 **HTTPS(액세스 키 2)의 값으로**바꿉니다.

## <a name="manage-the-network-and-smart-contracts"></a>네트워크 및 스마트 계약 관리

네트워크 및 스마트 계약 cmdlet을 사용하여 컨소시엄 관리를 담당하는 블록 체인 엔드포인트의 스마트 계약에 대한 연결을 설정합니다.

### <a name="import-consortiummanagementcontracts"></a>수입 컨소시엄관리계약

이 cmdlet을 사용하여 컨소시엄 관리의 스마트 계약에 연결합니다. 이러한 계약은 컨소시엄 내의 구성원을 관리하고 집행하는 데 사용됩니다.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 루트 계약 주소 | 컨소시엄 관리 스마트 계약의 루트 계약 주소 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>가져오기-웹3계정

이 cmdlet을 사용하여 원격 노드의 관리 계정에 대한 정보를 보유하는 개체를 만듭니다.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 관리 계정 주소 | 블록체인 회원 계정 주소 | yes |
| 관리 계정암호 | 계정 주소 암호 | yes |

#### <a name="example"></a>예제

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>새 웹3연결

이 cmdlet을 사용하여 트랜잭션 노드의 RPC 끝점에 대한 연결을 설정합니다.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 원격RPC엔드포인트 | 블록체인 구성원 엔드포인트 주소 | yes |

#### <a name="example"></a>예제

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>컨소시엄 구성원 관리

컨소시엄 구성원 관리 cmdlet을 사용하여 컨소시엄 내의 구성원을 관리합니다. 사용 가능한 작업은 컨소시엄 역할에 따라 다릅니다.

### <a name="get-blockchainmember"></a>Get-블록체인회원

이 cmdlet을 사용하여 컨소시엄의 멤버 세부 정보 또는 목록 멤버를 가져옵니다.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 이름 | 세부 정보를 검색하려는 블록 체인 서비스 멤버의 이름입니다. 이름을 입력하면 멤버의 세부 정보를 반환합니다. 이름을 생략하면 모든 컨소시엄 멤버 의 목록을 반환합니다. | 예 |
| 멤버 | 멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 변수를 설정합니다.

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

### <a name="remove-blockchainmember"></a>제거-블록체인회원

이 cmdlet을 사용하여 블록 체인 멤버를 제거하십시오.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 이름 | 제거할 회원 이름 | yes |
| 멤버 | 멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3계정 | Web3계정 가져오기-Web3Account에서 얻은 개체 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>세트 블록체인회원

이 cmdlet을 사용하여 표시 이름 및 컨소시엄 역할을 비롯한 블록 체인 멤버 특성을 설정합니다.

컨소시엄 관리자는 모든 구성원에 대해 **DisplayName** 및 **역할을** 설정할 수 있습니다. 사용자 역할이 있는 컨소시엄 멤버는 자신의 구성원의 표시 이름만 변경할 수 있습니다.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 이름 | 블록체인 회원의 이름 | yes |
| DisplayName | 새 표시 이름 | 예 |
| 계정 주소 지정 | 계정 주소 | 예 |
| 멤버 | 멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3계정 | Web3계정 가져오기-Web3Account에서 얻은 개체 | yes |
| 웹3클라이언트 |  New-Web3Connection에서 얻은 Web3Client 개체| yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>컨소시엄 구성원의 초대 관리

컨소시엄 구성원 초대 관리 cmdlet을 사용하여 컨소시엄 구성원의 초대를 관리합니다. 사용 가능한 작업은 컨소시엄 역할에 따라 다릅니다.

### <a name="new-blockchainmemberinvitation"></a>뉴 블록체인회원초대

이 cmdlet을 사용하여 새 멤버를 컨소시엄에 초대합니다.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 구성원의 Azure 구독 ID | yes |
| 역할 | 컨소시엄 역할입니다. 값은 관리자 또는 사용자일 수 있습니다. 관리자는 컨소시엄 관리자 역할입니다. USER는 컨소시엄 멤버 역할입니다. | yes |
| 멤버 | 멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3계정 | Web3계정 가져오기-Web3Account에서 얻은 개체 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-블록체인회원초대

이 cmdlet을 사용하여 컨소시엄 구성원의 초대 상태를 검색하거나 나열합니다.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 구성원의 Azure 구독 ID입니다. 구독 ID가 제공되면 구독 ID의 초대 세부 정보를 반환합니다. 구독 ID를 생략하면 모든 구성원 초대 목록이 반환됩니다. | 예 |
| 멤버 | 멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>예제 출력

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>제거 -블록 체인회원 초대

이 cmdlet을 사용하여 컨소시엄 구성원의 초대를 취소합니다.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 해지할 구성원의 Azure 구독 ID | yes |
| 멤버 | 멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3계정 | Web3계정 가져오기-Web3Account에서 얻은 개체 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>세트-블록체인회원초대

이 cmdlet을 사용하여 기존 초대에 대한 **역할을** 설정합니다. 컨소시엄 관리자만 초대를 변경할 수 있습니다.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 구성원의 Azure 구독 ID | yes |
| 역할 | 초대를 위한 새 컨소시엄 역할입니다. 값은 **사용자** 또는 **ADMIN일**수 있습니다. | yes |
| 멤버 |  멤버 개체 가져오기-컨소시엄관리계약에서 얻은 | yes |
| 웹3계정 | Web3계정 가져오기-Web3Account에서 얻은 개체 | yes |
| 웹3클라이언트 | New-Web3Connection에서 얻은 Web3Client 개체 | yes |

#### <a name="example"></a>예제

[web3 연결을 설정하여](#establish-a-web3-connection) $ContractConnection 및 $MemberAccount 변수를 설정합니다.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>다음 단계

컨소시엄, 멤버 및 노드에 대한 자세한 내용은 [Azure 블록체인 서비스 컨소시엄을](consortium.md) 참조하십시오.
