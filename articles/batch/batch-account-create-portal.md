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
	ms.date="01/28/2016"
	ms.author="marsma"/>

# Azure 포털에서 Azure 배치 계정 만들기 및 관리

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

이 문서에서는 [Azure 포털][azure_portal]을 사용하여 계정 URL 및 계정 키와 같은 Azure 배치 계정을 만들고 관리하는 방법을 보여 줍니다. 모든 배치 API 요청을 인증하려면 배치 계정 URL 및 연결된 선택키가 있어야 합니다. 또한 계산 워크로드에 대한 모든 배치 리소스(예: 풀, 작업(job) 및 작업(task))와 특정 배치 계정을 연결해야 합니다.

>[AZURE.NOTE] 현재 Azure 포털은 배치 계정 관리 및 일부 계정 리소스 보기에 대한 기능을 지원합니다. 전체 배치 기능은 개발자가 배치 API를 통해 사용할 수 있습니다.

## 배치 계정 만들기

1. [Azure 포털][azure_portal]에 로그인합니다.

2. **새로 만들기** > **계산** > **배치 서비스**를 클릭합니다.

	![Marketplace에서 배치][marketplace_portal]

3. 정보를 검토한 후 **만들기**를 클릭합니다.

4. **새 배치 계정** 블레이드에서 다음 정보를 입력합니다.

	a. **계정 이름**에 배치 계정 URL에서 사용할 고유한 이름을 입력합니다.

	>[AZURE.NOTE] 배치 계정 이름은 Azure에서 고유해야 하며, 3자에서 24자 사이의 문자를 포함하고 소문자와 숫자만 사용해야 합니다.

	b. 둘 이상의 구독이 있는 경우 **구독**을 클릭하여 계정이 만들어지는 곳에 사용할 수 있는 구독을 선택합니다.

	c. **리소스 그룹**을 클릭하여 계정에 대해 기존 리소스 그룹을 선택하거나 새로 만듭니다.

	d. **위치**에서 배치를 사용할 수 있는 Azure 지역을 선택합니다.

	![배치 계정 만들기][account_portal]

5. **만들기**를 클릭하여 계정 만들기를 완료합니다.

## 선택키 및 계정 설정 관리
계정을 만든 후에는 선택키, 권한 있는 사용자 및 기타 설정을 관리하는 포털에서 찾을 수 있습니다.

배치 계정 URL이 **필수**에 표시됩니다. `https://<account_name>.<region>.batch.azure.com` 형식의 URL입니다.

선택키를 보고 관리하려면 키 아이콘을 클릭합니다.

![Batch 계정 키][account_keys]

## 배치 계정에 대해 알아야 할 추가 사항

* 배치 계정을 만들고 관리하기 위한 다른 방법에는 [배치 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md) 및 [배치 관리 .NET 라이브러리](batch-management-dotnet.md)를 사용하는 것입니다.

* Azure는 배치 계정 사용에 대해 요금을 청구하지 않습니다. 작업을 실행할 때 Azure 계산 리소스 및 다른 서비스 사용에 대해 요금을 청구합니다([배치 가격 책정][batch_pricing] 참조).

* 단일 배치 계정에서 여러 배치 작업을 실행하거나 다른 Azure 지역의 배치 계정 간에 작업을 배포할 수 있습니다.

* 여러 대규모 배치 작업을 실행하고 있는 경우 Azure 구독 및 각 배치 계정에 적용하는 특정 [배치 서비스 할당량 및 제한](batch-quota-limit.md)에 유의해야 합니다. 배치 계정의 현재 할당량이 계정 속성의 포털에 표시됩니다.

## 다음 단계

* 배치 개념에 대한 자세한 사항은 [Azure 배치 기능 개요](batch-api-basics.md)를 참조하세요.

* [배치 .NET 클라이언트 라이브러리](batch-dotnet-get-started.md)를 사용하여 첫 번째 응용 프로그램 개발을 시작하겠습니다.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0218_2016-->