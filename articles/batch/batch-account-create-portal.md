---
title: "Azure Portal에서 배치 계정 만들기 | Microsoft Docs"
description: "클라우드에서 대규모 병렬 작업을 실행하도록 Azure 포털에서 Azure 배치 계정을 만드는 방법에 대해 알아봅니다."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 053e2ae7f382bc55a6638ccd612289ab2ba64e52
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal에서 Batch 계정 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [배치 관리 .NET](batch-management-dotnet.md)
>
>

[Azure Portal][azure_portal]에 Azure 배치 계정을 만들고 계산 시나리오에 적합한 계정 속성을 선택하는 방법에 대해 알아봅니다. 액세스 키 및 계정 URL과 같은 중요한 계정 속성을 찾는 위치에 대해 알아봅니다.

배치 계정 및 시나리오에 대한 배경은 [기능 개요](batch-api-basics.md)를 참조하세요.



## <a name="create-a-batch-account"></a>배치 계정 만들기

포털을 사용하여 두 개의 *풀 할당 모드*, 즉 **배치 서비스** 모드 또는 더 새로운 **사용자 구독** 모드 중 하나에서 배치 계정을 만들며, 이 경우 더 많은 구성이 필요합니다. 이 두 가지 모드에 대한 내용은 [기능 개요](batch-api-basics.md#account)를 참조하세요. 사용자 구독 모드의 기능에 대해서는 [블로그 게시물](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/)도 참조하세요.

## <a name="batch-service-mode"></a>배치 서비스 모드



1. [Azure Portal][azure_portal]에 로그인합니다.
2. **새로 만들기** > **계산** > **배치 서비스**를 클릭합니다.

    ![Marketplace에서 배치][marketplace_portal]
3. **새로운 배치 계정** 블레이드가 표시됩니다. 각 블레이드 요소에 대해 아래 설명을 참조하세요.

    ![배치 계정 만들기][account_portal]

    a. **계정 이름**: 선택한 배치 계정 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다(아래 **위치** 참조). 계정 이름은 소문자 또는 숫자만 포함할 수 있으며 길이는 3-24자여야 합니다.

    b. **구독**: 배치 계정을 만들 구독입니다. 하나의 구독만 보유하는 경우 기본적으로 선택됩니다.

    c. **풀 할당 모드**: **배치 서비스**를 선택합니다.

    c. **리소스 그룹**: 새 배치 계정에 대한 기존 리소스 그룹을 선택하거나 필요에 따라 새 리소스 그룹을 만듭니다.

    d. **위치**: 배치 계정을 만들 Azure 지역입니다. 구독 및 리소스 그룹에서 지원하는 지역만 옵션으로 표시됩니다.

    e. **저장소 계정**(선택 사항): 배치 계정과 연결하는 범용 Azure Storage 계정입니다. 대부분의 배치 계정에 사용하는 것이 좋습니다. 자세한 내용은 이 문서 뒷부분의 [연결된 Azure Storage 계정](#linked-azure-storage-account) 을 참조하세요.

4. **만들기** 를 클릭하여 계정을 만듭니다.

   포털에서 배포가 진행 중임을 나타냅니다. 완료되면 **알림**에 **배포에 성공했습니다.**라는 알림이 표시됩니다.

## <a name="user-subscription-mode"></a>사용자 구독 모드

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure 배치에서 구독에 액세스하도록 허용(일회성 작업)
사용자 구독 모드에서 첫 번째 배치 계정을 만들 때 다음 단계를 수행하여 구독을 배치 계정에 등록합니다. (이전에 이 작업을 수행한 경우 다음 섹션으로 건너뜁니다.)

1. [Azure Portal][azure_portal]에 로그인합니다.

2. **추가 서비스** > **구독**을 차례로 클릭하고, 배치 계정에 사용할 구독을 클릭합니다.

3. **구독** 블레이드에서 **액세스 제어(IAM)** > **추가**를 클릭합니다.

    ![구독 액세스 제어][subscription_access]

4. **권한 추가** 블레이드에서 **참가자** 역할을 선택하고 Batch API를 검색합니다. API를 찾을 때까지 다음 문자열 각각을 검색합니다.
    1. **MicrosoftAzureBatch**
    2. **Microsoft Azure Batch** - 최신 Azure AD 테넌트에서는 이 이름을 사용할 수도 있습니다.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864**는 Batch API에 대한 ID입니다. 

5. Batch API를 찾으면 해당 API를 선택하고 **저장**을 클릭합니다.

    ![배치 권한 추가][add_permission]

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기
사용자 구독 모드에서 Azure 키 자격 증명 모음은 만들려는 배치 계정과 동일한 리소스 그룹에 속해야 합니다. 배치를 [사용할 수 있고](https://azure.microsoft.com/regions/services/) 구독에서 지원하는 지역에 리소스 그룹이 있는지 확인합니다.

1. [Azure Portal][azure_portal]에서 **새로 만들기** > **보안 + ID** > **Key Vault**를 차례로 클릭합니다.

2. **Key Vault 만들기** 블레이드에서 키 자격 증명 모음의 이름을 입력하고, 배치 계정에 사용할 지역에 리소스 그룹을 만듭니다. 나머지 설정은 기본값으로 그대로 두고 **만들기**를 클릭합니다.

### <a name="create-a-batch-account"></a>배치 계정 만들기

1. [Azure Portal][azure_portal]에서 **새로 만들기** > **Compute** > **배치 서비스**를 차례로 클릭합니다.

    ![Marketplace에서 배치][marketplace_portal]
3. **새로운 배치 계정** 블레이드가 표시됩니다. 각 블레이드 요소에 대해 아래 설명을 참조하세요.

    ![배치 계정 만들기][account_portal_byos]

    a. **계정 이름**: 선택한 배치 계정 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다(아래 **위치** 참조). 계정 이름은 소문자 또는 숫자만 포함할 수 있으며 길이는 3-24자여야 합니다.

    b. **구독**: 둘 이상의 구독이 있는 경우 배치 서비스에 등록한 구독을 선택합니다.

    c. **풀 할당 모드**: **사용자 구독**을 선택합니다.

    d. **Key Vault**: 이전 섹션에서 배치 계정에 대해 만든 키 자격 증명 모음을 선택합니다. 필요에 따라 새 키 자격 증명 모음을 만듭니다. 자격 증명 모음을 선택한 후 확인란을 선택하여 해당 키 자격 증명 모음에 Azure 배치 액세스 권한을 부여합니다.

    c. **리소스 그룹**: 키 자격 증명 모음을 만든 리소스 그룹을 선택합니다.

    d. **위치**: 배치 계정에 대한 키 자격 증명 모음을 만든 Azure 지역입니다.

    e. **저장소 계정**(선택 사항): 배치 계정과 연결하는 범용 Azure Storage 계정입니다. 대부분의 배치 계정에 사용하는 것이 좋습니다. 자세한 내용은 아래의 [연결된 Azure Storage 계정](#linked-azure-storage-account) 을 참조하세요.

4. **만들기** 를 클릭하여 계정을 만듭니다.

   포털에서 배포가 진행 중임을 나타냅니다. 완료되면 **알림**에 **배포에 성공했습니다.**라는 알림이 표시됩니다.



## <a name="view-batch-account-properties"></a>배치 계정 속성 보기
계정이 만들어지면 **배치 계정 블레이드** 를 열어 해당 설정 및 속성에 액세스할 수 있습니다. 배치 계정 블레이드의 왼쪽 메뉴를 사용하여 모든 계정 설정 및 속성에 액세스할 수 있습니다.

![Azure 포털에서 배치 계정 블레이드][account_blade]

* **배치 계정 URL**: [배치 API](batch-apis-tools.md#azure-accounts-for-batch-development)로 응용 프로그램을 개발할 때 배치 리소스에 액세스하려면 계정 URL이 필요합니다. 배치 계정 URL의 형식은 다음과 같습니다.

    `https://<account_name>.<region>.batch.azure.com`

![포털에서 배치 계정 URL][account_url]

* **액세스 키**(배치 서비스 모드): 응용 프로그램에서 배치 계정에 대한 액세스를 인증하려면 계정 액세스 키가 필요합니다. (Azure Active Directory 인증을 사용하는 사용자 구독 모드에서는 이 설정을 사용할 수 없습니다.)

    배치 계정의 액세스 키를 보거나 다시 생성하려면 배치 계정 블레이드에 있는 왼쪽 메뉴 **검색** 상자에 `keys`를 입력한 다음 **키**를 선택합니다.

    ![Azure 포털에서 배치 계정 키][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>연결된 Azure Storage 계정

필요에 따라 범용 Azure Storage 계정을 배치 계정에 연결할 수도 있습니다. 배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능은 [배치 파일 규칙 .NET](batch-task-output.md) 라이브러리와 마찬가지로 Azure Blob 저장소를 사용합니다. 이러한 선택적 기능은 배치 태스크에서 실행하는 응용 프로그램을 배포하고 생성한 데이터를 유지하는 데 도움이 됩니다.

배치 계정에서 배타적으로 사용할 수 있도록 새로운 저장소 계정을 만드는 것이 좋습니다.

!["범용" 저장소 계정 만들기][storage_account]

> [!NOTE]
> Azure 배치는 현재 범용 저장소 계정 유형만 지원합니다. 이 계정 유형은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)의 5단계 [저장소 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account)에서 설명하고 있습니다.
>
>

> [!WARNING]
> 연결된 저장소 계정의 액세스 키를 다시 생성할 때는 주의해야 합니다. 하나의 저장소 계정 키를 다시 생성하고 연결된 저장소 계정 블레이드에서 **동기화 키** 를 클릭합니다. 키를 풀의 계산 노드에 전파한 다음 필요한 경우 다른 키를 다시 생성하고 동기화할 수 있으려면 5분 동안 기다립니다. 동시에 두 키를 다시 생성하는 경우 계산 노드에서 키를 동기화할 수 없으며 저장소 계정에 액세스할 수 없게 됩니다.
>
>

![ 저장소 계정 키 다시 생성][4]

## <a name="batch-service-quotas-and-limits"></a>배치 서비스 할당량 및 제한
Azure 구독 및 다른 Azure 서비스처럼 특정 [할당량 및 제한](batch-quota-limit.md) 이 배치 계정에 적용되도록 주의하세요. 배치 계정의 현재 할당량이 계정 **속성**의 포털에 표시됩니다.

![Azure 포털에서 배치 계정 할당량][quotas]



또한 이러한 할당량 대부분은 Azure Portal에 제출된 무료 제품 지원 요청으로 간단히 늘릴 수 있습니다. 할당량 증가를 요청하는 방법에 대한 자세한 내용은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md) 을 참조하세요.

## <a name="other-batch-account-management-options"></a>다른 배치 계정 관리 옵션
Azure 포털을 사용하는 것 외에도 다음 항목으로 배치 계정을 만들고 관리할 수 있습니다.

* [배치 PowerShell cmdlets](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [배치 관리 .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>다음 단계
* 배치 서비스의 개념 및 기능에 대한 자세한 내용은 [배치 기능 개요](batch-api-basics.md)를 참조하세요. 이 문서에서는 풀, 계산 노드, 작업 및 태스크 등의 기본 배치 리소스에 대해 설명하며 대규모 계산 워크로드 실행을 사용할 수 있는 서비스 기능에 대한 개요를 제공합니다.
* [배치 .NET 클라이언트 라이브러리](batch-dotnet-get-started.md) 또는 [Python](batch-python-tutorial.md)을 사용하여 배치 지원 응용 프로그램 개발에 대한 기본 사항을 알아봅니다. 이러한 소개 자료에서는 배치 서비스를 사용하여 여러 계산 노드에서 워크로드를 실행하는 작업 응용 프로그램을 단계별로 안내하며, Azure Storage를 사용하여 워크로드 파일을 준비하고 검색하는 방법을 설명합니다.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "저장소 계정 키 다시 생성"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png

