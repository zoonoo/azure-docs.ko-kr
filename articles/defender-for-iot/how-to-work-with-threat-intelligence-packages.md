---
title: 위협 인텔리전스 데이터 업데이트
description: 위협 인텔리전스 데이터 패키지는 각 새 Defender for IoT 버전과 함께 제공되거나 필요한 경우 릴리스 간에 제공됩니다.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 386d59d33c4f9695b8fc5243dab345321cff4a5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784053"
---
# <a name="threat-intelligence-research-and-packages"></a>위협 인텔리전스 리서치 및 패키지

Microsoft의 보안팀은 독점 ICS 위협 인텔리전스 및 취약성 연구를 수행합니다. 이 팀에는 MSTIC(Microsoft 위협 인텔리전스 센터), DART(Microsoft Detection and Response Team), DCU(Digital Crimes Unit) 및 섹션 52(ICS 관련 제로 데이, 리버스 엔지니어링 맬웨어, 캠페인, 악의적 사용자를 추적하는 IoT/OT/ICS 도메인 전문가)가 포함됩니다.

해당 팀은 다음 Microsoft의 서비스에 대한 보안 검색, 분석, 대응을 제공합니다.

- 클라우드 인프라 및 서비스.
- 기존 제품 및 디바이스.
- 내부 회사 리소스.

보안 팀은 다음과 같은 이점을 얻을 수 있습니다.

- 알려진 위협 및 관련 위협으로부터의 보호.
- 심사 및 우선 순위를 지정하는 데 도움이 되는 인사이트.
- 위협의 영향을 받기 전에 위협에 대한 전체 컨텍스트 이해.
- 더욱 적절하고 정확하며 실행 가능한 데이터.

이 인텔리전스는 상황에 맞는 정보를 추가하여 Microsoft 플랫폼 분석을 강화하고 인시던트 대응 및 위반 조사를 위해 회사의 관리 서비스를 지원합니다. 위협 인텔리전스 패키지에는 서명(맬웨어 서명 포함), CVE, 기타 보안 콘텐츠가 포함됩니다.

Azure Defender for IoT 포털의 **업데이트** 페이지에서 패키지를 다운로드할 수 있습니다.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Azure Defender for IoT 포털을 통해 업데이트를 다운로드 합니다.":::

## <a name="update-threat-intelligence-data"></a>위협 인텔리전스 데이터 업데이트

위협 인텔리전스 패키지는 각 새 Defender for IoT 버전 업데이트와 함께 제공되거나 필요한 경우 릴리스 간에 제공됩니다.

Defender for IoT 포털에서 다운로드하는 패키지는 개별 센서에 수동으로 업로드할 수 있습니다. 온-프레미스 관리 콘솔에서 센서를 관리하는 경우 위협 인텔리전스 패키지를 관리 콘솔로 다운로드하고 여러 센서로 동시에 푸시할 수 있습니다.

단일 센서에서 패키지를 업데이트하려면 다음을 수행합니다.

1. Azure Defender for IoT **업데이트** 페이지로 이동합니다.

2. **위협 인텔리전스** 패키지를 다운로드하고 저장합니다.

3. 센서 콘솔에 로그인합니다.

4. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

5. **위협 인텔리전스 데이터** 를 선택하고 **업데이트** 를 선택합니다.

6. 새 패키지를 업로드합니다.

여러 센서에서 패키지를 동시에 업데이트하려면 다음을 수행합니다.

1. Azure Defender for IoT **업데이트** 페이지로 이동합니다.

2. **위협 인텔리전스** 패키지를 다운로드하고 저장합니다.

3. 관리 콘솔에 로그인합니다.

4. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

5. **센서 엔진 구성** 섹션에서 업데이트된 패키지를 수신해야 하는 센서를 선택합니다.  

6. **위협 인텔리전스 데이터 선택** 섹션에서 더하기 기호( **+** )를 선택합니다.

7. 패키지를 업로드합니다.

## <a name="see-also"></a>참고 항목

[업데이트 버전](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
