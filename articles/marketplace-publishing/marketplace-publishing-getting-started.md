---
title: "제품을 만들고 Marketplace에 배포하는 방법 개요 | Microsoft Docs"
description: "승인된 Microsoft 개발자가 되고, 가상 컴퓨터 이미지, 템플릿, 데이터 서비스 또는 개발자 서비스를 만들어서 Azure 마켓플레이스에 배포하는 데 필요한 단계를 이해합니다."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: b76185c0a4a0e17b663affee9a02b65f222fedeb
ms.openlocfilehash: d679096476406831c1fda4f695adff84e63d6ae8


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Azure 마켓플레이스에 제품을 게시하고 관리하는 방법
이 문서는 다른 Azure 고객 및 파트너가 구매하여 활용하도록 개발자가 Azure Marketplace에 나열된 솔루션을 만들고, 배포하고, 관리하는 데 도움을 주기 위해 제공됩니다.

## <a name="what-is-the-azure-marketplace"></a>Azure 마켓플레이스란 무엇인가요?
Azure 게시자로 Azure 마켓플레이스를 통해 해당 클라우드 기반 응용 프로그램 및 모바일 솔루션을 신속하게 개발하려는 다른 개발자, ISV 및 IT 전문가에게 혁신적인 솔루션 또는 서비스를 배포 및 판매할 수 있습니다. 솔루션이 비즈니스 사용자를 대상으로 하는 경우에는 [AppSource](http://appsource.microsoft.com) 마켓플레이스를 고려할 수 있습니다.


## <a name="supported-types-of-solutions"></a>지원되는 솔루션 형식
게시자로서 처음 수행하려고 할 작업은 자신의 회사에서 제공할 솔루션의 종류를 정의하는 것입니다. Azure Marketplace에서는 다음과 같은 유형의 제품을 지원합니다.

|솔루션 유형|가상 컴퓨터|솔루션 템플릿|
|---|---|---|
|정의|완전히 설치된 운영 체제 및 하나 이상의 응용 프로그램이 포함된 미리 구성된 이미지입니다. 가상 컴퓨터 이미지는 Azure 가상 컴퓨터 서비스에서 가상 컴퓨터를 만들고 배포하는 데 필요한 정보를 제공합니다.|Azure 구독자가 한 가지 일관된 방식으로 제품을 하나 이상 배포하도록 지원하기 위해 다른 판매자가 게시한 서비스를 포함하여 다양한 Azure 서비스를 참조할 수 있는 데이터 구조입니다.|
|예|**예를 들어** Azure 게시자로 다른 Azure 구독자가 이 VM을 자신의 클라우드 서비스 환경으로 기꺼이 확보 및 배포할 수 있도록 충분히 강력한 혁신적인 데이터베이스 서비스를 사용하여 VM을 만들고 유효성을 검사했습니다.|**예를 들어** Azure 게시자로 부하 분산, 강화된 보안 및 고가용성 기능을 갖춘 Cloud Services를 빠르게 배포할 수 있도록 하는 Azure의 서비스 집합을 포함했습니다. 다른 Azure 구독자는 동일하거나 유사한 Azure 서비스를 수동으로 찾거나 조달, 구축 및 구성하는 대신 목표에 맞는 솔루션 템플릿을 조달하여 시간을 절약할 수 있습니다.|

> [!NOTE]
> 일부 단계는 여러 형식의 솔루션에서 공유되고 다른 단계들은 각 솔루션 형식에 고유합니다. 여기에서는 모든 유형의 솔루션에 대해 완료해야 하는 단계에 대한 간략한 개요를 제공합니다.

## <a name="how-to-publish-a-solution"></a>솔루션을 게시하는 방법
![그리기](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1. 사전 승인을 위한 솔루션 추천
- [여기](https://createopportunity.azurewebsites.net)에서 **Microsoft Azure Certified for Virtual Machines**에 대한 솔루션 추천 양식 작성

>[!NOTE]
> 파트너 계정 관리자나 DX 파트너 관리자와 함께 작업하는 경우 Azure Certified 프로그램의 솔루션을 추천해달라고 요청하거나, [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) 웹 페이지로 이동하여 응용 프로그램 양식을 작성하고 Microsoft 스폰서 연락처 필드에 파트너 계정 관리자나 DX 파트너 관리자의 메일을 입력합니다.

[Azure Marketplace 참여 정책](http://go.microsoft.com/fwlink/?LinkID=526833)에 따라 자격 조건을 충족하고 신청이 승인되면 Azure Marketplace에 솔루션을 등록하기 위한 작업을 사용자와 함께 시작합니다.

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2. Microsoft 판매자로 계정 등록
- [Microsoft 개발자 계정으로 Microsoft 계정 등록](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3. 솔루션 게시
1. 비기술 요구 사항 충족
  - [비기술 필수 구성 요소 충족](marketplace-publishing-pre-requisites.md)
  - [VM 기술 필수 구성 요소](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [솔루션 템플릿 기술 필수 구성 요소](marketplace-publishing-solution-template-creation-prerequisites.md)
2. 제품 만들기
  - [가상 컴퓨터](marketplace-publishing-vm-image-creation.md)
  - [솔루션 템플릿](marketplace-publishing-solution-template-creation.md)
3. [제품 마케팅 콘텐츠 만들기](marketplace-publishing-push-to-staging.md)
4. 준비 단계에서 제품 테스트
  - [스테이징에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)
  - [준비 단계에서 솔루션 템플릿 제품 테스트](marketplace-publishing-solution-template-test-in-staging.md)
5. [Azure 마켓플레이스에 제품 배포](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>가상 컴퓨터 이미지별
* [온-프레미스로 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)
* [Azure Portal에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Portal에서 Linux를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [VHD를 만드는 동안 발생하는 일반적인 문제 해결](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>솔루션을 관리하는 방법
* [가상 컴퓨터에 대한 프로덕션 이후 가이드](marketplace-publishing-vm-image-post-publishing.md)
* [제품 또는 SKU의 비기술적인 세부 정보를 업데이트하는 방법](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [제품 또는 SKU의 기술적인 세부 정보를 업데이트하는 방법](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [나열된 제품에 새 SKU를 추가하는 방법](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [나열된 SKU에 대한 데이터 디스크 수를 변경하는 방법](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Azure 마켓플레이스에서 나열된 제품을 삭제하는 방법](marketplace-publishing-vm-image-post-publishing.md)
* [Azure 마켓플레이스에서 나열된 SKU를 삭제하는 방법](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Azure 마켓플레이스에서 나열된 현재 버전의 SKU를 삭제하는 방법](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [목록 가격을 프로덕션 값으로 되돌리는 방법](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [청구 모델을 프로덕션 값으로 되돌리는 방법](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [나열된 SKU의 표시 유형 설정을 프로덕션 값으로 복귀하는 방법](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [클라우드 솔루션 공급자 대리점 인센티브를 변경하는 방법](marketplace-publishing-csp-incentive.md)
* [판매자 통찰력 보고서 이해](marketplace-publishing-report-seller-insights.md)
* [지급 보고 이해](marketplace-publishing-report-payout.md)
* [게시자로 지원 받기](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>추가 리소스
* [Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)



<!--HONumber=Feb17_HO3-->


