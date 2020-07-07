---
title: 리포지토리에서 IoT 플러그 앤 플레이 미리 보기 모델 관리 | Microsoft Docs '
description: IoT용 Azure Certified 포털, Azure CLI 및 Visual Studio code를 사용 하 여 리포지토리에서 장치 기능 모델을 관리 하는 방법입니다.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159152"
---
# <a name="manage-models-in-the-repository"></a>리포지토리의 모델 관리

IoT 플러그 앤 플레이 미리 보기 모델 리포지토리는 장치 기능 모델과 인터페이스를 저장 합니다. 리포지토리를 사용 하면 솔루션 개발자가 모델 및 인터페이스를 검색 하 고 사용할 수 있습니다.

리포지토리를 관리 하는 데 사용할 수 있는 세 가지 도구는 다음과 같습니다.

- IoT용 Azure Certified 포털
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>모델 리포지토리

장치 기능 모델과 인터페이스를 저장 하는 모델 리포지토리에는 다음 두 가지 유형이 있습니다.

- [IoT용 Azure Certified 디바이스 카탈로그](https://aka.ms/iotdevcat)장치에 대 한 장치 기능 모델 및 인터페이스를 저장 하는 단일 _공용 리포지토리가_ 있습니다. 또한이 리포지토리는 Microsoft 파트너가 게시 한 [공용 인터페이스](./concepts-common-interfaces.md) 및 [dcms 및 인터페이스](./howto-onboard-portal.md)를 저장 합니다. 장치를 인증 하 고 장치 기능 모델을 공용 리포지토리에 추가 하는 방법을 알아보려면 [IoT 플러그 앤 플레이 장치 인증](./tutorial-certification-test.md)자습서를 참조 하세요.
- 여러 _회사 리포지토리가_있습니다. 회사 리포지토리는 [IoT용 Azure Certified 포털에](./howto-onboard-portal.md)등록할 때 조직에 대해 자동으로 만들어집니다. 개발 및 테스트 중에 회사 리포지토리를 사용 하 여 장치 기능 모델과 인터페이스를 저장할 수 있습니다.

## <a name="azure-certified-for-iot-portal"></a>IoT용 Azure Certified 포털

[IoT용 Azure Certified 포털](https://preview.catalog.azureiotsolutions.com)에서 다음 작업을 완료할 수 있습니다.

- [IoT 장치에 대 한 인증 프로세스를 완료](./tutorial-certification-test.md)합니다.
- IoT 플러그 앤 플레이 장치 기능 모델을 찾습니다. 이러한 모델을 사용 하 여 [IoT 준비 장치를 신속 하 게 빌드하고 솔루션과 통합할](./quickstart-connect-pnp-device-solution-node.md)수 있습니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 IoT 플러그 앤 플레이 공용 및 회사 모델 리포지토리에서 장치 기능 모델 및 인터페이스를 관리 하기 위한 명령을 제공 합니다. 자세한 내용은 Azure CLI 방법 가이드의 [Azure IoT 확장 설치 및 사용](./howto-install-pnp-cli.md) 을 참조 하세요.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code에서 **모델 리포지토리** 뷰를 엽니다.

1. Visual Studio Code를 열고 **Ctrl + Shift + P**를 사용한 다음 **IoT 플러그 앤 플레이: 모델 리포지토리 열기**를 입력 하 고 선택 합니다.

1. **공용 모델 리포지토리를 열거나** **조직 모델 리포지토리를 열도록**선택할 수 있습니다. 회사 모델 리포지토리의 경우 모델 리포지토리 연결 문자열을 입력 해야 합니다. 이 연결 문자열은 **회사 리포지토리**의 **연결문자열** 탭에 있는 [IoT용 Azure Certified 포털](https://preview.catalog.azureiotsolutions.com)에서 찾을 수 있습니다.

1. 새 탭에서 **모델 리포지토리** 뷰가 열립니다.

    이 뷰를 사용 하 여 장치 기능 모델과 인터페이스를 추가, 다운로드 및 삭제할 수 있습니다. 필터를 사용 하 여 목록에서 특정 항목을 찾을 수 있습니다.

1. 회사 모델 리포지토리와 공용 모델 리포지토리를 전환 하려면 **Ctrl + Shift + P**를 사용 하 여 **IoT 플러그 앤 플레이: 로그 아웃 모델 리포지토리**를 입력 하 고 선택 합니다. 그런 다음 **IoT 플러그 앤 플레이: Model 리포지토리 열기** 명령을 다시 사용 합니다.

> [!NOTE]
> VS Code에서 공용 모델 리포지토리는 읽기 전용입니다. Microsoft 파트너는 [IoT용 Azure Certified 포털](https://preview.catalog.azureiotsolutions.com)에서 공용 리포지토리를 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계는 [인증을 위해 IoT 플러그 앤 플레이 장치를 제출](tutorial-certification-test.md)하는 방법을 배우는 것입니다.
