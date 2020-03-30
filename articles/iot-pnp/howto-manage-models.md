---
title: 리포지토리에서 IoT 플러그 앤 플레이 미리보기 모델 관리| 마이크로소프트 문서'
description: IoT 포털, Azure CLI 및 Visual Studio 코드를 위해 Azure 인증을 사용하여 리포지토리에서 장치 기능 모델을 관리하는 방법
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159152"
---
# <a name="manage-models-in-the-repository"></a>리포지토리에서 모델 관리

IoT 플러그 앤 플레이 미리 보기 모델 리포지토리는 장치 기능 모델 및 인터페이스를 저장합니다. 리포지토리는 솔루션 개발자가 모델과 인터페이스를 검색 가능하고 소모할 수 있도록 합니다.

리포지토리를 관리하는 데 사용할 수 있는 세 가지 도구가 있습니다.

- IoT 포털용 Azure 인증
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>모델 리포지토리

장치 기능 모델 및 인터페이스를 저장하기 위한 두 가지 유형의 모델 리포지토리가 있습니다.

- IoT 장치 [카탈로그에 장치에](https://aka.ms/iotdevcat)대한 장치 기능 모델 및 인터페이스를 저장하는 단일 _공용 리포지토리가_ 있습니다. 이 리포지토리는 또한 Microsoft 파트너가 게시한 [공통 인터페이스와](./concepts-common-interfaces.md) [DcM 및 인터페이스를 저장합니다.](./howto-onboard-portal.md) 장치를 인증하고 해당 장치 기능 모델을 공용 리포지토리에 추가하는 방법을 알아보려면 [IoT 플러그 앤 플레이 장치를 인증하는](./tutorial-certification-test.md)자습서를 참조하십시오.
- 여러 _회사 리포지토리가_있습니다. IoT 포털에 대한 [Azure 인증 포털에 온보온하면](./howto-onboard-portal.md)조직에 대해 회사 리포지토리가 자동으로 만들어집니다. 회사 리포지토리를 사용하여 개발 및 테스트 중에 장치 기능 모델 및 인터페이스를 저장할 수 있습니다.

## <a name="azure-certified-for-iot-portal"></a>IoT 포털용 Azure 인증

[IoT에 대한 Azure 인증 포털에서](https://preview.catalog.azureiotsolutions.com)다음 작업을 완료할 수 있습니다.

- [IoT 장치에 대한 인증 프로세스를 완료합니다.](./tutorial-certification-test.md)
- IoT 플러그 앤 플레이 장치 기능 모델을 찾습니다. 이러한 모델을 사용하여 [IoT 지원 장치를 신속하게 구축하고 솔루션과 통합할](./quickstart-connect-pnp-device-solution-node.md)수 있습니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 IoT 플러그 앤 플레이 공용 및 회사 모델 리포지토리에서 장치 기능 모델 및 인터페이스를 관리하기 위한 명령을 제공합니다. 자세한 내용은 Azure CLI 사용 방법 [가이드에 대한 Azure IoT 확장 설치 및 사용을](./howto-install-pnp-cli.md) 참조하십시오.

## <a name="visual-studio-code"></a>Visual Studio Code

비주얼 스튜디오 코드에서 **모델 리포지토리** 보기를 엽니다.

1. 시각적 스튜디오 코드를 열고 **Ctrl+Shift+P를**사용하여 **IoT 플러그 앤 플레이: 오픈 모델 리포지토리를 입력하고**선택합니다.

1. 공용 모델 **리포지토리** 를 열거나 **조직 모델 리포지토리를 열도록**선택할 수 있습니다. 회사 모델 리포지토리의 경우 모델 리포지토리 연결 문자열을 입력해야 합니다. 이 연결 문자열은 **회사 리포지토리**의 **연결문자열** 탭에 있는 [IoT용 Azure Certified 포털](https://preview.catalog.azureiotsolutions.com)에서 찾을 수 있습니다.

1. 새 탭에서 **모델 리포지토리** 뷰가 열립니다.

    이 보기를 사용하여 장치 기능 모델 및 인터페이스를 추가, 다운로드 및 삭제합니다. 필터를 사용하여 목록에서 특정 항목을 찾을 수 있습니다.

1. 회사 모델 리포지토리와 공용 모델 리포지토리 간에 전환하려면 **Ctrl+Shift+P를**사용하여 **IoT 플러그 앤 플레이: 모델 리포지토리로 로그아웃합니다.** 그런 다음 **IoT 플러그 앤 플레이: 모델 리포지토리** 명령을 다시 엽니다.

> [!NOTE]
> VS 코드에서 공용 모델 리포지토리는 읽기 전용입니다. Microsoft 파트너는 [IoT 포털에 대한 Azure 인증](https://preview.catalog.azureiotsolutions.com)에서 공용 리포지토리를 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

제안된 다음 단계는 [인증을 위해 IoT 플러그 앤 플레이 장치를 제출하는](tutorial-certification-test.md)방법을 알아보는 것입니다.
