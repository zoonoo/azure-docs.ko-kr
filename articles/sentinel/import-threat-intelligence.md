---
title: Azure Sentinel로 위협 인텔리전스 가져오기 | Microsoft Docs
description: Azure Sentinel에서 위협 인텔리전스 피드를 사용하여 데이터를 분석하고, 위협을 탐지하고, 경고 및 인시던트를 생성합니다. 통합 문서를 사용하여 위협 인텔리전스 정보를 시각화합니다.
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
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574998"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Azure Sentinel로 위협 인텔리전스 가져오기

## <a name="introduction-to-threat-intelligence"></a>위협 인텔리전스 소개

CTI(사이버 위협 인텔리전스)는 시스템 및 사용자에게 알려진 기존의 또는 잠재적인 위협을 설명하는 정보입니다. 이 유형의 정보는 특정 위협 행위자의 동기, 인프라, 기술을 상세히 기술한 보고서에서부터 사이버 위협과 관련된 IP 주소, 도메인, 파일 해시에 대한 특정 관찰에 이르기까지 다양한 형태를 취합니다. 조직은 CTI를 사용하여 비정상적인 활동에 대한 중요한 컨텍스트를 제공하여 보안 담당자가 직원 및 자산을 신속하게 보호할 수 있도록 합니다. CTI는 오픈 소스 데이터 피드, 위협 인텔리전스 공유 커뮤니티, 상용 인텔리전스 피드, 조직 내 보안 조사 과정에서 수집된 로컬 인텔리전스 등의 여러 위치에서 원본으로 사용할 수 있습니다.

Azure Sentinel과 같은 SIEM(보안 정보 및 이벤트 관리) 솔루션 내에서 가장 많이 사용되는 CTI 형태는 침해 지표 또는 IoC라고 하는 위협 지표입니다. 위협 지표는 URL, 파일 해시 또는 IP 주소 등을 관찰하여 피싱, 봇네트, 맬웨어 등의 알려진 위협 활동과 이들을 연동하는 데이터입니다. 해당 위협 인텔리전스는 조직에 대한 잠재적 위협을 보호하고 탐지하기 위해 대규모의 보안 제품 및 자동화에 적용될 수 있기 때문에 보통 전략적 위협 인텔리전스라고 합니다. Azure Sentinel에서는 위협 지표를 사용하여 환경에서 관찰된 악의적인 활동을 탐지하고 보안 분석가에게 컨텍스트를 제공하여 대응 결정을 전파할 수 있습니다.

다음 작업을 통해 TI(위협 인텔리전스)를 Azure Sentinel로 통합할 수 있습니다.

- 다양한 TI 플랫폼에 대한 **데이터 커넥터** 를 사용하여 Azure Sentinel로 [위협 인텔리전스를 가져옵니다](./connect-threat-intelligence.md).
- **로그** 에서 가져온 위협 인텔리전스와 Azure Sentinel의 새로운 **위협 인텔리전스** 영역을 확인하고 관리합니다.
- 기본 제공 **분석** 규칙 템플릿을 사용하여 가져온 위협 인텔리전스를 통해 보안 경고 및 인시던트를 생성합니다.
- **위협 인텔리전스 통합 문서** 를 사용하여 Azure Sentinel에서 위협 인텔리전스에 대한 중요 정보를 시각화합니다.

또한 위협 인텔리전스는 **헌팅** 및 **Notebook** 같은 다른 Azure Sentinel 환경 내에서 유용한 컨텍스트를 제공하며, 이 문서에서 다루지 않지만 Notebook 내에서 CTI 사용에 대해 설명하는 [Azure Sentinel의 Jupyter Notebook에 대한 Ian Hellen의 이 유용한 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)에서 이러한 환경을 다룹니다.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>위협 인텔리전스용 Azure Sentinel 데이터 커넥터

Azure Sentinel의 다른 모든 이벤트 데이터와 마찬가지로 위협 지표는 데이터 커넥터를 사용하여 가져옵니다. Azure Sentinel에서는 두 가지 데이터 커넥터 **위협 인텔리전스 - TAXII** 및 **위협 인텔리전스 플랫폼** 이 위협 지표 전용으로 제공됩니다. 조직에서 위협 지표의 원본으로 사용하는 위치에 따라 데이터 커넥터를 단독으로 사용하거나 두 커넥터를 함께 사용할 수 있습니다. 각 데이터 커넥터에 대해 알아보겠습니다.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>위협 인텔리전스 플랫폼 데이터 커넥터를 사용하여 Azure Sentinel에 위협 지표 추가

많은 조직에서는 TIP(위협 인텔리전스 플랫폼) 솔루션을 활용하여 다양한 원본에서 위협 지표 피드를 집계하고, 플랫폼 내에서 데이터를 선별한 다음, 네트워크 디바이스, 지능형 위협 방지 솔루션 또는 SIEM(예: Azure Sentinel) 같은 다양한 보안 솔루션에 적용할 위협 지표를 선택합니다. 조직에서 통합 TIP 솔루션(예: MISP, Anomali ThreatStream, ThreatConnect, EclecticIQ Platform, ThreatQ Threat Intelligence Platform 또는 Palo Alto Networks의 MineMeld)을 활용하는 경우 **위협 인텔리전스 플랫폼 데이터 커넥터** 를 사용하여 Azure Sentinel로 위협 지표를 가져올 수 있습니다. 이 커넥터는 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator)와 함께 작동하여 이 작업을 수행하므로, 사용자 지정 위협 인텔리전스 플랫폼에서도 이 커넥터를 사용하여 tiIndicators API를 통해 Azure Sentinel(또는 Defender ATP 같은 다른 Microsoft 보안 솔루션)로 지표를 보낼 수 있습니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="위협 인텔리전스 가져오기 경로":::

다음 단계에 따라 통합 TIP 또는 사용자 지정 위협 인텔리전스 솔루션에서 Azure Sentinel로 위협 지표를 가져옵니다.

1. Azure Active Directory에서 애플리케이션 ID 및 클라이언트 비밀 가져오기

1. TIP 솔루션 또는 사용자 지정 애플리케이션에 이 정보를 입력

1. Azure Sentinel에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용하도록 설정

이러한 각 단계에 대한 자세한 내용은 다음과 같습니다.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 ID 및 클라이언트 비밀 가져오기

TIP을 사용하든 사용자 지정 솔루션을 사용하든 관계없이 tiIndicators API에 연결하고 위협 지표를 보내려면 몇 가지 기본 정보가 필요합니다. 다음 세 가지 정보를 알아야 합니다.
- 애플리케이션(클라이언트) ID
- 디렉터리(테넌트) ID
- 클라이언트 암호

이 정보는 항상 **앱 등록** 이라는 프로세스를 통해 Azure Active Directory에서 제공됩니다. 이 프로세스에는 다음 세 단계가 포함됩니다.
- Azure Active Directory에 앱 등록
- 앱이 Microsoft Graph tiIndicators API에 연결하고 위협 지표를 보내기 위해 필요한 권한을 지정합니다.
- 조직에서 동의를 받아 이 애플리케이션에 이러한 권한을 부여합니다.  

**Azure Active Directory에 애플리케이션 등록**

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Active Directory** 서비스로 이동합니다.

1. 메뉴에서 **앱 등록** 을 선택하고 **새 등록** 을 선택합니다.

1. 애플리케이션 등록의 이름을 선택하고, **단일 테넌트** 라디오 단추를 선택하고, **등록** 을 선택합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="애플리케이션 등록":::

1. 결과 화면에서 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 값을 복사합니다. 이들은 나중에 Azure Sentinel에 위협 지표를 보내도록 TIP 또는 사용자 지정 솔루션을 구성하는 데 필요한 처음 두 가지 정보입니다.

**애플리케이션에 필요한 권한 지정**

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Active Directory** 서비스로 이동합니다.

1. 메뉴에서 **앱 등록** 을 선택하고 새로 등록한 앱을 선택합니다.

1. 메뉴에서 **API 권한** 을 선택하고 **권한 추가** 단추를 클릭합니다.

1. **API 선택** 페이지에서 **Microsoft Graph** 를 선택하여 Microsoft Graph 권한 목록 중에서 선택합니다.

1. **애플리케이션에 어떤 유형의 권한이 필요한가요?** 를 묻는 메시지가 표시되면 **애플리케이션 권한** 을 선택합니다. 이는 앱 ID 및 앱 비밀(API 키)을 사용하여 인증하는 애플리케이션에서 사용하는 유형의 권한입니다.

1. **ThreatIndicators.ReadWrite.OwnedBy** 를 선택하고 **권한 추가** 를 선택하여 앱의 권한 목록에 이 권한을 추가합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="권한 지정":::

**조직에서 동의를 받아 이 애플리케이션에 이러한 권한을 부여**

1. 동의를 부여하려면 앱의 API 권한 페이지에서 **테넌트에 대한 관리자 동의 부여** 단추를 선택할 수 있는 Azure Active Directory 전역 관리자가 필요합니다. 계정에 대한 전역 관리자 역할이 없으면 이 단추를 사용할 수 없으며 조직의 전역 관리자에게 이 단계를 수행하도록 요청해야 합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="동의 권한 부여":::

1. 앱에 동의가 부여되면 **상태** 아래에 녹색 확인 표시가 표시됩니다.
 
이제 앱이 등록되고 권한이 부여되었으므로 목록의 마지막 항목인 앱의 클라이언트 비밀을 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Active Directory** 서비스로 이동합니다.

1. 메뉴에서 **앱 등록** 을 선택하고 새로 등록한 앱을 선택합니다.

1. 메뉴에서 **인증서 및 비밀** 을 선택하고 **새 클라이언트 비밀** 단추를 클릭하여 앱의 암호(API 키)를 가져옵니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="클라이언트 비밀 가져오기":::

1. **추가** 단추를 클릭하고 반드시 **클라이언트 비밀을 복사** 합니다. 이 페이지를 벗어나는 경우 이 암호를 다시 검색할 수 없습니다. TIP 또는 사용자 지정 솔루션을 구성할 때 이 값이 필요합니다.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>TIP 솔루션 또는 사용자 지정 애플리케이션에 이 정보를 입력

이제 Azure Sentinel에 위협 지표를 보내도록 TIP 또는 사용자 지정 솔루션을 구성하는 데 필요한 세 가지 정보가 모두 있습니다. 
- 애플리케이션(클라이언트) ID
- 디렉터리(테넌트) ID
- 클라이언트 암호

필요한 경우 통합 TIP 또는 사용자 지정 솔루션의 구성에 이러한 값을 입력하면 Azure Sentinel을 대상으로 하는 Microsoft Graph tiIndicators API를 통해 위협 지표가 전송됩니다.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Azure Sentinel에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용하도록 설정

통합 프로세스의 마지막 단계는 Azure Sentinel에서 **위협 인텔리전스 플랫폼** 데이터 커넥터를 사용하도록 설정하는 것입니다. 이는 TIP 또는 사용자 지정 솔루션에서 Microsoft Graph tiIndicators API를 통해 보낸 위협 지표를 Azure Sentinel로 가져오는 단계입니다. 이러한 지표는 조직의 모든 Azure Sentinel 작업 영역에서 사용할 수 있습니다. 각 작업 영역에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. TIP 또는 사용자 지정 솔루션에서 보낸 위협 지표를 가져올 **작업 영역** 을 선택합니다.

1. 메뉴에서 **데이터 커넥터** 를 선택하고 커넥터 갤러리에서 **위협 인텔리전스 플랫폼** 을 선택한 다음 **커넥터 페이지 열기** 단추를 클릭합니다.

1. 앱 등록을 이미 완료했고 위협 지표를 보내도록 TIP 또는 사용자 지정 솔루션을 구성했으므로 이제 남은 단계는 **연결** 단추를 클릭하는 것입니다.

몇 분 후에 위협 지표가 이 Azure Sentinel 작업 영역으로 이동하기 시작합니다.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>위협 인텔리전스 - TAXII 데이터 커넥터를 사용하여 Azure Sentinel에 위협 지표 추가

위협 인텔리전스 전송을 위한 가장 널리 채택된 업계 표준은 [STIX 데이터 형식과 TAXII 프로토콜의 조합](https://oasis-open.github.io/cti-documentation/)입니다. 조직이 현재 STIX/TAXII 버전(2.0 또는 2.1)을 지원하는 솔루션에서 위협 지표를 가져오는 경우 **위협 인텔리전스 - TAXII** 데이터 커넥터를 사용하여 Azure Sentinel로 위협 지표를 가져올 수 있습니다. 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하면 Azure Sentinel에서 기본 제공 TAXII 클라이언트를 사용하여 TAXII 2.x 서버로부터 위협 인텔리전스를 가져올 수 있습니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 가져오기 경로":::
 
TAXII 서버에서 Azure Sentinel로 STIX 형식 위협 지표를 가져오려면 다음 단계를 수행합니다.

1. TAXII 서버 API 루트 및 컬렉션 ID 가져오기

1. Azure Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하도록 설정

이제 이러한 각 단계에 대해 자세히 살펴보겠습니다.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>TAXII 서버 API 루트 및 컬렉션 ID 가져오기

TAXII 2.x 서버는 위협 인텔리전스의 컬렉션을 호스트하는 URL인 API 루트를 보급합니다. 대부분의 경우 API 루트는 TAXII 서버를 호스트하는 위협 인텔리전스 공급자의 설명서 페이지를 통해 가져올 수 있습니다. 그러나 경우에 따라 검색 엔드포인트라고 하는 URL이 유일하게 보급되는 정보입니다. 이 경우 검색 엔드포인트를 사용하여 API 루트를 쉽게 찾을 수 있습니다. 사용하려는 TAXII 서버 API 루트 및 컬렉션 ID를 이미 알고 있는 경우 다음 섹션 **Azure Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하도록 설정** 으로 건너뛸 수 있습니다.

Windows 및 대부분의 Linux 배포판에서 제공되는 [cURL](https://en.wikipedia.org/wiki/CURL)이라는 간단한 명령줄 유틸리티를 사용하여 API 루트를 검색하고 검색 엔드포인트에서만 시작하는 TAXII 서버의 컬렉션을 검색하는 실제 예제를 살펴보겠습니다. 이 예제에서는 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 서버의 검색 엔드포인트를 사용합니다.

1.  브라우저에서 [ThreatStream TAXII 2.0 서버 검색 엔드포인트](https://limo.anomali.com/taxii)로 이동하여 API 루트를 검색합니다. 사용자 및 암호 `guest`를 사용하여 인증합니다.

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

2.  cURL 유틸리티 및 이전 단계의 API 루트(https://limo.anomali.com/api/v1/taxii2/feeds/) )를 사용하여 API 루트에서 호스트되는 컬렉션 ID 목록을 검색합니다.

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

이제 Anomali Limo에서 제공하는 하나 이상의 TAXII 서버 컬렉션에 Azure Sentinel을 연결하는 데 필요한 정보가 모두 있습니다.

| **API 루트**(https://limo.anomali.com/api/v1/taxii2/feeds/) | 컬렉션 ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware Domains**                              | 136           |
| **DShield Scanning IPs**                                     | 150           |
| **Malware Domain List - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Emerging Threats C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats - Compromised**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Azure Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하도록 설정

TAXII 서버에서 Azure Sentinel로 위협 지표를 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. TAXII 서버에서 위협 지표를 가져올 **작업 영역** 을 선택합니다.

1. 메뉴에서 **데이터 커넥터** 를 선택하고 커넥터 갤러리에서 **위협 인텔리전스 - TAXII** 를 선택한 다음 **커넥터 페이지 열기** 단추를 클릭합니다.

1. 이 TAXII 서버 컬렉션의 **이름**, **API 루트 URL**, **컬렉션 ID**, **사용자 이름**(필요한 경우) 및 **암호**(필요한 경우)를 입력하고 **추가** 단추를 클릭합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="TAXII 서버 구성":::
 
TAXII 서버에 대한 연결이 성공적으로 설정되었다는 확인 메시지가 표시되고, 동일한 서버 또는 다른 TAXII 서버에서 여러 컬렉션에 연결하는 데 필요한 횟수만큼 위의 단계(4)를 반복할 수 있습니다.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Azure Sentinel에서 위협 지표 보기

이제 **위협 인텔리전스 플랫폼** 및/또는 **위협 인텔리전스 - TAXII** 데이터 커넥터를 사용하여 위협 지표를 Azure Sentinel로 가져왔으므로 모든 Azure Sentinel 이벤트 데이터가 저장되는 **로그** 의 **ThreatIntelligenceIndicator** 테이블에서 볼 수 있습니다. 해당 테이블은 분석 및 통합 문서와 같은 다른 Azure Sentinel 기능을 통해 수행하는 쿼리의 기초가 됩니다. **ThreatIntelligenceIndicator** 테이블에서 위협 지표를 찾고 확인하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용하여 위협 지표를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 **일반** 섹션에서 **로그** 를 선택합니다.

1. **ThreatIntelligenceIndicator** 테이블은 **Azure Sentinel** 그룹 아래에 있습니다.

1. 테이블 이름 옆의 **데이터 미리 보기** 아이콘(눈)을 선택하고 **쿼리 편집기에서 확인** 단추를 선택하여 이 테이블의 레코드를 표시하는 쿼리를 실행합니다.

결과는 아래에 표시된 샘플 위협 지표와 비슷합니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="샘플 쿼리 데이터":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Azure Sentinel의 새로운 위협 인텔리전스 영역에서 위협 지표 관리

Azure Sentinel 메뉴에서 액세스할 수 있는 새로운 **위협 인텔리전스** 영역을 사용하여 **로그** 쿼리를 작성하지 않고도 가져온 위협 지표를 확인, 정렬, 필터링, 검색할 수 있습니다. 또한 이 새로운 영역에서는 Azure Sentinel 인터페이스 내에 직접 위협 지표를 작성하고 지표 태그 지정 및 보안 조사와 관련된 신규 지표 작성과 같은 일반적인 위협 인텔리전스 관리 작업을 수행할 수 있습니다.
간단한 그룹화 및 참조를 위해 신규 위협 지표 및 태그 지표를 만드는 가장 일반적인 작업 중 두 가지를 살펴봅니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용하여 위협 지표를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 위협 관리 섹션에서 **위협 인텔리전스** 를 선택합니다.

1. 페이지의 상단 메뉴에서 **신규 추가** 버튼을 선택합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="새 위협 지표 추가" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. 지표 유형을 선택한 다음 **신규 지표** 패널에서 빨간색 별표(*)로 표시된 필수 필드를 작성합니다.

1. **적용** 을 선택합니다. 지표가 지표 그리드에 추가되고 **로그** 의 ThreatIntelligenceIndicator 테이블에도 보내집니다.

위협 지표에 태그를 지정하면 지표를 함께 그룹화하여 손쉽게 찾을 수 있습니다. 일반적으로, 알려진 행위자 또는 잘 알려진 공격 캠페인의 위협을 나타내는 특정 인시던트 또는 지표와 관련된 지표에 태그를 적용할 수 있습니다. 위협 지표에 각각 태그를 지정할 수도 있고 여러 개를 선택하여 한 번에 모두 태그를 지정할 수도 있습니다. 인시던트 ID를 사용하여 여러 지표에 태그를 지정하는 예제는 다음과 같습니다. 태그 지정은 특정한 형식이 없기 때문에, 위협 지표 태그에 대한 표준 명명 규칙을 만드는 것이 좋습니다. 각 지표에 여러 태그를 적용할 수 있습니다.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="위협 지표에 태그 적용" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>분석에서는 위협 지표를 사용하여 잠재적 위협을 탐지합니다.

Azure Sentinel에 위협 지표를 공급했고 이러한 지표를 보고 관리하는 방법을 알아보았습니다. 이제 사용자가 수행할 수 있는 작업을 확인하세요. Azure Sentinel과 같은 SIEM 솔루션에서 위협 지표의 가장 중요한 사용 사례는 분석 규칙을 제공하는 것입니다.  이러한 지표 기반 규칙은 데이터 원본의 원시 이벤트를 위협 지표와 비교하여 조직에서 보안 위협을 탐지합니다. Azure Sentinel **분석** 에서 일정에 따라 실행되고 보안 경고를 생성하는 분석 규칙을 만듭니다. 이러한 규칙은 쿼리를 실행하는 빈도, 보안 경고를 생성하는 쿼리 결과의 종류, 경고가 생성될 때 트리거할 자동화된 응답을 결정하는 구성과 함께 쿼리를 통해 제공됩니다.

언제든지 새 분석 규칙을 처음부터 만들 수 있지만, Azure Sentinel은 Microsoft 보안 엔지니어가 만든 기본 제공 규칙 템플릿 집합을 제공하며, 이를 그대로 사용하거나 필요에 맞게 수정할 수 있습니다. 위협 지표를 사용하는 규칙 템플릿은 모두 '**TI map**...'으로 시작하므로 쉽게 식별할 수 있습니다. 이러한 모든 규칙 템플릿은 사용하는 위협 지표 유형(도메인, 이메일, 파일 해시, IP 주소 또는 URL) 및 일치시킬 이벤트 유형을 제외하고 유사하게 작동합니다. 각 템플릿에는 규칙이 작동하는 데 필요한 데이터 원본이 나열되어 있으므로 Azure Sentinel에서 필요한 이벤트를 이미 가져왔는지 확인할 수 있습니다.

이러한 규칙 템플릿 중 하나를 살펴보고 Azure Sentinel로 가져온 위협 지표를 사용하여 보안 경고를 생성하는 규칙을 사용하도록 설정하고 구성하는 방법을 살펴보겠습니다. 이 예제에서는 **TI map IP entity to AzureActivity** 라는 규칙 템플릿을 사용합니다. 이 규칙은 모든 IP 주소 유형 위협 지표를 모든 Azure 활동 이벤트와 일치시킵니다. 일치 항목이 발견되면 **경고** 가 생성되고 보안 운영 팀에서 조사하기 위한 해당 **인시던트** 도 생성됩니다. 이 분석 규칙은 위협 지표를 가져오기 위해 **위협 인텔리전스** 데이터 커넥터 중 하나 또는 둘 다 사용하도록 설정하고 Azure 구독 수준 이벤트를 가져오기 위해 **Azure 활동** 데이터 커넥터를 사용하도록 설정한 경우에만 정상적으로 작동합니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. **위협 인텔리전스** 데이터 커넥터를 위협 지표를 가져오고 **Azure 활동** 데이터 커넥터를 사용하여 Azure 활동 데이터를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 **구성** 섹션에서 **분석** 을 선택합니다.

1. **규칙 템플릿** 탭을 선택하여 사용 가능한 분석 규칙 템플릿의 목록을 확인합니다.

1. **TI map IP entity to AzureActivity** 라는 규칙으로 이동하여 아래와 같이 필요한 데이터 원본을 모두 연결했는지 확인합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="필요한 데이터 원본":::

1. 이 규칙을 선택하고 **규칙 만들기** 단추를 선택합니다. 그러면 규칙을 구성하는 마법사가 열립니다. 여기에서 설정을 완료하고 **다음: 규칙 논리 설정 >** 단추를 선택합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="분석 규칙 만들기":::

1. 마법사의 규칙 논리 부분에는 다음이 포함됩니다.
    - 규칙에 사용될 쿼리

    - **인시던트** 및 **조사** 가 이 규칙에 의해 생성된 모든 보안 경고의 데이터로 작업하는 방법을 이해하도록 Azure Sentinel에게 계정, IP 주소, URL과 같은 엔터티를 인식하는 방법을 알려주는 엔터티 매핑

    - 이 규칙을 실행할 일정

    - 보안 경고가 생성되기 전에 필요한 쿼리 결과의 수

    템플릿의 기본 설정은 다음과 같습니다.
    - 한 시간에 한 번 실행합니다.

    - **ThreatIntelligenceIndicator** 테이블의 모든 IP 주소 위협 지표를 **AzureActivity** 테이블의 마지막 1시간 동안 이벤트에서 검색된 IP 주소와 일치시킵니다.

    - 쿼리 결과가 0보다 큰 경우(즉, 일치 항목이 있는 경우) 보안 경고를 생성합니다.

기본 설정을 그대로 두거나 요구 사항에 맞게 변경할 수 있습니다. 완료되면 **다음: 자동화된 응답 >** 단추를 선택합니다.

1. 이 마법사 단계에서는 이 분석 규칙에서 보안 경고가 생성될 때 트리거하는 모든 자동화를 구성할 수 있습니다. Azure Sentinel의 자동화는 Azure Logic Apps에서 제공하는 **플레이북** 을 사용하여 수행됩니다. 자세히 알아보려면 [자습서: Azure Sentinel에서 자동화된 위협 응답 설정](./tutorial-respond-threats-playbook.md)을 참조하세요. 이 예제에서는 단지 **다음: 검토 >** 단추를 선택하여 계속합니다.

1. 이 마지막 단계에서는 규칙의 설정에 대한 유효성을 검사합니다. 규칙을 사용하도록 설정할 준비가 되면 **만들기** 단추를 선택하면 작업이 완료된 것입니다.

분석 규칙을 사용하도록 설정했으므로 Azure Sentinel의 **분석** 섹션의 **활성 규칙** 탭에서 설정된 규칙을 찾을 수 있습니다. 여기에서 활성 규칙을 편집, 활성화, 비활성화, 복제 또는 삭제할 수 있습니다. 새 규칙은 활성화되는 즉시 실행되며, 그 다음에는 정의된 일정에 따라 실행됩니다.

규칙이 기본 설정의 일정에 따라 실행될 때마다 검색된 결과가 있으면 보안 경고가 생성됩니다. Azure Sentinel의 보안 경고는 **Azure Sentinel** 그룹 아래 **SecurityAlert** 테이블에 있는 Azure Sentinel의 **로그** 섹션에서 볼 수 있습니다.

Azure Sentinel에서 분석 규칙에서 생성된 경고는 Azure Sentinel 메뉴의 **위협 관리** 아래에 있는 **인시던트** 에서 확인할 수 있는 보안 인시던트도 생성합니다. 인시던트는 보안 운영 팀이 적절한 대응 조치를 결정하기 위해 선별 및 조사하는 것입니다. 이 [자습서: Azure Sentinel을 사용하여 인시던트 조사](./tutorial-investigate-cases.md)에서 자세한 정보를 찾을 수 있습니다.

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>통합 문서는 위협 인텔리전스에 대한 정보를 제공합니다.

마지막으로 Azure Sentinel에서 Azure Sentinel 통합 문서를 사용하여 위협 인텔리전스에 대한 주요 정보를 시각화할 수 있으며, 통합 문서를 비즈니스 요구 사항에 따라 쉽게 사용자 지정할 수 있습니다.
Azure Sentinel에서 제공된 위협 인텔리전스 통합 문서를 찾는 방법을 알아보겠습니다. 또한 통합 문서를 편집하여 사용자 지정하는 방법도 살펴보겠습니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 **Azure Sentinel** 서비스로 이동합니다.

1. 위협 인텔리전스 데이터 커넥터를 사용하여 위협 지표를 가져온 **작업 영역** 을 선택합니다.

1. Azure Sentinel 메뉴의 **위협 관리** 섹션에서 **통합 문서** 를 선택합니다.

1. **Threat Intelligence** 라는 통합 문서로 이동하여 아래와 같이 **ThreatIntelligenceIndicator** 테이블에 데이터가 있는지 확인합니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="데이터 확인":::
 
1. **저장** 단추를 선택하고 통합 문서를 저장할 Azure 위치를 선택합니다. 이 단계는 어떤 방식으로든 통합 문서를 수정하고 변경 내용을 저장하려는 경우에 필요합니다.

1. 이제 **저장된 통합 문서 보기** 단추를 선택하여 보기 및 편집을 위해 통합 문서를 엽니다.

1. 이제 템플릿에서 제공하는 기본 차트가 표시됩니다. 이제 차트 중 하나를 변경하겠습니다. 페이지 맨 위에 있는 **편집** 단추를 선택하여 통합 문서 편집 모드로 전환합니다.

1. 위협 유형별 위협 지표라는 새 차트를 추가해 보겠습니다. 페이지의 아래쪽으로 스크롤하여 쿼리 추가를 선택합니다.

1. **Log Analytics 작업 영역 로그 쿼리** 텍스트 상자에 다음 텍스트를 추가합니다.
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. **시각화** 드롭다운에서 **막대형 차트** 를 선택합니다.

1. **편집 완료** 단추를 선택합니다. 통합 문서에 대한 새 차트를 만들었습니다.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="가로 막대형 차트":::

통합 문서는 Azure Sentinel의 모든 측면에 대한 정보를 제공하는 강력한 대화형 대시보드를 제공합니다. 통합 문서를 사용하면 많은 작업을 수행할 수 있으며, 제공된 템플릿이 좋은 출발점이지만, 이러한 템플릿을 사용자 지정하거나, 다양한 데이터 원본을 결합하는 새로운 대시보드를 만들어 데이터를 고유한 방식으로 시각화할 수 있습니다. Azure Sentinel 통합 문서는 Azure Monitor 통합 문서를 기반으로 하기 때문에 이미 광범위한 설명서를 사용할 수 있으며 더 많은 템플릿이 있습니다. [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법에 대한 이 문서에서 시작하는 것이 좋습니다. 

또한 GitHub에는 추가 템플릿을 다운로드하고 자체 템플릿을 제공할 수 있는 활발한 [Azure Monitor 통합 문서](https://github.com/microsoft/Application-Insights-Workbooks) 커뮤니티가 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel의 위협 인텔리전스 기능 및 새로운 위협 인텔리전스 블레이드에 대해 알아보았습니다. Azure Sentinel의 위협 인텔리전스 기능을 사용하는 방법에 대한 실제 지침은 다음 문서를 참조하세요.

- Azure Sentinel에 [위협 인텔리전스 데이터 연결](./connect-threat-intelligence.md)
- Azure Sentinel에서 [기본 제공](./tutorial-detect-threats-built-in.md) 또는 [사용자 지정](./tutorial-detect-threats-custom.md) 경고를 만들고 인시던트를 [조사](./tutorial-investigate-cases.md)