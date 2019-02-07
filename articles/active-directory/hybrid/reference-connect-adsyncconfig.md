---
title: 'Azure AD Connect: ADSyncConfig PowerShell 참조 | Microsoft Docs'
description: 이 문서에서는 ADSyncConfig.psm1 PowerShell 모듈에 대한 참조 정보를 제공합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 19b7cebd1c75e1a2bbfe34e3ab4e657051e8a6d4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159535"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  ADSyncConfig PowerShell 참조
이 설명서에서는 Azure AD Connect에 포함된 ADSyncConfig.psm1 PowerShell 모듈에 대해 다음과 같은 참조 정보를 제공합니다.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>개요
각 AD 커넥터에 구성된 계정 이름과 도메인을 가져옵니다.

### <a name="syntax"></a>구문

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>설명
이 함수는 AAD Connect에 있는 'Get-ADSyncConnector' cmdlet을 사용하여 연결 매개 변수에서 AD 커넥터 계정을 보여 주는 테이블을 검색합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>개요
권한 상속을 사용하지 않도록 설정된 AD 개체를 가져옵니다.

### <a name="syntax"></a>구문

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>설명
SearchBase 매개 변수에서 시작하여 AD에서 검색하고, ObjectClass 매개 변수로 필터링하여 현재 ACL 상속을 사용하지 않도록 설정된 모든 개체를 반환합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>예제 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>예제 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>매개 변수

#### <a name="-searchbase"></a>-SearchBase
AD 도메인 DistinguishedName 또는 FQDN일 수 있는 LDAP 쿼리에 대한 SearchBase입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
'*'(모든 개체 클래스), 'user', 'group', 'container' 등일 수 있는 검색할 개체의 클래스입니다. 기본적으로 이 함수는 'organizationalUnit' 개체 클래스를 검색합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>개요
기본 읽기 권한을 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncBasicReadPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
모든 하위 computer 개체의 모든 특성에 대한 읽기 속성 액세스
2.
모든 하위 device 개체의 모든 특성에 대한 읽기 속성 액세스
3.
모든 하위 foreignsecurityprincipal 개체의 모든 특성에 대한 읽기 속성 액세스
5.
모든 하위 user 개체의 모든 특성에 대한 읽기 속성 액세스
6.
모든 하위 inetorgperson 개체의 모든 특성에 대한 읽기 속성 액세스
7.
모든 하위 group 개체의 모든 특성에 대한 읽기 속성 액세스
8.
모든 하위 contact 개체의 모든 특성에 대한 읽기 속성 액세스

이러한 권한은 포리스트의 모든 도메인에 적용됩니다.
필요에 따라 ADobjectDN 매개 변수에 DistinguishedName을 제공하여 해당 AD 개체(하위 개체에 대한 상속 포함)에 대해서만 이러한 권한을 설정할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>예제 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>예제 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
권한을 설정할 대상 AD 개체의 DistinguishedName입니다(선택 사항).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
AdminSDHolder 컨테이너를 이러한 권한으로 업데이트하지 않아야 함을 나타내는 선택적 매개 변수입니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>개요
Exchange 하이브리드 기능을 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncExchangeHybridPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
모든 하위 user 개체의 모든 특성에 대한 읽기/쓰기 속성 액세스
2.
모든 하위 inetorgperson 개체의 모든 특성에 대한 읽기/쓰기 속성 액세스
3.
모든 하위 group 개체의 모든 특성에 대한 읽기/쓰기 속성 액세스
4.
모든 하위 contact 개체의 모든 특성에 대한 읽기/쓰기 속성 액세스

이러한 권한은 포리스트의 모든 도메인에 적용됩니다.
필요에 따라 ADobjectDN 매개 변수에 DistinguishedName을 제공하여 해당 AD 개체(하위 개체에 대한 상속 포함)에 대해서만 이러한 권한을 설정할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>예제 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>예제 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
권한을 설정할 대상 AD 개체의 DistinguishedName입니다(선택 사항).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
AdminSDHolder 컨테이너를 이러한 권한으로 업데이트하지 않아야 함을 나타내는 선택적 매개 변수입니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>개요
Exchange 메일 공용 폴더 기능을 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncExchangeMailPublicFolderPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
모든 하위 publicfolder 개체의 모든 특성에 대한 읽기 속성 액세스

이러한 권한은 포리스트의 모든 도메인에 적용됩니다.
필요에 따라 ADobjectDN 매개 변수에 DistinguishedName을 제공하여 해당 AD 개체(하위 개체에 대한 상속 포함)에 대해서만 이러한 권한을 설정할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>예제 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>예제 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
권한을 설정할 대상 AD 개체의 DistinguishedName입니다(선택 사항).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
AdminSDHolder 컨테이너를 이러한 권한으로 업데이트하지 않아야 함을 나타내는 선택적 매개 변수입니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>개요
mS-DS-ConsistencyGuid 기능을 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncMsDsConsistencyGuidPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
모든 하위 user 개체의 mS-DS-ConsistencyGuid 특성에 대한 읽기/쓰기 속성 액세스

이러한 권한은 포리스트의 모든 도메인에 적용됩니다.
필요에 따라 ADobjectDN 매개 변수에 DistinguishedName을 제공하여 해당 AD 개체(하위 개체에 대한 상속 포함)에 대해서만 이러한 권한을 설정할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>예제 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>예제 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
권한을 설정할 대상 AD 개체의 DistinguishedName입니다(선택 사항).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
AdminSDHolder 컨테이너를 이러한 권한으로 업데이트하지 않아야 함을 나타내는 선택적 매개 변수입니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>개요
암호 해시 동기화를 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncPasswordHashSyncPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
디렉터리 변경 내용 복제
2.
모든 디렉터리 변경 내용 복제

이러한 권한은 포리스트의 모든 도메인에 부여됩니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>개요
Azure AD의 암호 쓰기 저장을 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncPasswordWritebackPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
하위 user 개체의 암호 재설정
2.
모든 하위 user 개체의 lockoutTime 특성에 대한 읽기 속성 액세스
3.
모든 하위 user 개체의 pwdLastSet 특성에 대한 쓰기 속성 액세스

이러한 권한은 포리스트의 모든 도메인에 적용됩니다.
필요에 따라 ADobjectDN 매개 변수에 DistinguishedName을 제공하여 해당 AD 개체(하위 개체에 대한 상속 포함)에 대해서만 이러한 권한을 설정할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>예제 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>예제 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
권한을 설정할 대상 AD 개체의 DistinguishedName입니다(선택 사항).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
AdminSDHolder 컨테이너를 이러한 권한으로 업데이트하지 않아야 함을 나타내는 선택적 매개 변수입니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>개요
다른 방법으로 AD 보호된 보안 그룹에 포함되지 않은 AD 개체에 대한 권한을 강화합니다.
일반적인 예는 AAD Connect에서 자동으로 만든 AD Connect 계정(MSOL)입니다.
이 계정에는 모든 도메인에 대한 복제 권한이 있지만 보호되지 않으므로 쉽게 손상될 수 있습니다.

### <a name="syntax"></a>구문

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncRestrictedPermissions 함수는 제공된 계정에 대한 권한을 강화합니다.
권한 강화에 포함되는 단계는 다음과 같습니다.
1.
지정된 개체에서 상속을 사용하지 않도록 설정합니다.
2.
특정 개체에서 SELF와 관련된 ACE를 제외하고 ACE를 모두 제거합니다.
SELF의 경우 기본 사용 권한을 그대로 유지할 수 있습니다.
3.
다음과 같은 특정 권한을 할당합니다.

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
권한을 강화해야 하는 Active Directory 계정의 DistinguishedName입니다.
일반적으로 AD 커넥터에 구성된 MSOL_nnnnnnnnnn 계정 또는 사용자 지정 도메인 계정입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credential"></a>-Credential
ADConnectorAccountDN 계정에 대한 권한을 제한하는 데 필요한 권한이 있는 관리자 자격 증명입니다. 일반적으로 엔터프라이즈 또는 도메인 관리자입니다. 계정 조회 오류를 방지하려면 관리자 계정의 정규화된 도메인 이름을 사용합니다.
예제: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
DisableCredentialValidation이 사용되는 경우 이 함수는 -Credential에 제공된 자격 증명이 AD에서 유효한지와 제공된 계정에 ADConnectorAccountDN 계정에 대한 권한을 제한하는 데 필요한 권한이 있는지를 확인하지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>개요
Azure AD의 그룹 쓰기 저장을 위해 Active Directory 포리스트 및 도메인을 초기화합니다.

### <a name="syntax"></a>구문

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
Set-ADSyncUnifiedGroupWritebackPermissions 함수에서 부여하는 AD 동기화 계정에 필요한 권한은 다음과 같습니다.
1.
모든 그룹 Object 유형 및 SubObjects에 대한 일반 읽기/쓰기, 삭제, 트리 삭제 및 자식 만들기/삭제

이러한 권한은 포리스트의 모든 도메인에 적용됩니다.
필요에 따라 ADobjectDN 매개 변수에 DistinguishedName을 제공하여 해당 AD 개체(하위 개체에 대한 상속 포함)에 대해서만 이러한 권한을 설정할 수 있습니다.
이 경우 ADobjectDN은 GroupWriteback 기능과 연결하려는 컨테이너의 고유 이름입니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>예제 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>예제 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>예제 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 이름입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 도메인입니다.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 동기화에서 디렉터리의 개체를 관리하는 데 사용하거나 사용할 Active Directory 계정의 DistinguishedName입니다.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
권한을 설정할 대상 AD 개체의 DistinguishedName입니다(선택 사항).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
AdminSDHolder 컨테이너를 이러한 권한으로 업데이트하지 않아야 함을 나타내는 선택적 매개 변수입니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>개요
지정된 AD 개체의 권한을 표시합니다.

### <a name="syntax"></a>구문

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>설명
이 함수는 매개 변수 -ADobjectDN에 제공된 특정 AD 개체에 대해 현재 설정된 모든 AD 권한을 반환합니다.
ADobjectDN은 DistinguishedName 형식으로 제공해야 합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>매개 변수

#### <a name="-adobjectdn"></a>-ADobjectDN
{{ADobjectDN 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.
