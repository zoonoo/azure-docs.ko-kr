<properties
	pageTitle="Azure 배치 계정 만들기 | Microsoft Azure"
	description="클라우드에서 대규모 병렬 작업을 실행하도록 Azure 포털에서 Azure 배치 계정을 만드는 방법에 대해 알아봅니다."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="marsma"/>

# Azure 포털에서 Azure 배치 계정 만들기 및 관리

> [AZURE.SELECTOR]
- [Azure 포털](batch-account-create-portal.md)
- [배치 관리 .NET](batch-management-dotnet.md)

[Azure 포털][azure_portal]은 대규모 병렬 워크로드 처리에 사용할 수 있는 Azure 배치 계정을 만들고 관리하는 데 필요한 도구를 제공합니다. 이 문서에서는 포털을 사용하여 배치 계정을 만드는 방법을 안내하고 배치 계정에 가장 중요한 몇 가지 설정과 속성에 대해 설명합니다. 예를 들어, 배치로 개발하는 응용 프로그램 및 서비스에는 배치 서비스 API와 통신하기 위한 계정의 URL과 액세스 키가 필요하며 모두 Azure 포털에서 확인할 수 있습니다.

>[AZURE.NOTE] 현재 Azure 포털은 계정 만들기 및 계정 설정 및 속성의 관리를 포함하여 배치 서비스에 사용할 수 있는 기능의 하위 집합을 지원합니다. 작업 및 태스크 만들기 및 실행을 포함한 배치의 전체 기능 집합은 배치 API를 통해 개발자에게 제공됩니다.

## 배치 계정 만들기

1. [Azure 포털][azure_portal]에 로그인합니다.

2. **새로 만들기** > **계산** > **배치 서비스**를 클릭합니다.

	![Marketplace에서 배치][marketplace_portal]

3. **배치 서비스** 블레이드에서 정보를 확인한 다음 **만들기**를 클릭합니다. 배포 모델 선택은 사용하지 않도록 합니다. 배치에서 리소스 그룹 배포 모델만 사용하기 때문입니다.

	![배치 서비스로 Azure 포털에서 블레이드 만들기][3]

4. **새로운 배치 계정** 블레이드가 표시됩니다. 각 블레이드 요소에 대한 설명은 아래 *a* ~ *e* 항목을 참조하세요.

    ![배치 계정 만들기][account_portal]

	a. **계정 이름** - 배치 계정에 대한 고유한 이름을 지정합니다. 이 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다(아래 *위치* 참조). 소문자, 숫자만 포함할 수 있으며 길이는 3-24자여야 합니다.

	b. **구독** - 배치 계정을 만들 구독을 선택합니다. 하나의 구독만 보유하는 경우 기본적으로 선택됩니다.

	c. **리소스 그룹** - 새 배치 계정에 대한 리소스 그룹을 선택하거나 구독에 리소스 그룹이 없는 경우 새로 만듭니다.

	d. **위치** - 배치 계정을 만들 Azure 지역을 선택합니다. 구독 및 리소스 그룹에서 지원하는 지역만 옵션으로 표시됩니다.

    e. **저장소 계정**(선택 사항) - 저장소 계정을 새 배치 계정에 연결(링크)할 수 있습니다. 배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능은 저장소를 위한 연결된 저장소 계정과 응용 프로그램 패키지의 검색을 사용합니다. 이 기능에 대한 자세한 내용은 [Azure 배치 응용 프로그램 패키지를 사용하여 응용 프로그램 배포](batch-application-packages.md)를 참조하세요.

     > [AZURE.TIP] 연결된 저장소 계정에 키를 다시 생성하려면 특별히 고려할 사항이 있습니다. 자세한 내용은 [배치 계정을 위한 고려 사항](#considerations-for-batch-accounts)을 참조하세요.

5. **만들기**를 클릭하여 계정을 만듭니다.

  포털은 계정을 **배포 중**임을 나타내고 완료 시 배치 계정 블레이드가 표시됩니다.

## 배치 계정 속성 보기

배치 계정 블레이드는 계정에 대한 여러 속성을 표시하며 액세스 키, 할당량, 사용자 및 저장소 계정 연결 등의 추가 설정에 대한 액세스를 제공합니다.

* **배치 계정 URL** - 이 URL은 [Batch REST][api_rest] API 또는 [Batch .NET][api_net] 클라이언트 라이브러리와 같은 API를 사용할 때 배치 계정에 대한 액세스를 제공하며 다음 형식을 준수합니다.

  `https://<account_name>.<region>.batch.azure.com`

* **액세스 키** - 배치 계정의 액세스 키를 보고 관리하려면 키 아이콘을 클릭하여 **키 관리** 블레이드를 열거나 **모든 설정** > **키**를 클릭합니다. [Batch REST][api_rest] 또는 [Batch .NET][api_net] 클라이언트 라이브러리와 같은 배치 서비스 API와 통신할 때 액세스 키가 필요합니다.

 ![Batch 계정 키][account_keys]

* **모든 설정** - 배치 계정에 대한 모든 설정을 관리하고 해당 속성을 보려면 **모든 설정**을 클릭하여 **설정** 블레이드를 엽니다. 이 블레이드는 계정 할당량 보기, 배치 계정에 연결할 Azure 저장소 계정 선택 및 사용자 관리를 비롯하여 해당 계정의 모든 설정 및 속성에 대한 액세스를 제공합니다.

 ![배치 계정 설정 및 속성 블레이드][5]

## 배치 계정에 대한 고려 사항

* [배치 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md) 및 [배치 관리 .NET](batch-management-dotnet.md) 라이브러리를 사용하여 배치 계정을 만들고 관리할 수도 있습니다.

* 배치 계정 자체에는 요금이 부과되지 않습니다. 배치 솔루션에서 사용하는 Azure 계산 리소스 및 워크로드를 실행할 때 다른 서비스에서 사용하는 리소스에 대해 요금이 부과됩니다. 예를 들어 풀의 계산 노드에 대해 요금이 부과되며 [응용 프로그램 패키지](batch-application-packages.md) 기능을 사용하는 경우 응용 프로그램 패키지 버전을 저장하는 데 사용한 Azure 저장소 리소스에 대해 요금이 부과됩니다. 자세한 내용은 [배치 가격 책정][batch_pricing]을 참조하세요.

* 단일 배치 계정에서 여러 배치 작업을 실행하거나 다른 Azure 지역의 배치 계정 간에 작업을 배포할 수 있습니다.

* 여러 대규모 배치 작업을 실행하고 있는 경우 Azure 구독 및 각 배치 계정에 적용하는 특정 [배치 서비스 할당량 및 제한](batch-quota-limit.md)에 유의해야 합니다. 배치 계정의 현재 할당량이 계정 속성의 포털에 표시됩니다.

* 저장소 계정을 배치 계정에 연결하는 경우 저장소 계정 액세스 키를 다시 생성할 때 주의하세요. 단일 저장소 계정 키만 다시 생성해야 하며 연결된 저장소 계정 블레이드에서 **동기화 키**를 클릭하고 키가 풀에 있는 계산 노드에 전파되도록 5분 기다린 후 필요한 경우 다른 키를 다시 생성하고 동기화합니다. 동시에 두 키를 다시 생성하는 경우 계산 노드에서 키를 동기화할 수 없으며 저장소 계정에 액세스할 수 없게 됩니다.

  ![저장소 계정 키 다시 생성][4]

## 다음 단계

* 배치 서비스 개념 및 기능에 대해 자세히 알아보려면 [Azure 배치 기능 개요](batch-api-basics.md)를 참조하세요. 이 문서에서는 풀, 계산 노드, 작업 및 태스크 등의 기본 배치 리소스에 대해 설명하며 대규모 계산 워크로드 실행을 사용할 수 있는 서비스 기능에 대한 개요를 제공합니다.

* [배치 .NET 클라이언트 라이브러리](batch-dotnet-get-started.md)를 사용하여 배치 가능 응용 프로그램 개발에 대한 기본 사항을 알아봅니다. [소개 자료](batch-dotnet-get-started.md)에서는 배치 서비스를 사용하여 여러 계산 노드에서 워크로드를 실행하는 응용 프로그램 작업 과정을 안내하며 워크로드 파일 스테이징 및 검색을 위해 Azure 저장소를 사용하는 과정을 포함합니다.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "배치 서비스로 Azure 포털에서 블레이드 만들기"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "저장소 계정 키 다시 생성"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "배치 계정 설정 및 속성 블레이드"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0323_2016-->