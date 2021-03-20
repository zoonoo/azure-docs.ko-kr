---
title: 위협 인텔리전스 데이터 업데이트
description: 위협 인텔리전스 데이터 패키지는 각각의 IoT 버전용 새 Defender에 제공 되거나 릴리스 사이에 필요한 경우에 제공 됩니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521410"
---
# <a name="threat-intelligence-research-and-packages"></a>위협 인텔리전스 연구 및 패키지

Microsoft의 보안 팀은 독점 ICS 위협 인텔리전스 및 취약성 연구를 수행 합니다. 이러한 팀은 MSTIC (Microsoft 위협 인텔리전스 센터), DART (Microsoft 검색 및 대응 팀), DCU (Digital Crimes Unit) 및 섹션 52 (IoT/OT/ICS 도메인 전문가 (ICS 관련 제로, 리버스 엔지니어링 맬웨어, 캠페인 및 악의적 사용자)를 포함 합니다.

팀은 Microsoft에 대 한 보안 검색, 분석 및 응답을 제공 합니다.

- 클라우드 인프라 및 서비스.
- 기존 제품 및 장치.
- 내부 회사 리소스.

보안 팀은 다음과 같은 이점을 얻을 수 있습니다.

- 알려진 위협 및 관련 위협 으로부터 보호 합니다.
- 심사 및 우선 순위를 지정 하는 데 도움이 되는 정보입니다.
- 영향을 받기 전에 위협에 대 한 전체 컨텍스트를 이해 합니다.
- 보다 적절 하 고 정확 하며 실행 가능한 데이터

이 인텔리전스는 Microsoft platform analytics를 보강 하 고 인시던트 대응 및 위반 조사를 위해 회사의 관리 되는 서비스를 지 원하는 컨텍스트 정보를 추가 합니다. 위협 인텔리전스 패키지에는 서명 (맬웨어 서명 포함), CVEs 및 기타 보안 콘텐츠가 포함 됩니다.

IoT 포털 용 Azure Defender의 **업데이트** 페이지에서 패키지를 다운로드할 수 있습니다.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="IoT 용 Azure Defender 포털을 통해 업데이트를 다운로드 합니다.":::

## <a name="update-threat-intelligence-data"></a>위협 인텔리전스 데이터 업데이트

위협 인텔리전스 패키지는 IoT 버전 업데이트를 위해 각각의 새로운 Defender와 함께 제공 되거나 릴리스 사이에 필요한 경우에 제공 됩니다.

IoT 포털 용 Defender에서 다운로드 하는 패키지는 개별 센서에 수동으로 업로드할 수 있습니다. 온-프레미스 관리 콘솔에서 센서를 관리 하는 경우 위협 인텔리전스 패키지를 관리 콘솔로 다운로드 하 고 여러 센서에 동시에 푸시할 수 있습니다.

단일 센서에서 패키지를 업데이트 하려면 다음을 수행 합니다.

1. IoT **업데이트** 에 대 한 Azure Defender 페이지로 이동 합니다.

2. **위협 인텔리전스** 패키지를 다운로드 하 여 저장 합니다.

3. 센서 콘솔에 로그인 합니다.

4. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

5. **위협 인텔리전스 데이터** 를 선택 하 고 **업데이트** 를 선택 합니다.

6. 새 패키지를 업로드 합니다.

여러 센서에서 패키지를 동시에 업데이트 하려면 다음을 수행 합니다.

1. IoT **업데이트** 에 대 한 Azure Defender 페이지로 이동 합니다.

2. **위협 인텔리전스** 패키지를 다운로드 하 여 저장 합니다.

3. 관리 콘솔에 로그인 합니다.

4. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

5. **센서 엔진 구성** 섹션에서 업데이트 된 패키지를 수신 해야 하는 센서를 선택 합니다.  

6. **위협 인텔리전스 데이터 선택** 섹션에서 더하기 기호 ()를 선택 **+** 합니다.

7. 패키지를 업로드 합니다.

## <a name="see-also"></a>참고 항목

[업데이트 버전](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
