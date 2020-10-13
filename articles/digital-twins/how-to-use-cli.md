---
title: Azure Digital Twins CLI 사용
titleSuffix: Azure Digital Twins
description: Azure Digital Twins CLI를 시작 하 고 사용 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950910"
---
# <a name="use-the-azure-digital-twins-cli"></a>Azure Digital Twins CLI 사용

Azure digital Twins는 Azure Portal에서 Azure Digital Twins 인스턴스를 관리 하는 것 외에도 다음을 포함 하 여 서비스와 관련 하 여 가장 중요 한 작업을 수행 하는 데 사용할 수 있는 **CLI (명령줄 인터페이스)** 를 포함 합니다.
* Azure Digital Twins 인스턴스 관리
* 모델 관리
* Digital 쌍 관리
* 쌍 관계 관리
* 엔드포인트 구성
* [경로](concepts-route-events.md) 관리
* RBAC (역할 기반 액세스 제어)를 통해 [보안](concepts-security.md) 구성

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>사용 (배포 및 유효성 검사)

일반적으로 인스턴스를 관리 하는 것 외에도 CLI는 배포 및 유효성 검사에 유용한 도구입니다.
* 제어 평면 명령을 사용 하 여 새 인스턴스를 반복 하거나 자동으로 배포할 수 있습니다.
* 데이터 평면 명령은 인스턴스의 값을 신속 하 게 확인 하 고 작업이 예상 대로 완료 되었는지 확인 하는 데 사용할 수 있습니다.

## <a name="get-the-extension"></a>확장 가져오기

Azure Digital Twins 명령은 [Azure CLI에 대 한 Azure IoT 확장](https://github.com/Azure/azure-iot-cli-extension)의 일부입니다. 명령 `az iot` 집합: [ *az dt* command reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)에 대 한 참조 설명서의 일부로 전체 명령과 사용의 전체 목록을 볼 수 있습니다.

이러한 단계를 통해 확장의 최신 버전이 있는지 확인할 수 있습니다. [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [로컬 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)에서 이러한 명령을 실행할 수 있습니다.

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>다음 단계

참조 문서를 통해 CLI 및 전체 명령 집합을 탐색 합니다.
* [*az dt* 명령 참조](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
