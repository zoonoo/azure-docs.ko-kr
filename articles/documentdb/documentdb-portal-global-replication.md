<properties
	pageTitle="DocumentDB 글로벌 데이터베이스 복제 | Microsoft Azure"
	description="Azure 포털을 통해 DocumentDB 계정의 글로벌 복제를 관리하는 방법을 알아봅니다."
	services="documentdb"
    keywords="글로벌 데이터베이스, 복제"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="mimig"/>

# Azure 포털을 사용하여 DocumentDB 글로벌 데이터베이스 복제를 수행하는 방법

Azure DocumentDB에서 Azure 포털을 사용하여 데이터의 글로벌 가용성을 위해 여러 지역의 데이터를 복제하는 방법을 알아봅니다.

글로벌 데이터베이스 복제가 DocumentDB에서 작동하는 방법에 대한 자세한 내용은 [DocumentDB를 사용하여 전역적으로 데이터 배포](documentdb-distribute-data-globally.md)를 참조하세요. 프로그램 방식으로 글로벌 데이터베이스 복제 수행에 대한 자세한 내용은 [다중 하위 지역 DocumentDB 계정을 사용하여 개발](documentdb-developing-with-multiple-regions.md)을 참조하세요.

> [AZURE.NOTE] DocumentDB 데이터베이스의 전역 배포는 일반적으로 사용 가능하며, 새로 만든 DocumentDB 계정에 대해 자동으로 사용되도록 설정됩니다. 현재, 모든 기존 계정에 대해 전역 배포를 사용하도록 설정하기 위해 작업 중이지만 계정에 대해 전역 배포를 일시적으로 사용하도록 설정하려면 [지원 서비스에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. Microsoft에서 처리해 드리겠습니다.

## <a id="addregion"></a>글로벌 데이터베이스 지역 추가

DocumentDB는 대부분 [Azure 지역][azureregions]에서 사용할 수 있습니다. 데이터베이스 계정에서 기본 일관성 수준을 선택한 후에는 (선택한 기본 일관성 수준 및 글로벌 배포 수요에 따라) 하나 이상의 지역을 연결합니다.

1. [Azure 포털](https://portal.azure.com/)의 이동 표시줄에서 **DocumentDB 계정**을 클릭합니다.
2. **DocumentDB 계정** 블레이드에서 수정할 데이터베이스 계정을 선택합니다.
3. 계정 블레이드의 메뉴에서 **지역 추가/제거**를 클릭합니다.
4. **지역 추가/제거** 블레이드에서 추가/제거할 지역을 선택하고 **확인**을 클릭합니다. 지역을 추가하는 비용에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/) 또는 [DocumentDB를 사용하여 전역적으로 데이터 배포](documentdb-distribute-data-globally.md) 문서를 참조하세요.

    ![지도에서 지역을 클릭하여 추가/삭제][1]

### 글로벌 데이터베이스 지역 선택

두 개 이상의 지역을 구성할 때는 [비즈니스 연속성 및 재해 복구(BCDR): Azure 쌍을 이루는 지역][bcdr] 문서에서 설명된 하위 지역 쌍에 기초하여 지역을 선택하는 것이 좋습니다.

특히, 여러 지역을 구성할 때는 각 쌍을 이루는 지역 열에서 같은 수의 지역(짝수/홀수 +/-1)을 선택해야 합니다. 예를 들어 미국 지역 네 곳에 배포하고자 할 경우 왼쪽 열에서 미국 지역 두 곳을 선택하고 오른쪽 열에서 미국 지역 두 곳을 선택합니다. 그러므로 미국 서부, 미국 동부, 미국 중북부, 미국 중남부가 적절한 세트가 될 것입니다.

재해 복구 시나리오에서 두 지역만 구성할 때는 이 지침을 따르는 것이 중요합니다. 둘 이상의 지역을 선택할 경우, 지침을 따르면 좋지만 선택한 지역 중 일부만 이 짝 지침을 따르면 크게 중요하지 않습니다.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>다음 단계

[DocumentDB의 일관성 수준](documentdb-consistency-levels.md)을 참조하여 전역적으로 복제한 계정의 일관성을 관리하는 방법을 알아봅니다.

글로벌 데이터베이스 복제가 DocumentDB에서 작동하는 방법에 대한 자세한 내용은 [DocumentDB를 사용하여 전역적으로 데이터 배포](documentdb-distribute-data-globally.md)를 참조하세요. 프로그램 방식으로 여러 하위 지역의 데이터 복제에 대한 자세한 내용은 [다중 지역 DocumentDB 계정을 사용하여 개발](documentdb-developing-with-multiple-regions.md)을 참조하세요.

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/ko-KR/regions/#services
[offers]: https://azure.microsoft.com/ko-KR/pricing/details/documentdb/

<!---HONumber=AcomDC_0831_2016-->