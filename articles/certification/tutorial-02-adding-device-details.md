---
title: Azure Certified Device 프로그램 - 자습서 - 디바이스 세부 정보 추가
description: Azure Certified Device 포털에서 프로젝트에 디바이스 세부 정보를 추가하는 단계별 가이드
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 05/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: be6bcf84ebb9c979cd60e6ca6803b7a24a0944a1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108736305"
---
# <a name="tutorial-add-device-details"></a>자습서: 디바이스 세부 정보 추가

이제 디바이스용 프로젝트가 생성되었고 인증 프로세스를 시작할 준비가 되었습니다. 먼저 디바이스 세부 정보를 추가하겠습니다. 여기에는 귀사의 고객이 Azure Certified Device 카탈로그에서 볼 수 있는 기술 사양 및 결정을 내린 후 구매하는 데 사용할 마케팅 세부 정보가 포함됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 구성 요소 및 종속성 기능을 사용하여 디바이스 세부 정보 추가
> * 디바이스에 대한 시작 가이드 업로드
> * 귀사의 고객이 디바이스를 구매할 수 있도록 마케팅 세부 정보 명시
> * (선택 사항) 산업 인증 확인

## <a name="prerequisites"></a>필수 구성 요소

* 로그인되어 있고 [Azure Certified Device 포털](https://certify.azure.com)에서 만든 디바이스용 프로젝트가 있어야 합니다. 자세한 내용은 [자습서](tutorial-01-creating-your-project.md)를 참조하세요.
* PDF 형식의 디바이스용 시작 가이드가 있어야 합니다. 인증 프로그램과 선호하는 언어에 따라 사용할 수 있는 많은 시작 템플릿이 제공됩니다. 이러한 템플릿은 [시작 템플릿](https://aka.ms/GSTemplate "시작 템플릿") GitHub 위치에서 사용할 수 있습니다.

## <a name="adding-technical-device-details"></a>기술적 디바이스 세부 정보 추가

프로젝트 페이지의 첫 번째 섹션인 '디바이스 세부 정보 입력'에서는 디바이스의 핵심 하드웨어 기능에 대한 정보(디바이스 이름, 설명, 프로세서, 운영 체제, 연결 옵션, 하드웨어 인터페이스, 산업 프로토콜, 물리적 크기 등)를 제공할 수 있습니다. 많은 필드가 선택 사항이지만 디바이스가 인증된 후 게시하기로 선택하면 이 정보의 대부분이 Azure Certified Device 카탈로그에서 잠재 고객에게 제공됩니다.

1. 프로젝트 요약 페이지의 '디바이스 세부 정보 입력' 섹션에서 `Add`를 클릭하여 디바이스 세부 정보 섹션을 엽니다. 완료해야 할 6개 섹션이 표시됩니다.

![프로젝트 세부 정보 페이지의 이미지](./media/images/device-details-menu.png)

2. `Basics` 탭에서 프로젝트를 만들 때 이전에 제공한 정보를 검토합니다.
1. `Certifications` 탭에서 디바이스에 대해 적용하려는 인증서를 검토합니다.
1. `Hardware` 탭을 열고 디바이스를 설명하는 하나 **이상** 의 개별 구성 요소를 추가합니다. [구성 요소 사용](how-to-using-the-components-feature.md)에서 지침을 확인할 수도 있습니다.
1. `Save`을 클릭합니다. 그런 다음, 구성 요소 디바이스를 편집하고 고급 세부 정보를 더 추가할 수 있습니다.
1. 운영 조건(예: IP 등급, 작동 온도 또는 안전 인증)과 관련된 정보를 추가합니다.

![하드웨어 섹션의 이미지](./media/images/hardware-section.png)

7. 구성 요소 세부 정보로 캡처되지 않은 추가 디바이스 세부 정보를 `Additional product details` 아래에 나열합니다.
1. 구성 요소 필드에 `Other`를 표시했거나 Azure 인증 팀으로 플래그를 지정하려는 특별한 상황이 있는 경우 `Comments for reviewer` 섹션에 명확한 설명을 남깁니다.
1. `Software` 탭을 열고 하나 **이상** 의 운영 체제를 선택합니다.
1. (**개발자 키트 디바이스에 필요** 및 모든 다른 디바이스에 권장) 디바이스를 Azure에 연결하는 데 필요한 설정 프로세스를 나타내는 수준을 선택합니다. 수준 2를 선택하는 경우 사용 가능한 소프트웨어 이미지의 링크를 제공해야 합니다.

![소프트웨어 섹션의 이미지](./media/images/software-section.png)

11. Azure에 데이터를 보내기 위해 디바이스에 추가 하드웨어나 서비스가 필요한 경우 `Dependencies` 탭을 사용하여 종속성을 나열합니다. 또한 [종속성 나열](how-to-indirectly-connected-devices.md)에 관한 추가 지침을 볼 수 있습니다.
1. 제공한 정보에 만족하면 `Review` 탭을 사용하여 입력한 전체 디바이스 세부 정보 집합에 대한 읽기 전용 개요를 볼 수 있습니다.
1. 페이지 위쪽에서 `Project summary`를 클릭하여 요약 페이지로 돌아갑니다.

![프로젝트 세부 정보 검토 페이지](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>시작 가이드 업로드

시작 가이드는 제품의 설정, 구성 및 관리를 간소화하는 PDF 문서입니다. 고객이 귀사의 디바이스를 사용하여 Azure에서 디바이스를 간편하게 연결하고 지원할 수 있도록 하는 것이 목적입니다. 인증 프로세스의 일환으로 파트너는 자사에 가장 관련성이 높은 인증 프로그램에 대한 시작 가이드를 **하나** 제공해야 합니다.

1. 제공된 [템플릿](https://aka.ms/GSTemplate)에 따라 시작 가이드 PDF에 요청된 모든 정보를 제공했는지 다시 확인합니다. 사용하는 템플릿은 신청하는 인증 배지에 따라 결정해야 합니다. (예를 들어 IoT 플러그 앤 플레이 디바이스는 IoT 플러그 앤 플레이 템플릿을 사용합니다. Azure Certified Device 기준 인증만 신청하는 디바이스는 Azure Certified Device 템플릿을 사용합니다.)
1. 프로젝트 요약 페이지의 '시작' 가이드 섹션에서 `Add`를 클릭합니다.

![GSG 단추의 이미지](./media/images/gsg-menu.png)

2. '파일 선택'을 클릭하여 PDF를 업로드합니다.
1. 미리 보기에서 문서를 검토하여 서식을 지정합니다.
1. '저장' 단추를 클릭하여 업로드를 저장합니다.
1. 페이지 위쪽에서 `Project summary`를 클릭하여 요약 페이지로 돌아갑니다.

## <a name="providing-marketing-details"></a>마케팅 세부 정보 제공

이 영역에서는 디바이스에 대한 고객용 마케팅 정보를 제공합니다. 이러한 필드는 인증된 디바이스를 게시하기로 선택하면 Azure Certified Device 카탈로그에 사례로 제시됩니다.

1. '마케팅 세부 정보 추가' 섹션에서 `Add`를 클릭하여 마케팅 세부 정보 페이지를 엽니다.

![마케팅 세부 정보 섹션의 이미지](./media/images/marketing-details.png)

1. 카탈로그에 사용될 제품 사진을 JPEG 또는 PNG 형식으로 업로드합니다.
1. 카탈로그의 제품 설명 페이지에 표시될 디바이스에 대한 간단한 설명을 작성합니다.
1. 디바이스의 지리적 가용성을 명시합니다.
1. 디바이스에 대한 제조업체의 마케팅 페이지 링크를 제공합니다. 디바이스에 대한 추가 정보를 제공하는 사이트에 대한 링크여야 합니다.
    > [!Note]
    > 제공된 모든 URL은 유효하거나, 승인 후 게시될 때 활성 상태여야 합니다.*)

1. 디바이스가 최적화된 대상 산업을 3개까지 표시합니다.
1. 디바이스의 유통업체 최대 5곳에 관한 정보를 제공합니다. 여기에는 제조업체의 자체 사이트가 포함될 수 있습니다.

    > [!Note]
    > 유통업체 제품 페이지 URL이 제공되지 않으면, 카탈로그의 `Shop` 단추는 `Distributor page`에 제공된 링크로 기본 설정되며, 디바이스용 특정 페이지가 아닐 수 있습니다. 유통업체 URL은 고객이 디바이스를 구매할 수 있는 특정 페이지로 연결되는 것이 가장 좋지만 필수는 아닙니다. 유통업체가 제조업체와 동일하면 이 URL이 제조업체의 마케팅 페이지와 동일할 수 있습니다.*)

1. `Save`를 클릭하여 정보를 확인합니다.
1. 페이지 위쪽에서 `Project summary`를 클릭하여 요약 페이지로 돌아갑니다.

## <a name="declaring-additional-industry-certifications"></a>추가 산업 인증 선언

디바이스에 대해 받은 추가 산업 인증을 홍보할 수도 있습니다. 이러한 인증은 디바이스를 사용하는 용도에 대해 명확한 설명을 제공하는 데 유용할 수 있으며 Azure Certified Device 카탈로그에서 검색이 가능합니다.

1. '산업 인증 제공' 섹션에서 `Add`를 클릭합니다.
1. `Add a certification`을 클릭하여 일반적인 산업 인증 프로그램 목록에서 선택합니다. 제품이 획득한 인증이 목록에 없는 경우 `Other (please specify)`를 선택하여 사용자 지정 문자열 값을 지정할 수 있습니다.
1. (선택 사항) 검토자에게 설명 또는 메모를 제공합니다. 단, 이러한 메모는 카탈로그에서 공개적으로 볼 수 없습니다.
1. `Save`를 클릭하여 정보를 확인합니다.
1. 페이지 위쪽에서 `Project summary`를 클릭하여 요약 페이지로 돌아갑니다.

## <a name="next-steps"></a>다음 단계

이제 디바이스를 설명하는 과정이 완료되었습니다. 이 내용은 Azure Certified Device 검토 팀과 고객이 귀사의 제품을 더 잘 이해하는 데 도움이 될 수 있습니다. 제공한 정보가 만족스러우면, 이제 인증 프로세스의 테스트 단계로 이동할 준비가 된 것입니다.
> [!div class="nextstepaction"]
> [자습서: 디바이스 테스트](tutorial-03-testing-your-device.md)
