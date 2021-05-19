---
title: Azure Certified Device 프로그램 | Microsoft Docs
description: Azure Certified Device 프로그램을 이해합니다.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92048103"
---
# <a name="what-is-the-azure-certified-device-program"></a>Azure Certified Device 프로그램이란?

Azure Certified Device 프로그램을 사용하면 고객 솔루션이 Azure 서비스와 원활하게 작동합니다. 프로그램은 도구, 서비스, 마켓플레이스를 사용하여 디바이스 및 솔루션 빌더 커뮤니티와 업계 지식 및 모범 사례를 공유합니다.

이 프로그램은 다음을 수행하도록 설계되었습니다.

- **고객 신뢰도 제공:** 고객은 Azure 서비스와 함께 사용하기 위해 Microsoft에서 인증한 디바이스를 안심하고 구매할 수 있습니다.

- **고객이 솔루션에 적합한 디바이스를 찾을 수 있도록 지원:** 디바이스 빌더는 인증 프로세스의 일환으로 자사 디바이스의 고유한 기능을 게시할 수 있습니다. 고객은 각자의 요구 사항에 맞는 제품을 쉽게 찾을 수 있습니다.

- **인증된 디바이스 승격:** 디바이스 빌더는 Azure Certified Device 브랜드의 가시성, 고객 접촉, 사용량을 향상시킵니다.

이 문서에서는 다음과 같이 방법을 설명합니다.

- Azure Certified Device 프로그램에 회사를 온보딩합니다.
- 디바이스에 적용할 수 있는 인증을 결정합니다.
- 프로그램 요구 사항 및 테스트를 시작하는 데 도움이 되는 기타 리소스를 확인합니다.
- Azure Certified Device 포털을 사용하여 디바이스의 유효성을 검사합니다.

## <a name="onboarding"></a>온보딩

[Azure Certified Device 포털](https://aka.ms/acdp)에서 디바이스를 인증하려면 다음 단계를 완료해야 합니다.

1. 회사에 회사 또는 학교 테넌트를 사용하는 Azure Active Directory 계정이 있는지 확인합니다.
2. 계정을 사용하여 [MPN(Microsoft 파트너 네트워크)](https://partner.microsoft.com/)에 가입합니다.
3. MPN에 가입한 후 [Azure Certified Device 포털](https://aka.ms/acdp)에 로그인합니다.
4. 회사 프로필 페이지에서 [프로그램 계약](https://aka.ms/acdagreement)을 검토하고 서명합니다.

### <a name="company-profile"></a>회사 프로필

Azure Certified Device 포털에서 회사 프로필을 관리하려면 **회사 프로필** 을 선택합니다. 회사 프로필에는 회사 URL, 메일 주소, 로고가 포함됩니다. 인증 작업을 계속 진행하기 전에 이 페이지의 프로그램 계약에 동의합니다.

Azure Certified Device 카탈로그의 디바이스 설명 페이지는 회사 프로필 정보를 사용합니다.

## <a name="choose-the-certification"></a>인증 선택

각각 서로 다른 고객 가치를 제공하는 데 중점을 두는 세 가지 인증이 있습니다. 빌드하는 디바이스 유형과 대상 사용자에 따라 가장 적합한 인증을 하나 또는 여러 개 선택할 수 있습니다.

### <a name="azure-certified-device"></a>Azure Certified Device

‘Azure Certified Device 인증’은 디바이스에서 Azure IoT Hub에 연결하고 DPS(Device Provisioning Service)를 통해 안전하게 프로비저닝할 수 있는지 확인합니다. 이 인증은 고급 인증에 필요한 기준이 되는 디바이스 기능 및 상호 운용성을 반영합니다.

- 자세한 내용은 [인증 요구 사항](https://aka.ms/acdrequirements)을 참조하세요.
- DPS를 사용하여 디바이스를 Azure IoT Hub에 연결하는 방법에 대한 자세한 내용은 [디바이스 프로비저닝 개요](../iot-dps/about-iot-dps.md)를 참조하세요.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

Azure Certified Device 인증에 대한 증분 인증인 ‘IoT 플러그 앤 플레이 인증’은 사용자 지정 디바이스 코드 없이 디바이스를 빌드하는 프로세스를 간소화합니다. IoT 플러그 앤 플레이를 사용하면 하드웨어 파트너가 Azure IoT Central 및 타사 솔루션을 기반으로 하는 클라우드 솔루션과 쉽게 통합되는 디바이스를 빌드할 수 있습니다.

- 자세한 내용은 [인증 요구 사항](https://aka.ms/acdiotpnprequirements)을 참조하세요.
- IoT 플러그 앤 플레이 테스트를 위해 디바이스를 준비하는 방법에 대한 자세한 내용은 [IoT 플러그 앤 플레이 디바이스를 인증하는 방법](howto-certify-device.md)을 참조하세요.

### <a name="edge-managed"></a>Edge Managed

Azure Certified Device 인증에 대한 증분 인증인 ‘Edge Managed 인증’은 Windows, Linux 또는 RTOS를 실행하는 Azure IoT 디바이스에 대한 디바이스 관리 표준에 중점을 둡니다. 현재 이 프로그램 인증은 모듈 배포 및 관리를 위한 Edge 런타임 호환성에 중점을 둡니다.

> [!TIP]
> 이전에는 이 프로그램을 ‘IoT Edge 인증 프로그램’이라고 했습니다.

- 자세한 내용은 [인증 요구 사항](https://aka.ms/acdedgemanagedrequirements)을 참조하세요.
- IoT Edge에 대한 자세한 내용은 [IoT Edge 개요](../iot-edge/about-iot-edge.md)를 참조하세요.
- 지원되는 운영 체제 및 컨테이너에 대한 자세한 내용은 [IoT Edge 지원 시스템](../iot-edge/support.md)을 참조하세요.

## <a name="use-the-azure-certified-device-portal"></a>Azure Certified Device 포털 사용

이 섹션에는 [Azure Certified Device 포털](https://certify.azure.com)을 사용하는 방법이 요약되어 있습니다. 자세한 내용은 [포털 시작 가이드](https://aka.ms/acdhelp)를 참조하세요.

Azure Certified Device 프로그램에서 디바이스를 인증하려면 다음 네 단계를 완료합니다.

1. 디바이스 세부 정보 제공
2. 디바이스 테스트
3. 제출 및 검토 완료
4. Azure Certified Device 카탈로그에 게시(선택 사항)

### <a name="provide-device-details"></a>디바이스 세부 정보 제공

인증할 각 디바이스에 대해 인증 포털의 양식을 사용하여 디바이스 하드웨어, 설치 지침, 마케팅 자료에 대한 세부 정보를 기록합니다.

- **디바이스 정보:** 이름, 설명, 하드웨어 세부 정보, 운영 체제와 같은 디바이스 정보를 수집합니다.
- **시작 가이드**: 고객이 제품을 신속하게 사용할 수 있도록 도움말을 제공하는 PDF 문서입니다. [샘플 템플릿](https://aka.ms/GSTemplate)을 사용할 수 있습니다.
- **마케팅 세부 정보:** 사진, 배포자 정보 등 디바이스에 대한 고객 지원 마케팅 정보를 제공합니다.
- **추가 업계 인증:** 디바이스가 보유한 다른 인증에 대한 정보를 제공할 수 있는 선택적 섹션입니다.

### <a name="test-the-device"></a>디바이스 테스트

이 단계에서는디바이스에서 DPS를 사용하여 IoT Hub에 연결한 후 디바이스를 조작하고 일련의 테스트를 실행합니다. 완료되면 디바이스 테스트 결과가 포함된 로그 파일 집합을 볼 수 있습니다.

인증 포털에는 테스트에 사용되는 IoT Hub 인스턴스에 연결하는 방법에 대한 지침이 나와 있습니다. [지원되는 증명 방법](../iot-dps/concepts-service.md#attestation-mechanism) 중 하나를 통해 DPS 연결을 설정할 수 있습니다.

디바이스에 대한 추가 수동 유효성 검사를 위해 Azure Certified Device 팀이 디바이스 빌더에게 문의할 수도 있습니다.

### <a name="submit-and-publish"></a>제출 및 게시

최종 필수 단계는 검토를 위해 프로젝트를 제출하는 것입니다. 이 단계에서는 디바이스 및 마케팅 세부 정보, 시작 가이드를 포함하여 프로젝트의 완전성을 검토하도록 Azure Certified Device 팀원에게 알립니다. 팀원은 이전에 질문과 함께 제공된 회사 메일 주소로 문의하거나 승인 전에 요청을 편집할 수 있습니다.

인증의 일환으로 디바이스에 추가 수동 유효성 검사가 필요한 경우 이때 공지를 받게 됩니다.

디바이스가 인증되면 제품 요약 페이지의 **카탈로그에 게시** 기능을 사용하여 Azure Certified Device 카탈로그에 제품 세부 정보를 게시할 수 있습니다.

## <a name="if-you-have-questions"></a>질문이 있는 경우

인증 프로그램, 인증 포털 또는 Azure Certified Device 카탈로그에 대해 궁금한 사항이 있으면 [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)으로 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure Certified Device 프로그램에 대해 개괄적으로 살펴보았으며, [IoT 플러그 앤 플레이 디바이스를 인증 하는 방법](howto-certify-device.md)을 알아보는 단계를 계속 진행하시기 바랍니다.