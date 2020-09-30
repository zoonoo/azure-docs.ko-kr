---
title: Azure 인증 장치 프로그램 | Microsoft Docs
description: Azure 인증 장치 프로그램을 이해 합니다.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9580f1c7a29b19bb926a7079b95cb3ed0b314d05
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580032"
---
# <a name="what-is-the-azure-certified-device-program"></a>Azure 인증 장치 프로그램 이란?

Azure 인증 장치 프로그램을 사용 하면 고객 솔루션이 Azure 서비스와 원활 하 게 작동 합니다. 프로그램은 도구, 서비스 및 마켓플레이스를 사용 하 여 장치 및 솔루션 빌더 커뮤니티와 업계 지식과 모범 사례를 공유 합니다.

이 프로그램은 다음을 수행 하도록 설계 되었습니다.

- **고객 신뢰도 제공:** 고객은 Azure 서비스와 함께 사용 하기 위해 Microsoft에서 인증 한 장치를 안전 하 게 구매할 수 있습니다.

- **고객이 솔루션에 적합 한 장치를 찾는 데 도움을 줍니다.** 장치 빌더는 인증 프로세스의 일환으로 자신의 장치에 고유한 기능을 게시할 수 있습니다. 고객은 각자의 요구에 맞는 제품을 쉽게 찾을 수 있습니다.

- **인증 된 장치 승격:** 장치 빌더는 향상 된 가시성을 얻고, 고객과 연결 하 고, Azure 인증 장치 브랜드를 사용 합니다.

이 문서에서는 다음과 같이 방법을 설명합니다.

- Azure 인증 장치 프로그램에 회사를 등록 합니다.
- 장치에 적용할 수 있는 인증을 결정 합니다.
- 테스트를 시작 하는 데 도움이 되는 프로그램 요구 사항 및 기타 리소스를 찾아보세요.
- Azure 인증 장치 포털을 사용 하 여 장치의 유효성을 검사 합니다.

## <a name="onboarding"></a>온보딩

[Azure 인증 장치 포털](https://aka.ms/acdp)에서 장치를 인증 하려면 다음 단계를 완료 해야 합니다.

1. 회사 또는 학교 테 넌 트를 사용 하 여 회사에 Azure Active Directory 계정이 있는지 확인 합니다.
2. 계정을 사용 하 여 [Microsoft 파트너 네트워크 (MPN)](https://partner.microsoft.com/) 에 가입 합니다.
3. MPN에 가입한 후 [Azure 인증 장치 포털](https://aka.ms/acdp) 에 로그인 합니다.
4. 회사 프로필 페이지에서 [프로그램 계약](https://aka.ms/acdagreement) 검토 및 서명

### <a name="company-profile"></a>회사 프로필

Azure 인증 장치 포털에서 회사 프로필을 관리 하려면 **회사 프로필**을 선택 합니다. 회사 프로필에는 회사 URL, 전자 메일 주소 및 로고가 포함 됩니다. 인증 작업을 계속 하기 전에이 페이지의 프로그램 계약에 동의 합니다.

Azure 인증 장치 카탈로그의 장치 설명 페이지에서는 회사 프로필 정보를 사용 합니다.

## <a name="choose-the-certification"></a>인증 선택

각각 서로 다른 고객 가치를 제공 하는 데 중점을 세는 세 가지 인증이 있습니다. 빌드 중인 장치와 대상 대상의 유형에 따라 가장 적합 한 인증 또는 인증을 선택할 수 있습니다.

### <a name="azure-certified-device"></a>Azure 인증 장치

_Azure 인증 장치 인증은_ 장치가 Azure IoT Hub와 연결 하 고 DPS (장치 프로 비전 서비스)를 통해 안전 하 게 프로 비전 할 수 있는지 확인 합니다. 이 인증은 고급 인증을 위해 필요한 기준이 되는 장치의 기능 및 상호 운용성을 반영 합니다.

- 자세히 알아보려면 [인증 요구 사항](https://aka.ms/acdrequirements)을 참조 하세요.
- DPS를 사용 하 여 장치를 Azure IoT Hub에 연결 하는 방법에 대 한 자세한 내용은 [장치 프로 비전 개요](../iot-dps/about-iot-dps.md)를 참조 하세요.

### <a name="iot-plug-and-play"></a>IoT 플러그 앤 플레이

Azure 인증 장치 인증에 대 한 증분 인증용 _IoT 플러그 앤 플레이 인증_은 사용자 지정 장치 코드 없이 장치를 빌드하는 프로세스를 간소화 합니다. IoT 플러그 앤 플레이를 사용 하면 하드웨어 파트너가 Azure IoT Central 및 타사 솔루션을 기반으로 하는 클라우드 솔루션과 쉽게 통합 되는 장치를 구축할 수 있습니다.

- 자세히 알아보려면 [인증 요구 사항](https://aka.ms/acdiotpnprequirements)을 참조 하세요.
- IoT 플러그 앤 플레이 테스트를 위해 장치를 준비 하는 방법에 대 한 자세한 내용은 [iot 플러그 앤 플레이 장치를 인증 하는 방법](howto-certify-device.md)을 참조 하세요.

### <a name="edge-managed"></a>Edge 관리

Azure 인증 장치 인증에 대 한 증분 인증 인 _Edge 관리 인증_에서는 Windows, LINUX 또는 rtos를 실행 하는 azure IoT 장치에 대 한 장치 관리 표준을 집중적으로 다룹니다. 현재이 프로그램 인증은 모듈 배포 및 관리에 대 한 Edge 런타임 호환성에 중점을 둔 것입니다.

> [!TIP]
> 이 프로그램은 이전에 _IoT Edge 인증 프로그램_이라고 했습니다.

- 자세히 알아보려면 [인증 요구 사항](https://aka.ms/acdedgemanagedrequirements)을 참조 하세요.
- IoT Edge에 대 한 자세한 내용은 [IoT Edge 개요](../iot-edge/about-iot-edge.md)를 참조 하세요.
- 지원 되는 운영 체제 및 컨테이너에 대 한 자세한 내용은 [지원 되는 IoT Edge 시스템](../iot-edge/support.md)을 참조 하세요.

## <a name="use-the-azure-certified-device-portal"></a>Azure 인증 장치 포털 사용

이 섹션에서는 [Azure 인증 장치 포털](https://certify.azure.com)을 사용 하는 방법을 요약 합니다. 자세히 알아보려면 [포털 시작 가이드](https://aka.ms/acdhelp)를 참조 하세요.

Azure 인증 장치 프로그램에서 장치를 인증 하려면 다음 네 단계를 완료 합니다.

1. 장치 세부 정보 제공
2. 장치 테스트
3. 제출 하 고 검토를 완료 합니다.
4. Azure 인증 장치 카탈로그에 게시 (선택 사항)

### <a name="provide-device-details"></a>장치 세부 정보 제공

인증 하려는 각 장치에 대해 인증 포털의 양식을 사용 하 여 장치 하드웨어, 설치 지침 및 마케팅 자료에 대 한 세부 정보를 기록 합니다.

- **장치 정보:** 이름, 설명, 하드웨어 세부 정보 및 운영 체제와 같은 장치에 대 한 정보를 수집 합니다.
- **시작 가이드**: 고객이 제품을 신속 하 게 사용 하는 데 사용할 수 있는 PDF 문서입니다. [샘플 템플릿을](https://aka.ms/GSTemplate) 사용할 수 있습니다.
- **마케팅 세부 정보:** 사진 및 배포자 정보 등 장치에 대 한 고객 지원 마케팅 정보를 제공 합니다.
- **추가 업계 인증:** 장치에 있는 다른 인증에 대 한 정보를 제공할 수 있는 선택적 섹션입니다.

### <a name="test-the-device"></a>장치 테스트

이 단계에서는 장치와 상호 작용 하 고 장치에서 DPS를 사용 하 여 IoT Hub에 연결한 후 일련의 테스트를 실행 합니다. 완료 되 면 장치 테스트 결과가 포함 된 로그 파일 집합을 볼 수 있습니다.

인증 포털에는 테스트에 사용 되는 IoT Hub 인스턴스에 연결 하는 방법에 대 한 지침이 있습니다. [지원 되는 증명 방법](https://aka.ms/acdAttestation)중 하나를 통해 DPS 연결을 설정할 수 있습니다.

Azure 인증 장치 팀은 장치에 대 한 추가 수동 유효성 검사를 위해 장치 빌더에 연결할 수 있습니다.

### <a name="submit-and-publish"></a>제출 및 게시

최종 필수 단계는 검토를 위해 프로젝트를 제출 하는 것입니다. 이 단계에서는 장치 및 마케팅 세부 정보 및 시작 가이드를 포함 하 여 프로젝트의 완전성을 검토 하도록 Azure 인증 장치 팀 멤버에 게 알립니다. 팀 구성원은 이전에 질문에 제공 된 회사 전자 메일 주소에서 사용자에 게 연락 하거나 승인 전에 요청을 편집할 수 있습니다.

장치에서 인증의 일부로 추가 수동 유효성 검사가 필요한 경우 이번에는 알림을 받게 됩니다.

장치가 인증 되 면 제품 요약 페이지의 **카탈로그에 게시** 기능을 사용 하 여 Azure 인증 장치 카탈로그에 제품 세부 정보를 게시 하도록 선택할 수 있습니다.

## <a name="if-you-have-questions"></a>질문이 있는 경우

[iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)인증 프로그램, 인증 포털 또는 Azure 인증 장치 카탈로그에 대해 궁금한 사항이 있는 경우 팀에 문의 하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure 인증 장치 프로그램의 개요를 만들었으므로, [IoT 플러그 앤 플레이 장치를 인증 하는 방법을](howto-certify-device.md)알아보려면 다음 단계를 제안 합니다.
