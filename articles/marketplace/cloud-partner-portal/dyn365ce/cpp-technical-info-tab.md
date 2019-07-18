---
title: Dynamics 365 Customer Engagement 기술 정보 탭에 대 한 | Azure Marketplace
description: AppSource Marketplace에서 Dynamics 365 for Customer Engagement 애플리케이션에 대한 기술 정보를 지정하는 방법입니다.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: df7f3be5a92a183176da7851ce1943793b8b57d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942384"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 for Customer Engagement 기술 정보 탭

**새 제안** 페이지의 **기술 정보** 탭을 사용하면 CRM 패키지 및 마케팅 로고 자산을 포함하여 Dynamics 365 for Customer Engagement 애플리케이션에 대한 자세한 정보를 지정할 수 있습니다.  이 탭은 **애플리케이션 정보**, **CRM 패키지**, **CRM 패키지 가용성** 및 **마케팅 아티팩트**의 4개 섹션으로 구분됩니다. 필드 이름에 추가된 별표(*)는 필수 항목임을 나타냅니다. 


## <a name="application-info-section"></a>애플리케이션 정보 섹션

이 섹션에서는 Dynamics 365 애플리케이션에 대한 세부 정보를 제공합니다.

![기술 정보 탭의 애플리케이션 정보 섹션](./media/dynce-technical-info-tab1.png)

다음 표에서는 이러한 필드에 대해 설명합니다. 필수 필드는 별표(*)로 표시됩니다.

|      필드                    |    설명                  |
|    ---------                  |  ---------------                |
|   기본 라이선스 모델\*          |  라이선스 모델은 Dynamic 365 관리 센터에서 고객에게 애플리케이션을 할당받는 방법을 결정합니다. **리소스** 라이선스는 인스턴스 기반이며, **사용자** 라이선스는 테넌트당 하나씩 할당됩니다.  |
|  아웃 바운드 S2S 및 CRM Secure Store 액세스\* |  CRM 보안 저장소 또는 S2S(서버 간) 아웃바운드 액세스 구성을 사용하도록 설정합니다. *이 기능을 사용하려면 Dynamics 365 팀에서 인증 단계 중에 특별히 고려해야 합니다.* Microsoft는 이 기능을 지원하기 위한 추가 단계를 수행하도록 요청하는 알림을 보냅니다.  |
| CRM 수명 주기 이벤트를 구독합니다\* | Dynamics 365 수명 주기 이벤트와 통합하려면 Microsoft와의 특별 계약을 통해 등록된 전용 서비스를 제공해야 합니다. *이 기능을 사용하려면 Dynamics 365 팀에서 인증 단계 중에 특별히 고려해야 합니다.* 이 기능을 지원하기 위한 추가 단계를 수행하도록 요청하는 알림을 받습니다.  |
| 애플리케이션 구성 URL | 사용자가 애플리케이션을 구성할 수 있도록 하는 웹 페이지의 URL입니다. |
| 적용 가능한 Dynamics 365 제품  | 이 제안이 적용되는 Dynamics 365 제품을 선택합니다. 이 제안은 AppSource에서 선택한 제품 아래에 표시됩니다.  |
| 마케팅만 변경         | 이 옵션을 [예]로 설정하면 기존 제안에 대한 마케팅/설명만 변경되었음을 나타냅니다.  제안은 이러한 변경을 통해 인증 및 프로비전 단계를 무시할 수 있습니다.  |
|  |  |


## <a name="crm-package-section"></a>CRM 패키지 섹션

이 섹션에서는 AppSource 패키지 파일에 대한 세부 정보를 제공합니다.  이 정보는 Dynamics 365 유효성 검사 및 인증 팀에서 사용합니다.

![기술 정보 탭의 CRM 패키지 섹션](./media/dynce-technical-info-tab2.png)

다음 표에서는 이러한 필드에 대해 설명합니다.  필수 필드는 별표(*)로 표시됩니다.

|      필드                    |    설명                  |
|    ---------                  |  ---------------                |
|  패키지의 파일 이름\*     |  패키지(.zip)의 파일 이름입니다.  이 이름은 *공개되지 않으며* Dynamics 365 인증 팀에서 내부적으로 사용합니다.  |
|  패키지 위치 Url\*      |  업로드된 패키지 파일이 포함된 Azure Storage 계정의 URL입니다. 이 URL에는 팀에서 확인을 위해 패키지를 선택할 수 있게 하는 읽기 전용 SAS 키가 있어야 합니다.  |
| 둘 이상의 crm 패키지\*     | 서로 다른 패키지가 포함된 여러 버전의 CRM을 지원하는 경우에만 [예]를 선택합니다.  각 버전에는 개별적으로 만들어야 하는 해당 패키지 파일이 있습니다.  |
| 시나리오 및 사용 사례 자산\*   | Dynamics 365 유효성 검사 팀에서 사용할 수 있도록 애플리케이션에 대한 기능 사양 문서를 업로드할 수 있도록 합니다.  이 사양에 대한 기본 설정 형식은 [E2E 사용자 시나리오 템플릿](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx)입니다.  |
|  |  |


## <a name="crm-package-availability-section"></a>CRM 패키지 가용성 섹션

이 섹션에서는 고객이 애플리케이션을 사용할 수 있는 지리적 지역을 선택합니다.  다음 자치 지역에 배포 *특별 권한 및 유효성 검사 필요* 인증 프로세스 중: [독일](https://docs.microsoft.com/azure/germany/)하십시오 [미국 정부 클라우드](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), 및 팁입니다.


## <a name="marketing-artifacts-section"></a>마케팅 아티팩트 섹션

이 섹션에서는 AppSource Marketplace에서 패키지를 나타내는 데 사용할 애플리케이션 로고를 업로드합니다.  로고 이미지는 255x115 픽셀 크기의 PNG 형식이어야 합니다.


## <a name="next-steps"></a>다음 단계

[시험 사용 탭](./cpp-testdrive-tab.md)을 완료하여 애플리케이션 데모를 제공하는 것이 좋습니다.
