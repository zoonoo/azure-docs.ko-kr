---
title: 가상 머신 제안 작성 | Microsoft Docs
description: Azure Marketplace에 가상 머신을 게시합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d77dad52e75253de02fd079d791861356c4c5e1f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247078"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Azure Marketplace에 가상 머신 게시

이 문서에서는 가상 머신 제안을 Azure Marketplace에 게시하는 데 필요한 단계를 제공합니다.

## <a name="prerequisites"></a>필수 조건

Azure Marketplace에 가상 머신을 게시하는 데 적용되는 기술 및 비기술 필수 조건은 다음과 같습니다

### <a name="technical"></a>기술

-   [Azure Marketplace용 가상 머신 이미지를 만들기 위한 기술 필수 조건](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Linux VHD 만들기 및 업로드](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [이미지에서 Linux VM 만들기 및 시험](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Windows VHD 만들기 및 업로드 ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [이미지에서 Windows VM 만들기 및 시험](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [VHD를 만드는 동안 발생하는 일반적인 문제 해결 방법](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Azure Marketplace의 보안 권장 사항 이미지](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>비기술(비즈니스 요구 사항)

 -   회사(또는 해당 자회사)는 Azure Marketplace에서 지원되는 판매 국가에 있습니다.

-   제품은 Azure Marketplace에서 지원되는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.

-   상업적이고 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다. 이러한 지원은 체험, 유료 또는 커뮤니티 지원을 통해 제공될 수 있습니다.

-   소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.

-   Azure Marketplace 및 Azure 관리 포털에 등재할 제안에 대한 기준에 맞는 콘텐츠를 제공합니다.

-   Azure Marketplace 참가 정책 및 게시자 계약의 조건에 동의합니다.

-   [사용 약관](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft 개인정보처리방침](https://www.microsoft.com/privacystatement/default.aspx) 및 [Microsoft Azure Certified 프로그램 계약](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)을 준수한다는 데 동의합니다.

## <a name="before-you-begin"></a>시작하기 전에

모든 필수 조건이 충족되면 솔루션 템플릿 제안 작성을 시작할 수 있습니다. 시작하기 전에 검토해야 하는 제안 및 SKU 정보는 다음과 같습니다.

**제안**

Azure 애플리케이션 제안은 게시자가 제공하는 제품의 유형에 해당합니다. Azure Marketplace에서 사용할 수 있는 새 유형의 솔루션/응용 프로그램이 있으면 새 제안이 가장 좋은 방법입니다. 제품은 SKU의 컬렉션입니다. 모든 제품은 Azure Marketplace에 고유 엔터티로 나타납니다.

**SKU**

SKU는 제품의 가장 작은 구매 가능 단위입니다. SKU를 사용하면 동일한 제품 클래스(제안) 내에서 지원되는 다양한 기능을 구분할 수 있습니다. 예를 들어 제안은 관리되거나 관리되지 않으며 다른 청구 모델이 지원됩니다.

여러 SKU를 추가하는 시나리오는 다음과 같습니다.
- BYOL(사용자 라이선스 필요) 또는 PAYG(종량제)와 같은 다양한 청구 모델을 지원하려고 합니다.
- 각 SKU에서 서로 다른 기능 집합을 지원하며, 각 기능 집합의 가격은 서로 다르게 책정됩니다.

SKU는 Azure Marketplace의 부모 제안 아래에 표시되고 Azure Portal에는 자체의 구입 가능한 엔터티로 표시됩니다.

## <a name="to-create-a-new-offer"></a>새 제안 만들기

1.  [Cloud 파트너 포털](http://cloudpartner.azure.com/)에 로그인합니다.

2.  왼쪽 탐색 모음에서 **+ 새 제안**을 클릭한 다음, **Virtual Machines**를 선택합니다.

    ![가상 머신에 대한 새 제안](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  **새 제안** 아래에서 **편집기**를 선택합니다.

![새 제안 편집기](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  **편집기** 아래에서 다음 보기의 정보를 제공합니다.
    - 제품 설정
    - SKU
    - Marketplace
    - 각 지원 보기에는 사용자가 입력할 수 있는 여러 필드가 있습니다. 필수 필드는 빨간색 별표(\*)로 표시됩니다.

## <a name="to-configure-offer-settings"></a>제안 설정 구성

1. 제안 설정에서 다음 **제안 ID** 필드를 구성합니다.

    **제안 ID**

     게시자 프로필 내에서 고유한 제안 식별자입니다. 이 ID는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)만 사용할 수 있습니다. ID는 대시로 끝날 수 없으며, 최대 50자까지 포함할 수 있습니다. 예를 들어 **contoso** 게시자가 제안 ID가 **sample-vm**인 제안을 게시하면 Azure Marketplace에 **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**로 표시됩니다.  
    >[!Note]
    >제안이 라이브 상태가 되면 이 필드가 잠깁니다.

    **게시자 ID**

    게시자 프로필에 대한 드롭다운 목록입니다. 제안을 게시하려는 프로필을 선택합니다. 
    >[!Note]
    >제품이 라이브 상태가 되면 이 필드가 잠깁니다.

    **Name**

    제품의 표시 이름입니다. 이 이름은 Azure Marketplace 및 Azure Portal에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 제안 이름에 대해 다음 지침을 사용합니다.
    -  제품의 인식 가능한 브랜드 이름을 포함합니다. 
    - 마케팅 방식의 제안인 경우에만 여기에 회사 이름을 포함합니다.
    - 자신의 웹 사이트에서 이 제안을 마케팅하는 경우 이름이 웹 사이트의 이름과 동일한지 확인합니다.

2. **저장**을 선택하여 [제안 설정]을 완료합니다.

## <a name="to-create-a-sku"></a>SKU 만들기

1. **SKU**를 선택합니다. 
2. **SKU 추가**를 선택하여 SKU ID를 입력합니다. SKU ID는 제안 내에서 고유한 SKU에 대한 식별자입니다. 이 ID는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. SKU ID:
    - 최대 50자만 포함할 수 있습니다.
    - 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다.
    - ID는 대시로 끝날 수 없습니다.

    ![SKU 추가](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>SKU 세부 정보 구성

SKU가 추가되면 SKU 보기의 SKU 목록에 표시됩니다. SKU 세부 정보을 보려면 SKU 이름을 선택합니다. 세부 정보 보기를 사용하여 다음 필드에 정보를 추가할 수 있습니다.

### <a name="hide-this-sku"></a>이 SKU 숨기기

이 설정을 사용하여 SKU 가시성을 관리합니다. "이 SKU 숨기기"가 해제되어 있으면 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 SKU가 고객에게 표시됩니다. 구입에 대해 개별적으로 보지 않고 솔루션 템플릿을 통해서만 사용할 수 있도록 하려면 SKU를 숨기는 것이 좋습니다.

### <a name="cloud-availability"></a>클라우드 가용성

이 필드를 사용하여 서로 다른 Azure 클라우드에서 SKU의 가용성을 정의할 수 있습니다.

**공용 Azure**

이 SKU는 Marketplace 통합을 사용하는 모든 공용 Azure 하위 지역의 고객에게 배포할 수 있습니다.

**Azure Government 클라우드**

이 SKU는 Azure Government 클라우드에 배포할 수 있습니다. [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)에 게시하기 전에 게시자가 자신의 솔루션이 예상한 대로 작동하는지 시험하고 유효성을 검사하는 것이 좋습니다. 준비하고 테스트하려면 [평가판 계정을 요청하세요](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>Microsoft Azure Government는 미국 연방, 주, 지방 또는 부족의 고객 및 이러한 실체에 서비스를 제공할 자격이 있는 파트너에 대한 액세스를 제어할 수 있는 정부 커뮤니티 클라우드입니다.

### <a name="countryregion-availability"></a>국가/하위 지역 가용성

이 필드는 SKU를 구입할 수 있는 지역을 식별합니다. SKU를 사용할 수 있는 위치를 신중하게 고려해야 합니다. 일부 국가는 "Microsoft 세금 송금 국가"로 분류됩니다.

-   "Microsoft 세금 송금 국가"에서 Microsoft는 고객으로부터 세금을 징수하고 정부에 세금을 납부(송금)합니다.

-   다른 국가에서는 파트너가 고객으로부터 세금을 징수하고 정부에 납부해야 합니다. 이러한 국가에서 판매하기로 선택한 경우 해당 국가에서 세금을 계산하고 지불하는 기능이 있어야 합니다.

![국가/지역 가용성 선택](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>가격

현재 두 가지 가격 책정 모델이 지원됩니다.

#### <a name="bring-your-own-license-byol"></a>BYOL(사용자 라이선스 필요)

VM에서 실행 중인 소프트웨어에 대한 라이선스를 관리합니다. Microsoft는 인프라 비용만 청구합니다.

#### <a name="usage-based-monthly-billed-sku"></a>사용량 기준 월별 청구 SKU

고객은 VM 크기에 대해 게시자가 설정한 비율에 따라 시간당 단위로 요금이 청구됩니다. SKU의 **시간별 청구** 모델에서 총 가격은 게시자가 청구한 소프트웨어 비용에 Microsoft가 청구한 인프라 비용을 합한 값입니다. 이 총 가격은 고객이 구입을 고려할 때 시간별 및 월별 가격으로 표시됩니다. 이 경우 청구는 월별 기준으로 이루어집니다.

사용량 기반 모델 내에 고객이 지정해야 하는 추가 설정이 있습니다.

**평가판**

고객에 대해 평가판을 제공하려는지 여부를 지정할 수 있습니다.
여기서는 고객이 VM을 배포한 후 처음 30/90일 동안(선택에 따라) 소프트웨어 비용이 청구되지 않습니다. 평가판 기간이 경과한 후에는 시간별 모델에서 게시자가 설정한 비율에 따라 시간당 요금이 청구됩니다.

**코어당 가격**

SKU에 대해 코어당 가격을 설정할 수 있습니다. 이를 위해 고객은 단일 코어에 대한 기준 가격만 입력하면 되며 나머지 코어에 대한 가격은 당사가 자동 계산합니다. 포털에서 USD 단위로 가격을 입력하면 다른 하위 지역에 대한 가격은 자동 계산됩니다. **가격 데이터 내보내기**를 사용하여 다른 하위 지역의 가격을 확인할 수 있습니다.

![코어당 가격](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**불연속 가격**

각 코어의 가격을 별도로 책정하려는 경우 각 코어 집합에 대해 개별적으로 가격을 설정할 수 있습니다.

![불연속 가격](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**가격 내보내기-가져오기**

포털을 통해 구성한 가격을 내보내고 Excel 인터페이스를 통해 변경하는 유연성이 있습니다. 이 방법을 사용하면 하위 지역당 가격 및 현지 통화 단위의 가격을 확인할 수도 있습니다.
**내보내기-가격**을 클릭하면 가격 정보가 미리 채워진 Excel 파일이 다운로드됩니다. Excel 내에서 이 정보를 편집한 다음 **가져오기-가격**을 사용하여 실행한 변경 내용을 가져올 수 있습니다.
가져온 가격은 포털에도 반영됩니다.

이 가격 Excel 내에서 서로 다른 하위 지역에 대한 가격은 현지 통화로 나열됩니다. 당사가 사용하는 환율은 매일 새로 고쳐집니다.

![환율이 적용된 가격 내보내기-가져오기 예제](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   제안이 라이브 상태가 되면 가격을 변경할 수 없습니다. 그러나 지원되는 지역은 여전히 추가하거나 제거할 수 있습니다.
>-   이 가격은  [Azure\'의 가상 머신 가격](https://aka.ms/vmpricingdetails)에 더하여 사용자에게 청구됩니다.
>-   가격은 가격 설정 당시의 사용 가능한 환율을 사용하여 모든 지역에 대해 현지 통화로 설정됩니다.
>-   각 지역의 가격을 개별적으로 설정하거나 확인하려면 가격 책정 스프레드시트를 내보내고 사용자 지정 가격으로 가져옵니다.

## <a name="vm-images"></a>VM 이미지

다음에 수행할 섹션은 VM 이미지 섹션입니다. 이 섹션으로 이동하기 전에 게시할 VHD를 준비 완료해야 합니다. 다음은 VHD를 만드는 데 도움이 되는 몇몇 링크입니다.

-   [Azure Marketplace용 가상 머신 이미지를 만들기 위한 기술 필수 조건](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Linux VHD 만들기 및 업로드](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [이미지에서 Linux VM 만들기 및 시험](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Windows VHD 만들기 및 업로드 ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [이미지에서 Windows VM 만들기 및 시험](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [VHD를 만드는 동안 발생하는 일반적인 문제 해결 방법](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

VHD를 포함할 준비가 완료되면 이 섹션의 작성을 시작할 수 있습니다.
다음은 몇몇 필드에 대한 일부 정보입니다.

### <a name="recommended-vm-sizes"></a>권장되는 VM 크기

권장된 가상 머신 크기를 최대 6개까지 선택합니다. 이는 이미지를 구입하여 배포하려는 경우에 Azure Marketplace의 고객 및 Azure Portal의 가격 책정 계층 블레이드에 표시되는 권장 사항입니다. **이는 유일한 권장 사항입니다. 고객은 이미지에 지정된 디스크에 적용되는 VM 크기를 선택할 수 있습니다.**  다음 화면 캡처에서는 고객이 Azure Portal에서 볼 수 있는 권장되는 VM 크기를 보여 줍니다.


![권장되는 VM 크기](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>포트 열기

열어 놓고 사용할 수 있도록 할 포트를 지정합니다. 이러한 포트는 이 VM 배포 중에 열립니다.

### <a name="adding-vm-images"></a>VM 이미지 추가

다음 단계는 SKU에 대한 VM 이미지를 추가하는 것입니다. SKU당 디스크 버전을 최대 8개까지 추가할 수 있습니다. 특정 SKU에 대해 가장 높은 디스크 버전 번호만 Azure Marketplace에 표시됩니다. 다른 번호는 API를 통해 볼 수 있습니다.

**디스크 버전** 아래에서 **+ 새 버전**을 선택합니다. 그러면 작성해야 하는 다음과 같은 필드가 표시됩니다.

#### <a name="vm-image-version"></a>VM 이미지 버전

VM 이미지 버전은 [의미 체계 버전](http://semver.org/) 형식을 따라야 합니다. 버전은 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다. 다른 SKU에서 이미지는 다른 주 버전과 부 버전을 가질 수 있습니다. SKU 내의 버전은 패치 버전이 증가(X.Y.Z에서 Z)하는 증분 변경이어야 합니다.

#### <a name="os-vhd-url"></a>OS VHD URL

운영 체제 VHD에 대해 만들어진 [공유 액세스 서명 URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images)를 입력합니다.

이 SKU와 연결된 데이터 디스크가 있는 경우 **+ 새 데이터 디스크** 링크를 선택하여 이러한 디스크를 추가하도록 선택할 수 있습니다. 이 작업을 수행하면 작성할 추가 필드가 표시됩니다.

#### <a name="lun-vhd-url"></a>LUN VHD URL

데이터 디스크에 대한 [공유 액세스 서명 URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images)를 입력합니다.

#### <a name="lun-number"></a>LUN 번호

이 LUN에 번호를 할당합니다. 이 번호는 해당 SKU의 해당 데이터 디스크에 예약됩니다.

>[!Note]
>지정된 VM 버전과 데이터 디스크가 있는 SKU를 게시하면 이러한 필드는 잠기게 되며 수정할 수 없습니다. SKU에 추가되는 추가 VM 버전의 경우 지원해야 하는 데이터 디스크 수를 변경할 수 없습니다.

#### <a name="common-sas-url-issues--fixes"></a>일반적인 SAS URL 문제 및 해결

| 문제                                                                 | Message                                                                           | 해결                                                           |  설명서 링크                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| 이미지 복사 중 오류 - "?"가 SAS URL에 없습니다                | 오류: 이미지 복사 제공된 SAS URI를 사용하여 Blob을 다운로드할 수 없습니다.       | 권장 도구를 사용하여 SAS URL 업데이트                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 이미지 복사 중 오류 - "st" 및 "se" 매개 변수가 SAS URL에 없습니다   | 오류: 이미지 복사 제공된 SAS URI를 사용하여 Blob을 다운로드할 수 없습니다.        | 시작 및 종료 날짜로 SAS URL 업데이트             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 이미지 복사 중 오류 - “sp=rl”이 SAS URL에 없습니다                    | 오류: 이미지 복사 제공된 SAS URI를 사용하여 Blob을 다운로드할 수 없습니다.         | "읽기" 및 "나열"로 설정된 사용 권한으로 SAS URL 업데이트     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 이미지 복사 중 오류 - SAS URL은 VHD 이름에 공백을 포함합니다     | 오류: 이미지 복사 제공된 SAS URI를 사용하여 Blob을 다운로드할 수 없습니다.        | 공백 없이 SAS URL 업데이트                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 이미지 복사 중 오류 – SAS URL 권한 부여 오류               | 오류: 이미지 복사 권한 부여 오류로 인해 Blob을 다운로드할 수 없습니다.     | SAS URL 다시 생성                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Marketplace를 구성하려면

Marketplace 보기를 사용하여 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 제안에 대해 표시되는 필드를 구성합니다.

### <a name="preview-subscription-ids"></a>미리 보기 구독 ID

제안이 게시되면 해당 제안에 액세스할 수 있는 Azure 구독 ID의 목록입니다. 이러한 허용 목록 구독을 사용하여 제안을 라이브 상태로 만들기 전에 미리 본 제안을 테스트할 수 있습니다. 파트너 포털을 통해 최대 100개의 구독을 허용 목록에 추가할 수 있습니다.

### <a name="suggested-categories"></a>권장 범주

제공된 목록에서 제품이 가장 잘 연결할 수 있는 범주를 5개까지 선택합니다. 선택한 범주는 고객의 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 사용할 수 있는 제품 범주에 매핑하는 데 사용됩니다.

다음 예제에서는 Azure Marketplace 및 Azure Portal에서 마켓플레이스 정보를 보여 줍니다.

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>로고 지침

Cloud 파트너 포털에 업로드되는 로고에 대해 다음 지침을 따릅니다.

-   Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.

-   Azure 포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로는 이러한 색을 사용하지 마세요. Azure Portal에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다.

    >[!Note] 
    >투명한 배경을 사용하는 경우 로고/텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.

-   로고의 배경에 그라데이션 효과를 사용하지 마십시오.

-   텍스트를 로고 위에 배치하지 않습니다. 여기에는 회사 또는 브랜드 이름이 포함됩니다. 로고의 모양과 느낌은 *평면적*이어야 하며 그라데이션은 사용하지 않습니다.

-   로고는 늘릴 수 없습니다.

#### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. 그러나 로그가 업로드되면 삭제할 수 없습니다. 파트너는 대표 아이콘에 대한 Azure Marketplace 지침을 따라야 합니다.

#### <a name="guidelines-for-the-hero-logo-icon"></a>대표 로고 아이콘에 대한 지침

-   게시자 표시 이름, 계획 제목 및 자세한 제안 요약은 흰색 글꼴로 표시됩니다. 밝은색 배경은 사용하지 않습니다. 대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.

-   제안이 나열되면 게시자 표시 이름, 계획 제목, 자세한 제안 요약 및 [만들기] 단추가 대표 로고 내에 프로그래밍 방식으로 포함됩니다. 대표 로고를 디자인할 때 텍스트는 입력하지 않습니다. 로고의 오른쪽에 빈 공간을 남겨 둡니다. 이 공간은 415 x 100픽셀이어야 하며 왼쪽에서 370픽셀만큼 오프셋됩니다.

![대표 로고 예제](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>잠재 고객 관리

제안의 잠재 고객 관리 설정을 구성하려면 [여기의 지침](./cloud-partner-portal-get-customer-leads.md)을 따르세요.

## <a name="to-configure-support"></a>지원 구성

지원 보기를 사용하여 다음 정보를 제공합니다.

- 엔지니어링과 같은 회사의 기술 지원 담당자
- 고객 지원 담당자

## <a name="to-publish-the-offer"></a>제안을 게시하려면

마지막 단계는 제안을 게시하는 것입니다. [여기의 지침에 따라 제안을 라이브로 전환](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md)하세요.
