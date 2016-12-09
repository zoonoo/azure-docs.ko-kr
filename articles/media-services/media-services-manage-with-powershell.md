---
title: "PowerShell을 사용하여 Azure 미디어 서비스 계정 관리"
description: "PowerShell cmdlet를 사용하여 Azure 미디어 서비스 계정을 관리하는 방법에 대해 알아봅니다."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87f6d18ddc97921b62cbf111738044f24a0a940b


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>PowerShell을 사용하여 Azure 미디어 서비스 계정 관리
> [!div class="op_single_selector"]
> * [포털](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST (영문)](http://msdn.microsoft.com/library/azure/dn194267.aspx)
> 
> [!NOTE]
> Azure 미디어 서비스 계정을 만들려면 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 무료 평가판</a>을 참조하세요.
> 
> 

## <a name="overview"></a>개요
이 문서는 Azure Resource Manager 프레임워크의 AMS(Azure 미디어 서비스)에 대한 Azure PowerShell cmdlet을 나열합니다. Cmdlet는 **Microsoft.Azure.Commands.Media** 네임스페이스에 존재합니다.

## <a name="versions"></a>버전
**ApiVersion**: "2015-10-01"

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
미디어 서비스를 만듭니다.

### <a name="syntax"></a>구문
매개 변수 집합: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

매개 변수 집합: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | Name |
| --- | --- |
| Required? |true |
| Position? |1 |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

**-Location &lt;String&gt;**

미디어 서비스의 리소스 위치를 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |2 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-StorageAccountId &lt;String&gt;**

미디어 서비스와 연결된 기본 저장소 계정을 지정합니다.

* (리소스 관리자 API로 만든) 새 저장소 계정 만 지원함.
* 저장소 계정이 존재하며 미디어 서비스와 동일한 위치에 있어야 합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |3 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| 매개 변수 집합 이름 |StorageAccountIdParamSet |
| Accept Wildcard Characters? |false |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

미디어 서비스와 연결된 저장소 계정을 지정합니다.

* (리소스 관리자 API로 만든) 새 저장소 계정 만 지원함.
* 저장소 계정이 존재하며 미디어 서비스와 동일한 위치에 있어야 합니다.
* 단 하나의 저장소 계정 만을 기본으로 지정할 수 있습니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |3 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| 매개 변수 집합 이름 |StorageAccountsParamSet |
| Accept Wildcard Characters? |false |

**-Tags &lt;Hashtable&gt;**

미디어 서비스와 연결된 태그의 해시 테이블을 지정합니다.

* 예: @{"tag1"="value1";"tag2"=:value2"}

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position? |named |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
미디어 서비스를 만듭니다.

### <a name="syntax"></a>구문
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | Name |
| --- | --- |
| Required? |true |
| Position? |1 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |False |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

미디어 서비스와 연결된 저장소 계정을 지정합니다.

* (리소스 관리자 API로 만든) 새 저장소 계정 만 지원함.
* 저장소 계정이 존재하며 미디어 서비스와 동일한 위치에 있어야 합니다.
* 단 하나의 저장소 계정 만을 기본으로 지정할 수 있습니다.

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position? |named |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| 매개 변수 집합 이름 |StorageAccountsParamSet |
| Accept Wildcard Characters? |false |

**-Tags &lt;Hashtable&gt;**

이 미디어 서비스와 연결된 태그의 해시 테이블을 지정합니다.

* 미디어 서비스와 연결된 태그가 고객이 지정한 값으로 대체됩니다.

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position? |named |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
미디어 서비스를 제거합니다.

### <a name="syntax"></a>구문
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |2 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |False |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
지정된 이름으로 리소스 그룹 또는 미디어 서비스에 있는 모든 미디어 서비스를 가져옵니다.

### <a name="syntax"></a>구문
ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| 매개 변수 집합 이름 |ResourceGroupParameterSet, AccountNameParameterSet |

Accept Wildcard Characters?   false

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |1 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| 매개 변수 집합 이름 |AccountNameParameterSet |
| Accept Wildcard Characters? |false |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
미디어 서비스의 키를 가져옵니다.

### <a name="syntax"></a>구문
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |1 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
미디어 서비스의 기본 또는 보조 키를 다시 생성합니다.

### <a name="syntax"></a>구문
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |1 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-KeyType &lt;KeyType&gt;**

미디어 서비스의 키 유형을 지정합니다.

* 기본 또는 보조

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |2 |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
미디어 서비스와 연결된 저장소 계정에 대한 저장소 계정 키를 동기화합니다.

### <a name="syntax"></a>구문
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>매개 변수
**-ResourceGroupName &lt;String&gt;**

이 미디어 서비스가 속하는 리소스 그룹의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |0 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-AccountName &lt;String&gt;**

미디어 서비스의 이름을 지정합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position? |1 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**-StorageAccountId &lt;String&gt;**

미디어 서비스와 연결된 저장소 계정을 지정합니다.

| Aliases | Id |
| --- | --- |
| Required? |true |
| Position? |2 |
| 기본값 |없음 |
| Accept Pipeline Input? |true(ByPropertyName) |
| Accept Wildcard Characters? |false |

**&lt;CommandParameters&gt;**

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.

### <a name="inputs"></a>입력
입력 형식은 cmdlet으로 파이프할 수 있는 개체의 형식입니다.

### <a name="outputs"></a>출력
출력 형식은 cmdlet이 내보내는 개체의 형식입니다.

## <a name="next-step"></a>다음 단계
미디어 서비스 학습 경로를 확인하세요.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


