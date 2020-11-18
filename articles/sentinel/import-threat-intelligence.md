---
title: Azure 센티널로 위협 인텔리전스 가져오기 | Microsoft Docs
description: Azure 센티널에서 위협 인텔리전스 피드를 사용 하 여 데이터를 분석 하 고 위협을 감지 하 고 경고 및 인시던트를 생성 합니다. 통합 문서를 사용 하 여 위협 인텔리전스 정보를 시각화 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: bde11c8e06891025be96810acf6d87952a3d8d2f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660783"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Azure Sentinel로 위협 인텔리전스 가져오기

## <a name="introduction-to-threat-intelligence"></a>위협 인텔리전스 소개

CTI (사이버 위협 인텔리전스)는 시스템 및 사용자에 게 알려진 기존 또는 잠재적인 위협을 설명 하는 정보입니다. 이러한 유형의 정보는 특정 위협 행위자의 동기, 인프라 및 기술을 자세히 설명 하는 작성 된 보고서에서 IP 주소, 도메인 및 사이버 위협과 관련 된 파일 해시의 특정 관찰에 이르기까지 많은 형태를 사용 합니다. CTI는 조직에서 비정상적인 활동에 필수적인 컨텍스트를 제공 하는 데 사용 되므로 보안 담당자가 사용자 및 자산을 보호 하기 위한 조치를 신속 하 게 수행할 수 있습니다. CTI는 조직 내 보안 조사 과정에서 수집 된 오픈 소스 데이터 피드, 위협 인텔리전스 공유 커뮤니티, 상용 인텔리전스 피드, 로컬 인텔리전스 등의 여러 위치에서 원본으로 사용할 수 있습니다.

Azure 센티널과 같은 SIEM (보안 정보 및 이벤트 관리) 솔루션 내에서 가장 많이 사용 되는 CTI 형태는 위협 지표입니다. 일반적으로 손상 또는 IoCs 표시기 라고 합니다. 위협 지표는 Url, 파일 해시 또는 IP 주소와 같은 관찰을 피싱, 봇 넷, 맬웨어 등의 알려진 위협 작업과 연결 하는 데이터입니다. 이러한 형태의 위협 인텔리전스를 보안 제품 및 자동화에 적용 하 여 조직에 대 한 잠재적 위협을 보호 하 고 검색할 수 있으므로 전략적 위협 인텔리전스 라고 합니다. Azure 센티널에서 위협 지표를 사용 하 여 사용자 환경에서 관찰 된 악의적인 활동을 검색 하 고, 응답 결정을 알리는 데 도움이 되는 보안 사관에 대 한 컨텍스트를 제공할 수 있습니다.

다음 작업을 통해 Azure 센티널에 위협 인텔리전스 (TI)를 통합할 수 있습니다.

- 다양 한 TI 플랫폼에 **데이터 커넥터** 를 사용 하 여 Azure 센티널로 [위협 인텔리전스를 가져옵니다](./connect-threat-intelligence.md) .
- **로그** 및 Azure 센티널의 새로운 **위협 인텔리전스** 영역에서 가져온 위협 인텔리전스를 보고 관리 합니다.
- 기본 제공 **분석** 규칙 템플릿을 사용 하 여 가져온 위협 인텔리전스를 사용 하 여 보안 경고 및 인시던트를 생성할 수 있습니다.
- **위협 인텔리전스 통합 문서** 를 사용 하 여 Azure 센티널에서 위협 인텔리전스에 대 한 주요 정보를 시각화 합니다.

또한 위협 인텔리전스는 **구하기** 및 **노트북** 같은 다른 Azure 센티널 환경 내에서 유용한 컨텍스트를 제공 하며,이 문서에서 다루지 않지만 노트북 내에서 CTI 사용에 대해 설명 하는 [센티널의 Jupyter 노트북에 대 한 Ian olen의이 유용한 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)에서 이러한 환경을 다룹니다.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>위협 인텔리전스 용 Azure 센티널 데이터 커넥터

Azure 센티널의 다른 모든 이벤트 데이터와 마찬가지로 위협 표시기는 데이터 커넥터를 사용 하 여 가져옵니다. Azure 센티널에는 위협 지표, **위협 인텔리전스-TAXII** 및 **위협 인텔리전스 플랫폼** 을 위해 특별히 제공 되는 두 개의 데이터 커넥터가 있습니다. 조직에서 위협 지표를 원본으로 사용 하는 위치에 따라 데이터 커넥터를 단독으로 사용 하거나 두 커넥터를 함께 사용할 수 있습니다. 각 데이터 커넥터에 대해 알아보겠습니다.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>위협 인텔리전스 플랫폼 데이터 커넥터를 사용 하 여 Azure 센티널에 위협 표시기 추가

많은 조직에서는 TIP (위협 인텔리전스 플랫폼) 솔루션을 활용 하 여 다양 한 원본에서 위협 지표 피드를 집계 하 고, 플랫폼 내에서 데이터를 만든 다음, 네트워크 장치, advanced threat protection 솔루션 또는 SIEMs (예: Azure 센티널)와 같은 다양 한 보안 솔루션에 적용할 위협 지표를 선택 합니다. 조직에서 잘못 된 팁 솔루션 (예: Anomali ThreatStream, ThreatConnect, EclecticIQ Platform, ThreatQ Threat Intelligence Platform 또는 Palo Alto Networks ' MineMeld)을 활용 하는 경우 **위협 인텔리전스 플랫폼 데이터 커넥터** 를 사용 하 여 Azure 센티널에 위협 지표를 가져올 수 있습니다. 커넥터는 [Microsoft Graph Security tiIndicators api](/graph/api/resources/tiindicator) 와 함께 작동 하므로이를 위해 사용자 지정 위협 인텔리전스 플랫폼에서 커넥터를 사용 하 여 Azure 센티널 (및 다른 Microsoft 보안 솔루션 (예: Defender ATP)에 지표를 전송 하는 TIINDICATORS API를 활용할 수도 있습니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="위협 인텔리전스 가져오기 경로":::

다음 단계에 따라 통합 팁 또는 사용자 지정 위협 인텔리전스 솔루션에서 Azure 센티널로 위협 지표를 가져옵니다.

1. Azure Active Directory에서 응용 프로그램 ID 및 클라이언트 암호를 가져옵니다.

1. TIP 솔루션 또는 사용자 지정 응용 프로그램에이 정보를 입력 합니다.

1. Azure 센티널에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용 하도록 설정

이러한 각 단계에 대 한 자세한 내용은 다음과 같습니다.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Azure Active Directory에서 응용 프로그램 ID 및 클라이언트 암호를 가져옵니다.

팁을 사용 하거나 사용자 지정 솔루션을 사용 하 여 작업 하는 경우에는 tiIndicators API에 연결 하 여 위협 지표를 전송 하기 위한 몇 가지 기본 정보가 필요 합니다. 가져와야 하는 세 가지 정보는 다음과 같습니다.
- 애플리케이션(클라이언트) ID
- 디렉터리(테넌트) ID
- 클라이언트 암호

이 정보는 항상 다음 세 단계를 포함 하는 **앱 등록** 이라는 프로세스를 통해 Azure Active Directory에서 제공 됩니다.
- Azure Active Directory에 앱 등록
- Microsoft Graph tiIndicators API에 연결 하 고 위협 지표를 보내기 위해 앱에서 요구 하는 권한을 지정 합니다.
- 조직에서 동의를 받아이 응용 프로그램에 이러한 사용 권한을 부여 합니다.  

**Azure Active Directory에 애플리케이션 등록**

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure Active Directory** 서비스로 이동 합니다.

1. 메뉴에서 **앱 등록** 을 선택 하 고 **새 등록** 을 선택 합니다.

1. 응용 프로그램 등록의 이름을 선택 하 고, **단일 테 넌 트** 라디오 단추를 선택 하 고, **등록** 을 선택 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="애플리케이션 등록":::

1. 결과 화면에서 **응용 프로그램 (클라이언트) id** 및 **디렉터리 (테 넌 트) id** 값을 복사 합니다. 다음은 나중에 Azure 센티널에 위협 지표를 보내도록 팁 또는 사용자 지정 솔루션을 구성 하는 데 필요한 첫 번째 두 가지 정보입니다.

**응용 프로그램에 필요한 사용 권한 지정**

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure Active Directory** 서비스로 이동 합니다.

1. 메뉴에서 **앱 등록** 을 선택 하 고 새로 등록 된 앱을 선택 합니다.

1. 메뉴에서 **API 권한** 을 선택 하 고 **권한 추가** 단추를 클릭 합니다.

1. **API 선택** 페이지에서 **Microsoft Graph** 를 선택 하 여 Microsoft Graph 사용 권한 목록에서 선택 합니다.

1. **응용 프로그램에 필요한 사용 권한 유형을** 묻는 메시지가 표시 되 면 **응용 프로그램 사용 권한** 을 선택 합니다. 앱 ID 및 앱 암호 (API 키)를 사용 하 여 인증 하는 응용 프로그램에서 사용 하는 사용 권한의 유형입니다.

1. **ThreatIndicators** 을 선택 하 고 **사용 권한 추가** 를 선택 하 여 앱의 사용 권한 목록에이 권한을 추가 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="권한 지정":::

**조직에서 이러한 사용 권한을 부여 하는 동의 얻기**

1. 동의를 부여 하려면 앱의 API 사용 권한 페이지에서 **테 넌 트에 대 한 관리자 동의 부여** 단추를 선택할 수 있는 전역 관리자 Azure Active Directory 필요 합니다. 계정에 전역 관리자 역할이 없으면이 단추를 사용할 수 없으며 조직의 전역 관리자에 게이 단계를 수행 하도록 요청 해야 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="동의 권한 부여":::

1. 앱에 동의가 부여 되 면 **상태** 아래에 녹색 확인 표시가 표시 됩니다.
 
이제 앱이 등록 되 고 사용 권한이 부여 되었으므로 앱에 대 한 클라이언트 암호를 사용 하 여 목록에서 마지막 항목을 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure Active Directory** 서비스로 이동 합니다.

1. 메뉴에서 **앱 등록** 을 선택 하 고 새로 등록 된 앱을 선택 합니다.

1. 메뉴에서 **인증서 & 암호** 를 선택 하 고 **새 클라이언트 암호** 단추를 클릭 하 여 앱에 대 한 비밀 (API 키)을 가져옵니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="클라이언트 암호 가져오기":::

1. **추가** 단추를 클릭 하 고이 페이지에서 다른 곳으로 이동 하는 경우이 암호를 다시 검색할 수 없기 때문에 **클라이언트 암호를 복사** 해야 합니다. 팁 또는 사용자 지정 솔루션을 구성할 때이 값이 필요 합니다.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>TIP 솔루션 또는 사용자 지정 응용 프로그램에이 정보를 입력 합니다.

이제 Azure 센티널에 위협 지표를 보내도록 팁 또는 사용자 지정 솔루션을 구성 하는 데 필요한 세 가지 정보를 모두 사용할 수 있습니다. 
- 애플리케이션(클라이언트) ID
- 디렉터리(테넌트) ID
- 클라이언트 암호

필요한 경우 통합 팁 또는 사용자 지정 솔루션의 구성에서 이러한 값을 입력 하면 Azure 센티널을 대상으로 하는 Microsoft Graph tiIndicators API를 통해 위협 표시기가 전송 됩니다.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Azure 센티널에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용 하도록 설정

통합 프로세스의 마지막 단계는 Azure 센티널에서 **위협 인텔리전스 플랫폼** 데이터 커넥터를 사용 하도록 설정 하는 것입니다. Microsoft Graph tiIndicators API를 통해 TIP 또는 사용자 지정 솔루션에서 전송 된 위협 지표를 Azure 센티널로 가져오는 단계입니다. 이러한 지표는 조직의 모든 Azure 센티널 작업 영역에서 사용할 수 있습니다. 각 작업 영역에 대해 위협 인텔리전스 플랫폼 데이터 커넥터를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure 센티널** 서비스로 이동 합니다.

1. 팁 또는 사용자 지정 솔루션에서 보낸 위협 지표를 가져올 **작업 영역** 을 선택 합니다.

1. 메뉴에서 **데이터 커넥터** 를 선택 하 고 커넥터 갤러리에서 **위협 인텔리전스 플랫폼** 을 선택한 다음 **커넥터 페이지 열기** 단추를 클릭 합니다.

1. 앱 등록을 이미 완료 했 고 위협 지표를 보내도록 팁 또는 사용자 지정 솔루션을 구성 했으므로 왼쪽의 유일한 단계는 **연결** 단추를 클릭 하는 것입니다.

몇 분 내에 위협 표시기가이 Azure 센티널 작업 영역으로 이동 하기 시작 합니다.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>위협 인텔리전스-TAXII data connector를 사용 하 여 Azure 센티널에 위협 표시기 추가

위협 인텔리전스를 전송 하기 위해 가장 널리 채택 된 업계 표준은 [STIX 데이터 형식과 TAXII 프로토콜의 조합](https://oasis-open.github.io/cti-documentation/)입니다. 조직이 현재 STIX/TAXII 버전 (2.0 또는 2.1)을 지 원하는 솔루션에서 위협 지표를 가져오는 경우 **위협 인텔리전스-TAXII** data connector를 사용 하 여 위협 지표를 Azure 센티널로 가져올 수 있습니다. 위협 인텔리전스-TAXII 데이터 커넥터를 사용 하면 Azure 센티널에서 기본 제공 TAXII 클라이언트를 사용 하 여 TAXII 2.x 서버에서 위협 인텔리전스를 가져올 수 있습니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 가져오기 경로":::
 
TAXII 서버에서 Azure 센티널로 STIX 형식의 위협 지표를 가져오려면 다음 단계를 수행 합니다.

1. TAXII 서버 API 루트 및 컬렉션 ID 가져오기

1. Azure 센티널에서 위협 인텔리전스-TAXII 데이터 커넥터를 사용 하도록 설정

이제 이러한 각 단계에 대해 자세히 살펴보겠습니다.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>TAXII 서버 API 루트 및 컬렉션 ID 가져오기

TAXII 2.x 서버는 위협 인텔리전스의 컬렉션을 호스트 하는 Url 인 API 루트를 보급 합니다. TAXII 서버를 호스트 하는 위협 인텔리전스 공급자의 설명서 페이지를 통해 API 루트를 가장 자주 가져올 수 있습니다. 그러나 경우에 따라 검색 되는 유일한 정보는 검색 끝점 이라고 하는 URL입니다. 이 경우 검색 끝점을 사용 하 여 API 루트를 쉽게 찾을 수 있습니다. 작업 하려는 TAXII 서버 API 루트 및 컬렉션 Id를 이미 알고 있는 경우 **Azure 센티널에서 위협 인텔리전스-TAXII data connector를 사용 하도록 설정** 하 여 다음 섹션으로 건너뛸 수 있습니다.

Windows 및 대부분의 Linux 배포판에서 제공 되는 [TAXII 라는 간단한](https://en.wikipedia.org/wiki/CURL)명령줄 유틸리티를 사용 하 여 API 루트를 검색 하 고 검색 끝점 에서만 시작 하는 서버의 컬렉션을 검색 하는 방법에 대 한 실제 예를 살펴보겠습니다. 이 예에서는 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 서버의 검색 끝점을 사용 합니다.

1.  브라우저에서 [THREATSTREAM TAXII 2.0 서버 검색 끝점](https://limo.anomali.com/taxii) 으로 이동 하 여 API 루트를 검색 합니다. 사용자 및 암호를 사용 하 여 인증 `guest` 합니다.

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  이전 단계의 유틸리티 및 API 루트를 사용 https://limo.anomali.com/api/v1/taxii2/feeds/) 하 여 Api 루트에서 호스트 되는 컬렉션 id 목록을 검색 합니다.

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

이제 Anomali Limo에서 제공 하는 하나 이상의 TAXII 서버 컬렉션에 Azure 센티널을 연결 하는 데 필요한 모든 정보가 있습니다.

| **API 루트** (https://limo.anomali.com/api/v1/taxii2/feeds/) | 컬렉션 ID |
| ------------------------------------------------------------ | ------------: |
| **Phish 탱크**                                               | 107           |
| **Abuse.ch 랜 섬 웨어 Ip**                                  | 135           |
| **Abuse.ch 랜 섬 웨어 도메인**                              | 136           |
| **6실드 검색 Ip**                                     | 150           |
| **맬웨어 도메인 목록-Hotlist**                            | 200           |
| **Blutmagie**                                      | 209           |
| **새로운 위협 요소 C&C 서버**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **사이버 범죄**                                               |  41           |
| **새로운 위협-손상**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Azure 센티널에서 위협 인텔리전스-TAXII 데이터 커넥터를 사용 하도록 설정

TAXII 서버에서 Azure 센티널로 위협 지표를 가져오려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure 센티널** 서비스로 이동 합니다.

1. TAXII 서버에서 위협 지표를 가져올 **작업 영역** 을 선택 합니다.

1. 메뉴에서 **데이터 커넥터** 를 선택 하 고 커넥터 갤러리에서 **위협 인텔리전스-TAXII** 를 선택한 다음 **커넥터 페이지 열기** 단추를 클릭 합니다.

1. 이 TAXII 서버 컬렉션, **API 루트 URL**, **컬렉션 ID**, **사용자 이름** (필요한 경우) 및 **암호** (필요한 경우)에 대 한 **이름을** 입력 하 고 **추가** 단추를 클릭 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="TAXII 서버 구성":::
 
TAXII 서버에 대 한 연결이 성공적으로 설정 되었다는 확인 메시지가 표시 되 고, 동일한 서버나 다른 TAXII 서버에서 여러 컬렉션에 연결 하는 데 필요한 횟수 만큼 위의 단계 (4)를 반복할 수 있습니다.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Azure 센티널에서 위협 지표 보기

이제 위협 **인텔리전스 플랫폼** 및/또는 **위협 인텔리전스-TAXII** data connector를 사용 하 여 위협 지표를 azure 센티널로 가져왔습니다. 모든 azure 센티널 이벤트 데이터가 저장 되는 **로그** 의 **ThreatIntelligenceIndicator** 테이블에서 볼 수 있습니다. 이 테이블은 분석 및 통합 문서와 같은 다른 Azure 센티널 기능에서 수행 하는 쿼리의 기본입니다. **ThreatIntelligenceIndicator** 테이블에서 위협 지표를 찾고 확인 하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure 센티널** 서비스로 이동 합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용 하 여 위협 지표를 가져온 **작업 영역** 을 선택 합니다.

1. Azure 센티널 메뉴의 **일반** 섹션에서 **로그** 를 선택 합니다.

1. **ThreatIntelligenceIndicator** 테이블은 **Azure 센티널** 그룹 아래에 있습니다.

1. 테이블 이름 옆의 **데이터 미리 보기** 아이콘 (눈)을 선택 하 고 **쿼리 편집기에서 확인** 단추를 선택 하 여이 테이블의 레코드를 표시 하는 쿼리를 실행 합니다.

결과는 아래에 표시 된 샘플 위협 표시기와 유사 하 게 표시 됩니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="예제 쿼리 데이터":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Azure 센티널의 새로운 위협 인텔리전스 영역에서 위협 지표 관리

Azure 센티널 메뉴에서 액세스할 수 있는 새 **위협 인텔리전스** 영역을 사용 하 여 **로그** 쿼리를 작성 하지 않고도 가져온 위협 지표를 보고, 정렬 하 고, 필터링 하 고, 검색할 수도 있습니다. 또한이 새 영역에서는 Azure 센티널 인터페이스 내에서 직접 위협 지표를 만들고 표시기 태깅과 같은 일반적인 위협 인텔리전스 관리 작업을 수행 하 고 보안 조사와 관련 된 새 지표를 만들 수 있습니다.
간단한 그룹화 및 참조를 위해 새로운 위협 지표 및 태그 표시기를 만드는 가장 일반적인 작업 중 두 가지를 살펴보겠습니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure 센티널** 서비스로 이동 합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용 하 여 위협 지표를 가져온 **작업 영역** 을 선택 합니다.

1. Azure 센티널 메뉴의 위협 관리 섹션에서 **위협 인텔리전스** 를 선택 합니다.

1. 페이지의 상단 메뉴에서 **새로 추가** 단추를 선택 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="새 위협 지표 추가" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. 표시기 유형을 선택한 다음 **새 표시기** 패널에서 빨간색 별표 (*)로 표시 된 필수 필드를 완료 합니다.

1. **적용** 을 선택합니다. 표시기 표에 표시기가 추가 되 고 **로그** 의 ThreatIntelligenceIndicator 테이블에도 전송 됩니다.

위협 표시기를 태그를 지정 하 여 쉽게 찾을 수 있도록 그룹화 하는 쉬운 방법입니다. 일반적으로 특정 인시던트에 관련 된 표시기에 태그를 적용 하거나 특정 한 알려진 작업자 또는 잘 알려진 공격 캠페인의 위협을 나타내는 표시기를 적용할 수 있습니다. 위협 표시기를 개별적으로 태그를 지정할 수도 있고, 한 번에 여러 지표를 표시 하 고 태그를 지정할 수도 있습니다. 인시던트 ID를 사용 하 여 여러 지표에 태그를 지정 하는 예제는 다음과 같습니다. 태그 지정은 자유 형식 이므로 위협 지표 태그의 표준 명명 규칙을 만드는 것이 좋습니다. 각 표시기에 여러 태그를 적용할 수 있습니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="위협 표시기에 태그 적용" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>분석은 위협 표시기를 사용 하 여 잠재적 위협을 검색 합니다.

Azure 센티널에 위협 지표를 공급 했습니다. 이러한 항목을 보고 관리 하는 방법을 살펴보았습니다. 이제 사용자가 수행할 수 있는 작업을 확인 하세요. Azure 센티널과 같은 SIEM 솔루션에서 위협 표시기의 가장 중요 한 사용 사례는 전원 분석 규칙에 대 한 것입니다.  이러한 표시기 기반 규칙은 데이터 원본의 원시 이벤트를 위협 표시기와 비교 하 여 조직에서 보안 위협을 검색 합니다. Azure 센티널 **분석** 에서 일정에 따라 실행 되 고 보안 경고를 생성 하는 분석 규칙을 만듭니다. 규칙은 쿼리를 실행 하는 빈도를 결정 하는 구성, 보안 경고를 생성 하는 쿼리 결과의 종류 및 경고가 생성 될 때 트리거할 자동화 된 응답을 결정 하는 구성에 따라 결정 됩니다.

언제 든 지 새 분석 규칙을 처음부터 새로 만들 수 있지만, Azure 센티널은 Microsoft 보안 엔지니어가 만든 기본 제공 규칙 템플릿 집합을 제공 하며,이를 그대로 사용 하거나 필요에 맞게 수정할 수 있습니다. 위협 지표를 사용 하는 규칙 템플릿은 모두 "**TI map**..."로 시작 하는 것 이므로 쉽게 식별할 수 있습니다. 이러한 모든 규칙 템플릿은 유사 하 게 작동 하며, 위협 표시기의 유형 (도메인, 전자 메일, 파일 해시, IP 주소 또는 URL)과 일치 시킬 이벤트 유형을 유일 하 게 다릅니다. 각 템플릿에는 규칙이 작동 하는 데 필요한 데이터 원본이 나열 되어 있으므로 Azure 센티널에서 필요한 이벤트를 이미 가져왔는지 확인할 수 있습니다.

이러한 규칙 템플릿 중 하나를 살펴보고 Azure 센티널로 가져온 위협 지표를 사용 하 여 보안 경고를 생성 하는 규칙을 사용 하도록 설정 하 고 구성 하는 방법을 살펴보겠습니다. 이 예에서는 **TI MAP IP entity To AzureActivity** 라는 규칙 템플릿을 사용 합니다. 이 규칙은 모든 Azure 활동 이벤트와 IP 주소 유형 위협 표시기를 일치 시킵니다. 일치 항목이 발견 되 면 **경고가** 생성 되 고 보안 운영 팀에서 조사 하기 위한 해당 **인시던트가** 생성 됩니다. 이 분석 규칙은 위협 지표를 가져오는 **위협 인텔리전스** 데이터 커넥터 중 하나 또는 둘 다를 사용 하도록 설정 하 고 azure **활동** 데이터 커넥터 (azure 구독 수준 이벤트를 가져오기 위해)를 사용 하도록 설정한 경우에만 정상적으로 작동 합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure 센티널** 서비스로 이동 합니다.

1. **Azure 활동** 데이터 커넥터를 사용 하 여 **위협 인텔리전스** 데이터 커넥터 및 azure 활동 데이터를 사용 하 여 위협 지표를 가져온 **작업 영역** 을 선택 합니다.

1. Azure 센티널 메뉴의 **구성** 섹션에서 **분석** 을 선택 합니다.

1. 사용 가능한 분석 규칙 템플릿 목록을 보려면 **규칙 템플릿** 탭을 선택 합니다.

1. **TI IP 엔터티를 AzureActivity로 매핑** 이라는 규칙으로 이동 하 여 아래와 같이 필요한 모든 데이터 원본을 연결 했는지 확인 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="필요한 데이터 원본":::

1. 이 규칙을 선택 하 고 **규칙 만들기** 단추를 선택 합니다. 그러면 규칙을 구성 하는 마법사가 열립니다. 여기에서 설정을 완료 하 고 **다음: 규칙 논리 설정 >** 단추를 선택 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="분석 규칙 만들기":::

1. 마법사의 규칙 논리 부분에는 다음이 포함 됩니다.
    - 규칙에 사용 될 쿼리입니다.

    - 계정, IP 주소 및 Url과 같은 엔터티를 인식 하는 방법을 Azure 센티널에 알리는 엔터티 매핑은이 규칙에 의해 생성 된 모든 보안 경고의 데이터로 작업 하는 **방법을 이해** **하 고 있습니다** .

    - 이 규칙을 실행할 일정입니다.

    - 보안 경고가 생성 되기 전에 필요한 쿼리 결과의 수입니다.

    템플릿의 기본 설정은 다음과 같습니다.
    - 한 시간에 한 번 실행 합니다.

    - **ThreatIntelligenceIndicator** 테이블의 모든 ip 주소 위협 표시기를 **azureactivity** 테이블의 마지막 1 시간 동안 검색 된 ip 주소와 일치 시킵니다.

    - 쿼리 결과가 0 보다 큰 경우 (즉, 일치 하는 항목이 있는 경우) 보안 경고를 생성 합니다.

기본 설정을 그대로 두거나 요구 사항에 맞게 변경할 수 있습니다. 작업이 완료 되 면 **다음: 자동화 된 응답 >** 단추를 선택 합니다.

1. 이 마법사의 단계에서는이 분석 규칙에서 보안 경고가 생성 될 때 트리거하는 모든 automation을 구성할 수 있습니다. Azure 센티널의 자동화는 Azure Logic Apps에서 제공 하는 플레이 **북** 을 사용 하 여 수행 됩니다. 자세히 알아보려면이 [자습서: Azure 센티널에서 자동화 된 위협 응답 설정](./tutorial-respond-threats-playbook.md)을 참조 하세요. 이 예에서는 **다음: >검토** 단추를 선택 하 여 계속 합니다.

1. 이 마지막 단계에서는 규칙의 설정에 대 한 유효성을 검사 합니다. 규칙을 사용 하도록 설정할 준비가 되 면 **만들기** 단추를 선택 하면 작업이 완료 된 것입니다.

분석 규칙을 사용 하도록 설정 했으므로 Azure 센티널의 **분석** 섹션의 **활성 규칙** 탭에서 사용 규칙을 찾을 수 있습니다. 활성 규칙을 편집, 사용, 사용 안 함, 복제 또는 삭제할 수 있습니다. 새 규칙은 활성화 될 때 즉시 실행 되며, 그 다음에는 정의 된 일정에 따라 실행 됩니다.

기본 설정에 따라 규칙이 일정에 따라 실행 될 때마다 검색 된 결과에 따라 보안 경고가 생성 됩니다. Azure 센티널의 보안 경고 **는 azure 센티널 그룹 아래의** **securityalert** 테이블에 있는 azure 센티널의 **로그** 섹션에서 볼 수 있습니다.

Azure 센티널에서 분석 규칙에서 생성 된 경고는 Azure 센티널 메뉴의 **위협 관리** 아래에 있는 **인시던트에** 있는 보안 인시던트도 생성 합니다. 인시던트는 보안 운영 팀에서 적절 한 응답 작업을 확인 하 고 조사 하는 것입니다. 이 [자습서에서 Azure 센티널로 인시던트 조사](./tutorial-investigate-cases.md)에서 자세한 정보를 찾을 수 있습니다.

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>통합 문서는 위협 인텔리전스에 대 한 정보를 제공 합니다.

마지막으로 azure 센티널 통합 문서를 사용 하 여 Azure 센티널에서 위협 인텔리전스에 대 한 주요 정보를 시각화할 수 있으며, 비즈니스 요구에 따라 통합 문서를 쉽게 사용자 지정할 수 있습니다.
Azure 센티널에 제공 된 위협 인텔리전스 통합 문서를 찾는 방법을 살펴보겠습니다. 또한 통합 문서를 편집 하 여 사용자 지정 하는 방법을 보여 드리겠습니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **Azure 센티널** 서비스로 이동 합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용 하 여 위협 지표를 가져온 **작업 영역** 을 선택 합니다.

1. Azure 센티널 메뉴의 **위협 관리** 섹션에서 **통합 문서** 를 선택 합니다.

1. **위협 인텔리전스** 라는 통합 문서로 이동 하 여 아래와 같이 **ThreatIntelligenceIndicator** 테이블에 데이터가 있는지 확인 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="데이터 확인":::
 
1. **저장** 단추를 선택 하 고 통합 문서를 저장할 Azure 위치를 선택 합니다. 이 단계는 어떤 방식으로든 통합 문서를 수정 하 고 변경 내용을 저장 하려는 경우에 필요 합니다.

1. 이제 **저장 된 통합 문서 보기** 단추를 선택 하 여 보기 및 편집을 위해 통합 문서를 엽니다.

1. 이제 템플릿에서 제공 하는 기본 차트가 표시 됩니다. 이제 차트 중 하나를 변경 하겠습니다. 페이지 맨 위에 있는 **편집** 단추를 선택 하 여 통합 문서에 대 한 편집 모드로 전환 합니다.

1. 위협 유형에 따라 위협 지표의 새 차트를 추가 해 보겠습니다. 페이지 아래쪽으로 스크롤하고 쿼리 추가를 선택 합니다.

1. **Log Analytics 작업 영역 로그 쿼리** 입력란에 다음 텍스트를 추가 합니다.
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. **시각화** 드롭다운에서 **가로 막대형 차트** 를 선택 합니다.

1. **편집 완료** 단추를 선택 합니다. 통합 문서에 대 한 새 차트를 만들었습니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="가로 막대형 차트":::

통합 문서는 Azure 센티널의 모든 측면에 대 한 정보를 제공 하는 강력한 대화형 대시보드를 제공 합니다. 통합 문서를 사용 하면 많은 작업을 수행할 수 있으며, 제공 된 템플릿이 좋은 출발점 이지만, 이러한 템플릿을 사용 하 여 사용자 지정 하거나, 다양 한 데이터 소스를 결합 하는 새로운 대시보드를 만들어 데이터를 고유한 방식으로 시각화할 수 있습니다. Azure 센티널 통합 문서는 Azure Monitor 통합 문서를 기반으로 하기 때문에 이미 광범위 한 설명서를 사용할 수 있으며 더 많은 템플릿이 있습니다. [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서를 만드는](../azure-monitor/platform/workbooks-overview.md)방법에 대 한이 문서를 시작 하는 것이 좋습니다. 

또한 GitHub에는 추가 템플릿을 다운로드 하 고 사용자 고유의 템플릿을 제공할 수 있는 다양 한 [Azure Monitor 통합 문서](https://github.com/microsoft/Application-Insights-Workbooks) 커뮤니티가 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널의 위협 인텔리전스 기능 및 새로운 위협 인텔리전스 블레이드에 대해 알아보았습니다. Azure 센티널의 위협 인텔리전스 기능을 사용 하는 방법에 대 한 실질적인 지침은 다음 문서를 참조 하세요.

- Azure 센티널에 [위협 인텔리전스 데이터를 연결](./connect-threat-intelligence.md) 합니다.
- Azure 센티널에서 [기본 제공](./tutorial-detect-threats-built-in.md) 또는 [사용자 지정](./tutorial-detect-threats-custom.md) 경고를 만들고 인시던트를 [조사](./tutorial-investigate-cases.md) 합니다.