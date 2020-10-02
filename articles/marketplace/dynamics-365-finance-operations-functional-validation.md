---
title: Azure Marketplace에서 AppSource Dynamics 365 재무 및 운영 제품의 기능 유효성 검사
description: Azure Marketplace에서 Dynamics 365 재무 및 운영 제품의 유효성을 검사 하는 방법입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 10137e59e0ea06fa785fccc215c867b6d8c0cb76
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651159"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 재무 및 운영 기능 유효성 검사

[파트너 센터](https://partner.microsoft.com/dashboard/home)에서 첫 게시를 완료 하려면 Dynamics 365 재무 및 작업에 대 한 제품에 두 가지 기능 유효성 검사가 필요 합니다.

- 기본 기능을 보여 주는 Dynamics 365 환경의 데모 비디오를 업로드 합니다.
- 솔루션의 LCS ( [수명 주기 서비스](https://lcs.dynamics.com/) ) 환경을 보여 주는 스크린샷을 제공 합니다.

> [!NOTE]
> 후속 재인증 게시는 데모를 요구 하지 않습니다. 자세히 알아보려면 [Appsource 정책 문서](https://docs.microsoft.com/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops)를 참조 하세요.

## <a name="how-to-validate"></a>유효성을 검사 하는 방법

기능 유효성 검사에는 두 가지 옵션이 있습니다.

- Microsoft의 태평양 표준시 (PST) 업무 시간 동안 30 분 회의 통화를 개최 하 여 [LCS](https://lcs.dynamics.com/) 환경 및 솔루션을 시연 하 고 기록 합니다.
- 파트너 센터에서 [상업용 마켓플레이스](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)개요로 이동 하 여  >  **Overview** 제품의 추가 콘텐츠 탭에서 데모 비디오 URL 및 LCS 스크린샷을 업로드 합니다.

Microsoft 인증 팀은 비디오와 파일을 검토 한 후 다음 단계에 대 한 솔루션 또는 전자 메일을 승인 합니다.

### <a name="option-1-30-minute-conference-call"></a>옵션 1:30-분 회의 통화

최종 검토 호출을 예약 하려면 [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) 제품의 이름과 오전 8 시와 오후 5 시 사이의 몇 가지 잠재적인 시간 슬롯에 문의 하세요.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>옵션 2: 데모 비디오 및 LCS 스크린샷 업로드

1. 비디오를 기록 하 고 원하는 호스팅 사이트에 주소를 업로드 합니다. 다음 지침을 따릅니다.

    - Microsoft 인증 팀에서 볼 수 있습니다.
    - 20 분 미만.
    - Dynamics 365 환경에서 솔루션에 대 한 최대 3 개의 핵심 기능을 제공 합니다.

    > [!NOTE]
    > 지침을 충족 하는 경우 기존 마케팅 비디오를 사용할 수 있습니다.

2. 게시 하려는 제품 또는 솔루션과 일치 하는 [LCS](https://lcs.dynamics.com/) 환경의 다음 스크린샷을 찍습니다. 인증 팀이 텍스트를 읽을 수 있을 만큼 명확 해야 합니다. 스크린샷을 JPG 파일로 저장 합니다. [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com)스크린샷을 제공 하는 대신 설치를 확인할 수 있도록 LCS 환경에 사용 권한을 제공할 수 있습니다.

    1. **LCS**  >  **비즈니스 프로세스 모델러**  >  **프로젝트 라이브러리로**이동 합니다. 모든 프로세스 단계의 스크린샷을 찍습니다. 다음과 같이 **다이어그램과** **검토** 된 열을 포함 합니다.

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="프로젝트 라이브러리 창을 표시 합니다.":::

      2. **LCS**  >  **솔루션 관리**  >  **테스트 솔루션 패키지**로 이동 합니다. 다음 예제에 표시 된 패키지 개요 및 콘텐츠를 포함 하는 스크린샷을 찍습니다.

    | 필드 | 이미지 <img src="" width="400px">|
    | --- | --- |
    | 패키지 개요 | [!["패키지 개요" 창을 보여 주는 스크린샷](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>솔루션 승인자</li></ul> | [![패키지 개요 화면](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | 패키지 내용<ul><li>모델</li><li>소프트웨어 배포 가능 패키지</li></ul> | [![패키지 콘텐츠 화면 1](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>GER 구성</li><li>데이터베이스 백업</li></ul><br>**GER 구성** 섹션에는 아티팩트가 필요 하지 않습니다. | [![패키지 콘텐츠 화면 2](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI 보고서 모델</li><li>BPM 아티팩트</li></ul><br>아티팩트는 **Power BI** 섹션에서 필요 하지 않습니다. | [![패키지 콘텐츠 화면 3](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>데이터 패키지 처리</li><li>솔루션 사용권 계약 및 개인 정보 취급 방침</li></ul><br>**GER 구성** 및 **Power BI 보고서 모델** 섹션은 재무 및 운영 제품에 포함 될 수 있는 선택적 요소입니다. | [![패키지 콘텐츠 화면 4](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    LCS 포털의 각 섹션에 대해 자세히 알아보려면 [Lcs 사용자 가이드](https://docs.microsoft.com/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)를 참조 하세요.

3. 파트너 센터에 업로드 합니다.

    1. 데모 비디오 주소 및 스크린샷을 포함 하는 텍스트 문서를 만들거나 스크린샷을 별도의 JPG 파일로 저장 합니다.
    2. 재무 및 운영 제품의 **추가 콘텐츠** 탭에서 파트너 센터의 [상업 Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) 에 있는 .zip 파일에 텍스트 및 JPG 파일을 추가 합니다.

    [![프로젝트 라이브러리 창을 표시 합니다.](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>다음 단계

제품을 만드는 방법에 대 한 자세한 내용은 [운영 제안의 Dynamics 365 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-operations-offer)를 참조 하세요.
