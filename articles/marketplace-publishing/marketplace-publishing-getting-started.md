<properties
   pageTitle="제품을 만들고 마켓플레이스에 배포하는 방법 개요 | Microsoft Azure"
   description="승인된 Microsoft 개발자가 되고, 가상 컴퓨터 이미지, 템플릿, 데이터 서비스 또는 개발자 서비스를 만들어서 Azure 마켓플레이스에 배포하는 데 필요한 단계를 이해합니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
   ms.author="hascipio" />

# Azure 마켓플레이스에 제품을 게시하고 관리하는 방법
이 문서는 다른 Azure 고객 및 파트너가 구매하여 활용하도록 개발자가 Azure 마켓플레이스에 나열된 솔루션을 만들고, 배포하고, 관리하는 데 도움을 주기 위해 제공됩니다.

게시자로서 처음 수행하려고 할 작업은 자신의 회사에서 제공할 솔루션의 종류를 정의하는 것입니다. Azure 마켓플레이스는 여러 가지 솔루션을 지원하며, 각 솔루션을 마켓플레이스에 성공적으로 게시하는 데 필요한 일련의 작업은 서로 조금씩 다릅니다.

## 제품의 유형
|제품 유형| 정의 |
|---|---|
|가상 컴퓨터 이미지 | 완전히 설치된 운영 체제 및 하나 이상의 응용 프로그램이 포함된 미리 구성된 VM(가상 컴퓨터) 이미지입니다. 가상 컴퓨터 이미지 제공 서비스에는 단일 VM 이미지 또는 솔루션 템플릿으로 함께 결합된 여러 VM 이미지가 포함될 수 있습니다. 가상 컴퓨터 이미지("이미지")는 Azure 가상 컴퓨터 서비스에서 가상 컴퓨터를 만들고 배포하는 데 필요한 정보를 제공합니다. 하나의 이미지는 운영 체제 가상 하드 드라이브 하나와 0개 이상의 데이터 디스크 가상 하드 드라이브로 구성됩니다. 고객은 단일 이미지에서 원하는 수의 가상 컴퓨터를 배포할 수 있습니다.|
|개발자 서비스| 정보 근로자, 비즈니스 분석가, 개발자 또는 IT 전문가가 사용자 지정 응용 프로그램 개발 또는 시스템 관리에 사용하는 완전히 관리되는 서비스입니다. 개발자 서비스는 고객이 Azure에서 클라우드 규모 응용 프로그램을 빠르게 개발할 수 있는 기능을 제공합니다. 고객이 개발자 서비스를 구입하려면 Azure 구독이 있어야 합니다. 판매자는 Microsoft Azure 마켓플레이스 게시자 계약에 명시된 대로 개발자 서비스에 대한 고객 사용을 계량하고 사용 정보를 Microsoft에 보고해야 합니다.|
|솔루션 템플릿|“ARM(Azure Resource Manager)” 솔루션 템플릿은 Azure 고객이 한 가지 일관된 방식으로 제공 서비스를 하나 이상 배포하도록 지원하기 위해 다른 판매자가 게시한 제공 서비스를 비롯해 다양한 제공 서비스를 참조할 수 있는 데이터 구조입니다.|

일부 단계는 다양한 유형의 솔루션 간에 공유됩니다. 여기에서는 모든 유형의 솔루션에 대해 완료해야 하는 단계에 대한 간략한 개요를 제공합니다.

## 1\. 필수 구성 요소

> [AZURE.NOTE] Azure 마켓플레이스에서 작업을 시작하기 전에 사전 승인을 받아야 합니다. 데이터 서비스 게시자에는 해당하지 않습니다.

1. [Microsoft Azure 인증 사전 승인 신청](marketplace-publishing-azure-certification.md)
2. [Microsoft 개발자 계정 만들기 및 등록](marketplace-publishing-accounts-creation-registration.md)
3. [비기술 필수 구성 요소 충족](marketplace-publishing-pre-requisites.md)

## 2\. 제품 게시
### 2\.1 제품별 특정 기술 필수 구성 요소 완료
- [VM 기술 필수 구성 요소](marketplace-publishing-vm-image-creation-prerequisites.md)
- [솔루션 템플릿 기술 필수 구성 요소](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 제품 만들기
1. 아래 제품별 지침을 사용하여 제품을 만듭니다.
    - [VM 제품 만들기](marketplace-publishing-vm-image-creation.md)
    - [솔루션 템플릿 제품 만들기](marketplace-publishing-solution-template-creation.md)
2. [제품 마케팅 콘텐츠 만들기](marketplace-publishing-push-to-staging.md)

### 2\.3 준비 단계에서 제품 테스트
- [스테이징에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)
- [준비 단계에서 솔루션 템플릿 제품 테스트](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 마켓플레이스로 제품 배포
- [Azure 마켓플레이스에 제품 배포](marketplace-publishing-push-to-production.md)

### 가상 컴퓨터 이미지별 ###
- [온-프레미스로 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)
- [Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [마켓플레이스에서 일반적인 게시 문제 해결](marketplace-publishing-support-common-issues.md)
- 사용한 포털에 대한 자세한 내용은 [필요한 포털](marketplace-publishing-portals.md)을 참조하세요.


## 3\. 게시 후 제품 관리
- [가상 컴퓨터에 대한 프로덕션 이후 가이드](marketplace-publishing-vm-image-post-publishing.md)
- [제품 또는 SKU의 비기술적인 세부 정보를 업데이트하는 방법](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [Azure 마켓플레이스에서 제품 또는 SKU를 삭제하는 방법](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [클라우드 솔루션 공급자 대리점 인센티브를 변경하는 방법](marketplace-publishing-csp-incentive.md)
- [판매자 통찰력 보고서 이해](marketplace-publishing-report-seller-insights.md)
- [지급 보고 이해](marketplace-publishing-report-payout.md)
- [게시자로 지원 받기](marketplace-publishing-get-publisher-support.md)

## 추가 리소스
- [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->