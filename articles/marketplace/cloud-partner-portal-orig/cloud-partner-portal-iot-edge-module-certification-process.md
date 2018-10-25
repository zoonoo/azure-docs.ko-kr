---
title: IoT Edge 모듈 인증 | Microsoft Docs
description: Marketplace용 IoT Edge 모듈을 인증합니다.
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c37ed908b61ca54957affed3f81526353bc3f53b
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389567"
---
# <a name="the-iot-edge-module-certification-process"></a>IoT Edge 모듈 인증 프로세스

이 문서에서는 Azure Marketplace에 게시하는 IoT Edge 모듈을 인증하는 단계와 요구 사항에 대해 설명합니다. 

>[!Note]
>임시 문서입니다. IoT Edge 모듈 마켓플레이스가 동시에 구축되고 있으므로 이 문서가 공개 문서로 대체될 것입니다.

## <a name="understanding-an-iot-edge-module"></a>IoT Edge 모듈 이해

모듈 용어:

-   **모듈 이미지**는 모듈을 정의하는 소프트웨어가 포함된 패키지입니다.

-   **모듈 인스턴스**는 IoT Edge 장치에서 모듈 이미지를 실행하는 특정 계산 단위입니다. 모듈 인스턴스는 IoT Edge 런타임에 의해 시작됩니다.

모듈에는 다음 용어를 사용하는 IoT 모듈 SDK가 포함될 수도 있습니다.

-   **모듈 ID**는 각 모듈 인스턴스에 연결된 IoT Hub에 저장된 정보(보안 자격 증명 포함)입니다.

-   **모듈 쌍**은 IoT Hub에 저장된 JSON 문서로, 메타데이터, 구성 및 조건 등 모듈 인스턴스의 상태 정보를 포함합니다.

-   **SDK**는 C\#, C, Python, Java 및 Node.JS와 같은 여러 언어로 사용자 지정 모듈을 개발하는 데 사용됩니다.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>IoT Edge 모듈의 온보딩 프로세스

다음 화면 캡처에서는 IoT Edge 모듈이 Azure Marketplace에 공개되는 프로세스를 보여 줍니다.

![인증 프로세스](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>온보딩 단계 세부 정보

-   **1단계** - 파트너가 Azure Marketplace 팀의 Cloud 파트너 포털 도구에서 제안을 **IoT Edge 모듈** 제안 유형으로 제출합니다. Cloud 파트너 포털 도구에 액세스하려면 공식 MS 파트너여야 합니다. 자세한 내용은 [게시자 지침](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)을 참조하세요.

-   **2단계** - Marketplace에서 안티 바이러스 백신 및 맬웨어 방지 검사를 자동으로 실행합니다. IoT 팀은 이 단계에서 자동화된 사용자 지정 수집 테스트를 실행할 수 있습니다.

-   **3단계** - 모듈을 공개합니다. 마켓플레이스에 나와 있으며, 컨테이너를 URL에서 익명으로 가져올 수 있습니다. 예를 들어 *marketplace.azurecr.io/module-identifier*입니다.

## <a name="iot-edge-module-certification-criteria"></a>IoT Edge 모듈 인증 조건

Azure Marketplace에서 IoT Edge 모듈을 인증 및 게시하기 위해 충족해야 하는 주요 요구 사항은 다음과 같습니다.

>[!Note]
>이러한 요구 사항은 변경할 수 있습니다. 미리 알림을 받고 업데이트된 요구 사항을 충족할 수 있도록 컨테이너를 업데이트할 시간이 제공됩니다.

### <a name="technical-requirements"></a>기술적인 요구 사항

**IoT Edge 모듈 지원**

-   현재 Docker 호환 컨테이너만 IoT Edge 모듈로 지원됩니다. 모듈은 [Moby](https://mobyproject.org/)에서 실행되어야 합니다. 

    >[!Note]
    >Moby는 Docker에 대한 기본 오픈 소스 프로젝트이므로 Docker 컨테이너가 Moby에서 작동합니다.

-   파트너가 제공해야 하는 기본 설정은 다음과 같습니다. 

    -   기본 **tag**(비워 둘 수 없음).

    -   기본 **createOptions**(비어 둘 수 있음)

    -   IoT 모듈 SDK를 사용하는 경우 기본 **twin**(비어 둘 수 있음)

    -   IoT 모듈 SDK를 사용하는 경우 기본 **routes**(비어 둘 수 있음)

**플랫폼 지원**

-   IoT Edge 계층 1에서 **일반 공급**([Azure IoT Edge 지원](https://docs.microsoft.com/azure/iot-edge/support)에서 설명함)되는 플랫폼만 지원해야 합니다. 예를 들어 이 경우는 현재 다음과 같은 OS 및 아키텍처 조합을 지원한다는 것을 의미합니다.

    -   x64용 Ubuntu Server 18.04

    -   x64용 Ubuntu Server 16.04

    -   arm32용 Raspbian-stretch(armhf)

**장치 차원 지정**

-   Raspberry Pi보다 크거나 같은 차원의 장치(CPU, RAM, 저장소, GPU 등)는 IoT Edge 장치가 될 수 있습니다. 모듈이 특정 차원 제약 조건 내에서만 작동하는 경우 해당 제약 조건을 모듈 설명에 지정해야 합니다.

**기본 설정**

-   모듈은 지원되는 각 플랫폼(OS + 아키텍처)에 대한 기본 매개 변수로 바로 시작해야 합니다.

-   구성 설정(태그, 환경 변수, 쌍, 경로)은 명확하게 문서화해야 합니다. 파트너는 목록의 일부로 기본 HTML 표시를 지원하거나 외부 웹 페이지를 가리키는 모듈에 대한 설명을 정의할 수 있습니다.

**버전 관리**

-   고객은 마켓플레이스에서 제공되는 모듈을 자동으로 업데이트할지 또는 테스트한 정확한 버전을 사용할지를 선택할 수 있어야 합니다. Marketplace 모듈은 IoT Edge 런타임과 동일한 버전 관리 패턴을 따라야 합니다. 예: 

    -   롤링 태그(예: "1.0")는 업데이트할 수 있습니다.

    -   부 버전 태그(예: "1.3.24")는 업데이트할 수 없습니다.

**원격 분석**

-   IoT 모듈 SDK를 사용하는 모듈은 원격 분석을 위해 마켓플레이스에서 할당한 고유한 모듈 식별자를 설정해야 합니다. 이렇게 하면 Azure Marketplace에서 실행되는 모듈 인스턴스의 번호를 식별할 수 있습니다. 이 고유 식별자는 수집 시 마켓플레이스에서 할당한 컨테이너 이름입니다. 이 ID를 설정하려면 다음 SDK의 메서드를 사용합니다.
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   IoT 모듈 SDK를 사용하지 않는 모듈의 경우 Cloud 파트너 포털을 통해 덜 정확한 인사이트를 얻을 수 있습니다. 예를 들어 다운로드 수입니다.

**보안**

-   컨테이너는 가능한 한 최소한의 권한으로 호스트에 액세스해야 합니다. "권한 있는" 컨테이너는 매우 드문 경우입니다.

-   파트너는 제공하는 지원의 일부로 모듈을 안전하게 유지해야 합니다.

**업데이트**

-   파트너는 모듈을 최신 상태의 기능으로 유지해야 합니다. IoT Edge 런타임에 계획된 주요 변경이 있으면 사전 통보를 받습니다.

**모듈 IoT SDK**

-   IoT 모듈 SDK를 포함하는 것은 인증을 위한 필수 조건이 아닙니다.
    그러나 IoT 모듈 SDK가 포함되면 더 나은 사용자 환경을 제공할 수 있습니다. 예를 들어 라우팅을 지원하고 클라우드에 메시지를 보내는 등의 작업을 수행할 수 있습니다. IoT 모듈 SDK는 실행되는 모듈 인스턴스의 번호를 원격 분석해야 합니다.

**주관적 요구 사항**

-   하나 이상의 실제 IoT Edge 사용 사례를 충족해야 합니다. 예를 들어 고객이 IoT Edge에서 사용하려는 경우가 아니면 WordPress 컨테이너를 온보딩하면 안됩니다.

**법적 요구 사항(AMP 정책)**

-   모듈은 Microsoft Azure Marketplace 계약 및 정책을 준수해야 합니다. 자세한 내용은 연결된 게시자 계약 및 [참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)을 참조하세요.

-   Azure Marketplace 계약 및 정책 외에도 IoT Edge 모듈 제안 유형과 관련된 추가 법적 요구 사항이 있을 수 있습니다. 이는 현재 검토 중입니다.

-   모듈에는 *사용 약관* 및 *개인정보처리방침*이 있어야 합니다.

-   또한 모듈에서 제3자를 사용하는 경우 제3자의 고지 사항도 있어야 합니다.

**지원 요구 사항(AMP 정책)**

-   파트너는 IoT Edge 모듈을 지원할 책임이 있습니다. IoT Edge 모듈 설명에 제공된 지원 옵션은 계속 사용할 수 있으며, 하나 이상의 지원 옵션을 항상 사용할 수 있어야 합니다. 자세한 내용은 AMP 게시자 계약의 섹션 4를 참조하세요.

**분류**

-   모든 IoT Edge 모듈은 **사물 인터넷 \> IoT Edge 모듈** 범주 아래에 표시됩니다. 파트너는 해당 제품에 가장 적합한 하위 범주를 선택해야 합니다.
