<properties
	pageTitle="Azure 앱 서비스를 사용하는 ClearDB MySQL 데이터베이스에 대한 FAQ | Microsoft Azure"
	description="Azure 앱 서비스를 사용하는 ClearDB MySQL 데이터베이스를 사용하는 방법에 대한 일반적인 질문에 대한 답변입니다."
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# Azure 앱 서비스를 사용하는 ClearDB MySQLl 데이터베이스에 대한 FAQ

이 FAQ는 Azure 웹앱용 ClearDB MySQL 데이터베이스의 사용 및 구매에 대한 일반적인 질문에 답변합니다.

## Azure에서 MySQL을 사용하려면 어떤 옵션이 필요한가요?

여러 옵션이 있습니다.

* [ClearDB 공유 MySQL 데이터베이스](/marketplace/partners/cleardb/databases/)

* [ClearDB MySQL 프리미엄 클러스터](/marketplace/partners/cleardb-clusters/cluster/)

* [Azure VM에서 실행되는 MySQL 클러스터](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Azure VM에서 실행되는 MySQL의 단일 인스턴스](virtual-machines/virtual-machines-mysql-windows-server-2008r2.md)

ClearDB는 MySQL 호스팅 서비스이며 MySQL 인프라를 관리합니다. Azure 가상 컴퓨터에서 사용자 고유 MySQL 클러스터나 데이터베이스를 실행하는 경우 MySQL 서버를 설정하고 패치를 통해 지속적으로 업데이트해야 합니다.

## Azure 마켓플레이스의 웹앱 + MySQL 템플릿에 신용 카드가 필요한가요?

사용하고 있는 구독 유형에 따라 다릅니다. 다음은 일반적으로 사용되는 구독 유형입니다.

* [종량제](/offers/ms-azr-0003p/): 신용 카드가 필요하며 유료 MySQL 데이터베이스를 구매하는 경우 신용 카드로 요금이 청구됩니다.

* [무료 평가판](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure 서비스에서 사용할 크레딧이 포함되어 있지만 타사 리소스의 구매에는 사용할 수 없습니다. 타사 서비스 또는 유료 MySQL 데이터베이스를 구매하려면 신용 카드가 설정된 구독을 사용해야 합니다. 웹앱의 경우 무료 ClearDB MySQL 데이터베이스를 만들 수 있습니다.

* [MSDN 구독](/pricing/member-offers/msdn-benefits/) 및 **MSDN 개발 테스트 종량제**: 무료 평가판과 유사한 MSDN 구독을 사용하려면 신용 카드로 ClearDB의 유료 MySQL 솔루션을 구매해야 합니다.

* [EA(기업계약)](/pricing/enterprise-agreement/): EA 고객은 Azure 마켓플레이스(타사)에서 구매한 모든 제품에 대해 EA 분기별로 별도의 통합 청구서로 청구됩니다. 모든 마켓플레이스에서 구매한 제품은 현금 약정 금액과 별도로 청구됩니다. 현재 아제르바이잔, 크로아티아, 노르웨이 및 푸에르토리코에서 등록한 고객은 Azure 저장소를 사용할 수 없습니다.

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): 웹앱용 무료 ClearDB 데이터베이스만 만들 수 있습니다. 만들 수 있는 무료 ClearDB MySQL 데이터베이스의 수에는 제한이 없습니다. 이 서비스는 평가판 용도로만 제공되므로 무료 데이터베이스를 프로덕션 웹앱에서 사용해서는 안 됩니다.

## 왜 Azure 마켓플레이스에서 웹앱+ MySQL에 대해 3.50달러가 청구되었나요?

기본 데이터베이스 옵션은 3.50달러인 Titan입니다. 데이터베이스를 만드는 동안 비용이 표시되지 않으므로 의도하지 않은 데이터베이스를 실수로 구매할 수도 있습니다. 환경을 개선할 방법을 찾으려고 노력하고 있습니다. 그 전까지는 **만들기**를 클릭하고 리소스를 배포하기 전에 웹앱 및 데이터베이스에 대해 선택한 가격 책정 계층을 모두 확인해야 합니다.

## Azure 가상 컴퓨터에서 MySQL을 실행하고 있습니다. Azure 웹앱을 내 데이터베이스에 연결할 수 있나요?

예. Azure VM에서 웹앱으로 원격 액세스할 수 있으면 웹앱을 데이터베이스에 연결할 수 있습니다. 자세한 내용을 보려면 여기를 클릭하세요.

## ClearDB 프리미엄 MySQL 클러스터가 지원되는 국가는 어디인가요?

[ClearDB 프리미엄 MySQL 클러스터](/marketplace/partners/cleardb-clusters/cluster/)는 인도, 오스트레일리아, 브라질 남부 및 중국을 제외한 전 세계 모든 Azure 지역에서 사용할 수 있습니다.

## ClearDB 프리미엄 클러스터 솔루션을 사용하여 데이터베이스를 만들기 전에 새 클러스터를 만들 수 있나요?

아니요, 빈 ClearDB 클러스터 만들기는 지원되지 않습니다. Azure 포털을 사용하면 클러스터에서 데이터베이스를 만들 수 있으며 동시에 새 클러스터를 만들 수 있습니다.

## 내 응용 프로그램 중 하나에서 사용 중인 ClearDB MySQL 데이터베이스를 삭제하려고 하면 경고가 표시되나요?

아니요, 응용 프로그램이 사용하고 있는 마켓플레이스 구매 제품을 삭제할 경우 Azure는 경고를 표시하지 않습니다.

## 어떤 지역에서 ClearDB 데이터베이스를 만들 수 있나요?

아제르바이잔, 크로아티아, 노르웨이 또는 푸에르토리코에서 등록한 고객은 Azure 마켓플레이스를 사용할 수 없습니다. 이러한 지역에서는 ClearDB를 사용할 수 없습니다.

## 프로덕션 웹앱 및 데이터베이스에 대해 어떤 가격 책정 계층을 선택해야 하나요?

웹앱의 경우 기본이나 더 높은 가격 책정 계층을 사용합니다. ClearDB의 경우 Saturn 또는 Jupiter 플랜을 사용하는 것이 좋습니다. [웹앱](/pricing/details/app-service/) 및 [ClearDB MySQL 데이터베이스](/marketplace/partners/cleardb/databases/)에 대한 각 가격 책정 계층의 기능 및 제한 사항을 검토하여 요구에 맞는 계층을 선택합니다.

## 다른 플랜으로 ClearDB 데이터베이스를 업그레이드하려면 어떻게 하나요?

[ClearDB 업그레이드 마법사](https://www.cleardb.com/store/azure/upgrade)를 사용할 수 있습니다. 현재 Azure 포털에는 업그레이드 경로가 없습니다.

## 데이터베이스가 다운되었을 때 지원을 받으려면 누구에게 문의해야 하나요?

데이터베이스 관련 문제에 대해서는 [ClearDB 지원](https://www.cleardb.com/developers/help/support)에 문의하세요. Azure 구독 정보를 제공해야 하므로 준비합니다.

## EA(기업계약) 구독으로 확장성 있는 WordPress를 구매할 수 있나요?

프로세스는 모든 구독에서 동일합니다. [Azure 포털](https://portal.azure.com/)에서 Azure 마켓플레이스로 이동하고 [확장성 있는 WordPress](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress)를 선택하여 앱을 만들기 시작합니다. 확장성 있는 WordPress는 ClearDB Saturn 및 Jupiter 가격 책정 계층만 지원하며 EA 크레딧은 표준 웹앱 가격 책정 계층에서 실행 중인 웹 응용 프로그램 및 유료 ClearDB(공유) MySQL 데이터베이스로 이동합니다.[/marketplace/faq/](/marketplace/faq/) 스토어에서 구매한 모든 제품에 대해 EA 분기별로 별도의 통합 청구서로 청구됩니다.

## 신용 카드 구독에서 EA 구독으로 ClearDB 데이터베이스를 전송할 수 있나요?

기존 ClearDB 데이터베이스에서 기존 구독과 연결된 신용 카드를 사용합니다. EA 구독을 사용하려면 데이터를 새 데이터베이스로 마이그레이션해야 합니다.

* EA 구독으로 새 ClearDB 데이터베이스를 구매합니다.
* 새 데이터베이스로 데이터를 마이그레이션합니다.
* 새 데이터베이스를 사용할 응용 프로그램을 업데이트합니다.
* 이전 ClearDB 데이터베이스를 삭제합니다.

MySQL(ClearDB)을 사용하여 새 웹앱을 만들거나 MySQL 데이터베이스(ClearDB)를 만드는 경우 선택한 구독이 서비스에 대한 지불 방법을 결정합니다. EA 구독을 사용하면 Azure 포털의 ClearDB와 같은 타사 서비스의 조달이 차단되지 않습니다. EA 구독은 현금 약정 금액과 별도로 청구되며 분기별로 체납 청구됩니다. EA 고객은 모든 타사 마켓플레이스 서비스에 대해 지불하는 데 사용할 결제 방법(예: 신용 카드)을 설정해야 합니다.

## EA 구독의 ClearDB 리소스에 대한 요금은 어디에서 볼 수 있나요?

Direct EA 고객의 경우 Azure 마켓플레이스 요금은 엔터프라이즈 포털에 표시됩니다. 마켓플레이스에서 구매한 모든 제품 및 사용량 과금은 현금 약정 금액과 별도로 청구되며 분기별로 체납 청구됩니다. EA 고객은 타사 서비스 공급자에게 직접 요금을 지불해야 하며 EA 계정으로 신용 카드 등의 결제 방법을 사용하여 지불할 수 있습니다.

Indirect EA 고객은 엔터프라이즈 포털의 **구독 관리** 페이지에서 Azure 마켓플레이스 구독을 찾을 수 있지만 가격은 표시되지 않습니다. 고객은 마켓플레이스 요금에 대한 내용을 LSP에 문의해야 합니다.

EA Azure 등록 관리자가 관리할 수 있는 타사 서비스용 Azure 마켓플레이스에 액세스합니다. 엔터프라이즈 포털의 계정 및 구독 관리에 있는 계정 섹션에서 스토어에서 구매한 타사 제품에 대한 액세스를 사용하지 않도록 설정하거나 다시 사용하도록 설정할 수 있습니다.

## EA 구독의 ClearDB 서비스의 요금에 대한 질문이 있는 경우 누구에게 문의해야 하나요?

EA 등록에서 요금 청구와 관련된 사항은 [엔터프라이즈 고객 지원](http://aka.ms/AzureEntSupport)에 문의하세요. EA 포털 지원 팀이 질문에 대답하거나 문제를 해결하는데 도움을 줄 것입니다.

## 자세한 정보

[Azure 마켓플레이스 FAQ](/marketplace/faq/)

<!---HONumber=AcomDC_0211_2016-->