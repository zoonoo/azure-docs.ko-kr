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
ms.date: 09/30/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: aed47a7e9aa06e48199a71f67ae6d82bcfe27c9a


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Azure 마켓플레이스에 제품을 게시하고 관리하는 방법
이 문서는 다른 Azure 고객 및 파트너가 구매하여 활용하도록 개발자가 Azure Marketplace에 나열된 솔루션을 만들고, 배포하고, 관리하는 데 도움을 주기 위해 제공됩니다.

## <a name="what-is-the-azure-marketplace"></a>Azure 마켓플레이스란 무엇인가요?
Azure 마켓플레이스에서 Azure 구독자는 온-프레미스 또는 클라우드 기반 솔루션 및 응용 프로그램의 개발을 용이하게 하는 서비스를 찾을 수 있습니다. 그리고 구성 요소로 [Azure 인증](http://azure.com/certified) 서비스를 사용하여 LOB(기간 업무) 및 다른 Azure 구독자에 대한 혁신적인 응용 프로그램 또는 서비스를 빠르게 개발합니다.

Azure 게시자로 Azure 마켓플레이스를 통해 해당 클라우드 기반 응용 프로그램 및 모바일 솔루션을 신속하게 개발하려는 다른 개발자, ISV 및 IT 전문가에게 혁신적인 솔루션 또는 서비스를 배포 및 판매할 수 있습니다.

## <a name="supported-types-of-offers"></a>지원되는 제품의 유형
게시자로서 처음 수행하려고 할 작업은 자신의 회사에서 제공할 솔루션의 종류를 정의하는 것입니다. Azure 마켓플레이스에서는 다음과 같은 세 가지 유형의 제품을 지원합니다.

* **가상 컴퓨터 이미지** 는 완전히 설치된 운영 체제 및 하나 이상의 응용 프로그램이 포함된 미리 구성된 이미지입니다. 가상 컴퓨터 이미지는 Azure 가상 컴퓨터 서비스에서 가상 컴퓨터를 만들고 배포하는 데 필요한 정보를 제공합니다.

  > [!NOTE]
  > **예를 들어** Azure 게시자로 다른 Azure 구독자가 이 VM을 자신의 클라우드 서비스 환경으로 기꺼이 확보 및 배포할 수 있도록 충분히 강력한 혁신적인 데이터베이스 서비스를 사용하여 VM을 만들고 유효성을 검사했습니다.
  >
  >
* **개발자 서비스** 는 응용 프로그램 개발 또는 시스템 관리에 사용하는 완전히 관리된 서비스입니다. Azure에서 클라우드 규모 응용 프로그램을 빠르게 개발할 수 있도록 하는 기능을 제공합니다.

  > [!NOTE]
  > **예를 들어** Azure 게시자로 기록 데이터를 기반으로 예측을 제공하는 API 액세스 가능 서비스(Azure 또는 다른 곳에서 호스트됨)를 개발했습니다. 그리고 솔루션을 빌드하는 다른 Azure 구독자가 사용할 수 있는 서비스입니다. 다른 구독자가 해당 서비스에서 찾고 확보 및 사용하도록 이 서비스를 Azure 마켓플레이스에 배포할 수 있습니다.
  >
  >
* **솔루션 템플릿** 은 Azure 구독자가 한 가지 일관된 방식으로 제품을 하나 이상 배포하도록 지원하기 위해 다른 판매자가 게시한 서비스를 포함하여 다양한 Azure 서비스를 참조할 수 있는 데이터 구조입니다.

  > [!NOTE]
  > **예를 들어** Azure 게시자로 몇 번의 클릭으로 안전한 고가용성 클라우드 서비스를 빠르게 배포할 수 있도록 하는 Azure의 서비스 집합을 포함했습니다. 다른 Azure 구독자는 동일하거나 유사한 Azure 서비스를 수동으로 식별 및 구성하는 대신 이 솔루션 템플릿을 확보하여 시간을 절약하는 데서 가치를 찾을 수 있습니다.
  >
  >

일부 단계는 다양한 유형의 솔루션 간에 공유됩니다. 여기에서는 모든 유형의 솔루션에 대해 완료해야 하는 단계에 대한 간략한 개요를 제공합니다.

## <a name="1-pre-requisites"></a>1. 필수 구성 요소
> [!NOTE]
> Azure 마켓플레이스에서 작업을 시작하기 전에 [사전 승인](http://azure.com/certified)을 받아야 합니다.
>
>

1. [Microsoft Azure 인증 사전 승인 신청](marketplace-publishing-azure-certification.md)
2. [Microsoft 개발자 계정 만들기 및 등록](marketplace-publishing-accounts-creation-registration.md)
3. [비기술 필수 구성 요소 충족](marketplace-publishing-pre-requisites.md)

## <a name="2-publishing-your-offer"></a>2. 제품 게시
### <a name="21-complete-offer-specific-technical-pre-requisites"></a>2.1 제품별 특정 기술 필수 구성 요소 완료
* [VM 기술 필수 구성 요소](marketplace-publishing-vm-image-creation-prerequisites.md)
* [솔루션 템플릿 기술 필수 구성 요소](marketplace-publishing-solution-template-creation-prerequisites.md)

### <a name="22-create-your-offer"></a>2.2 제품 만들기
1. 아래 제품별 지침을 사용하여 제품을 만듭니다.
   * [VM 제품 만들기](marketplace-publishing-vm-image-creation.md)
   * [솔루션 템플릿 제품 만들기](marketplace-publishing-solution-template-creation.md)
2. [제품 마케팅 콘텐츠 만들기](marketplace-publishing-push-to-staging.md)

### <a name="23-test-your-offer-in-staging"></a>2.3 준비 단계에서 제품 테스트
* [스테이징에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)
* [준비 단계에서 솔루션 템플릿 제품 테스트](marketplace-publishing-solution-template-test-in-staging.md)

### <a name="24-deploy-your-offer-to-the-marketplace"></a>2.4 마켓플레이스로 제품 배포
* [Azure 마켓플레이스에 제품 배포](marketplace-publishing-push-to-production.md)
* [마켓플레이스에서 일반적인 게시 문제 해결](marketplace-publishing-support-common-issues.md)
* 사용한 포털에 대한 자세한 내용은 [필요한 포털](marketplace-publishing-portals.md)을 참조하세요.

### <a name="virtual-machine-image-specific"></a>가상 컴퓨터 이미지별
* [온-프레미스로 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)
* [Azure Portal에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Portal에서 Linux를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [VHD를 만드는 동안 발생하는 일반적인 문제 해결](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="3-post-publishing-management-of-your-offer"></a>3. 게시 후 제품 관리
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



<!--HONumber=Nov16_HO3-->


