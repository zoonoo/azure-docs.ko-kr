---
title: Azure Resource Mover란?
description: Azure Resource Mover에 대해 알아보기
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653338"
---
# <a name="what-is-azure-resource-mover"></a>Azure Resource Mover란?

이 문서는 Azure Resource Mover 서비스에 대한 개요를 제공합니다. Resource Mover는 Azure 지역 간에 Azure 리소스를 이동하는 데 유용합니다.

리소스를 다른 Azure 지역으로 이동하여 다음을 수행할 수 있습니다.

- **지역 시작에 맞추기**: 이전에 사용할 수 없었던 새로 도입된 Azure 지역으로 리소스를 이동합니다.
- **서비스/기능에 맞추기**: 리소스를 이동하여 특정 지역에서 사용할 수 있는 서비스 또는 기능을 활용합니다.
- **비즈니스 개발에 대응**: 합병 또는 인수 등의 비즈니스 변화에 대응하여 리소스를 지역으로 이동합니다.
- **근접성에 맞추기**: 리소스를 비즈니스 지역으로 이동합니다.
- **데이터 요구 사항 충족**: 데이터 상주 요구 사항 또는 데이터 분류 요구 사항에 맞게 리소스를 이동합니다.
- **배포 요구 사항에 대응**: 오류로 배포된 리소스를 이동하거나 용량 요구 사항에 따라 이동합니다.
- **서비스 해제에 대응**: 지역이 서비스 해제되어서 리소스를 이동합니다.

> [!IMPORTANT]
> Azure Resource Mover는 현재 공개 미리 보기로 제공됩니다.

## <a name="why-use-resource-mover"></a>Resource Mover를 사용하는 이유

Resource Mover는 다음을 제공합니다.

- 지역 간에 리소스를 이동하기 위한 단일 허브입니다.
- 이동 시간과 복잡성이 줄어듭니다. 필요한 모든 항목은 단일 위치에 있습니다.
- 다양한 유형의 Azure 리소스를 이동할 수 있는 간단하고 일관된 환경을 제공합니다.
- 이동하려는 리소스 간의 종속성을 쉽게 식별할 수 있는 방법입니다. 이를 통해 관련 리소스를 함께 이동함으로써 이동 후 대상 지역에서 모든 것이 예상대로 작동하도록 할 수 있습니다.
- 이동 후 리소스를 삭제하려는 경우 원본 지역의 리소스를 자동으로 정리합니다.
- 테스트 이동을 시도한 다음, 전체 이동을 수행하지 않으려면 취소할 수 있습니다.

## <a name="move-across-regions"></a>지역 간 이동

지역 간에 리소스를 이동하려면 이동할 리소스를 선택합니다. Resource Mover는 이러한 리소스의 유효성을 검사하고 다른 리소스에 대한 종속성이 있으면 해결합니다. 종속성이 있는 경우 두 가지 옵션이 있습니다.
- 종속 리소스를 대상 지역으로 이동합니다.
- 종속 리소스를 이동하지 않고 대신 대상 지역에서 동등한 리소스를 사용합니다.

모든 종속성이 해결되면 Resource Mover가 간단한 이동 프로세스를 안내합니다.

1. 초기 이동을 시작합니다.
2. 초기 이동 후 커밋하고 이동을 완료할지 또는 이동을 무시할지 결정할 수 있습니다.
3. 이동이 완료된 후 원본 위치에서 리소스를 삭제할지 여부를 결정할 수 있습니다.

Resource Mover 허브의 지역 간에 또는 리소스 그룹 내에서 리소스를 이동할 수 있습니다. [자세한 정보](select-move-tool.md) 

## <a name="what-resources-can-i-move-across-regions"></a>지역 간에 이동할 수 있는 리소스는 무엇인가요?

현재 Resource Mover를 사용하면 지역 간에 다음 리소스를 이동할 수 있습니다.

- Azure VM 및 연결된 디스크
- NIC
- 가용성 집합 
- Azure 가상 네트워크 
- 공용 IP 주소
- NSG(네트워크 보안 그룹)::
- 내부 및 공용 부하 분산 장치 
- Azure SQL 데이터베이스 및 탄력적 풀


## <a name="next-steps"></a>다음 단계

Resource Mover 구성 요소 및 이동 프로세스에 대해 [자세히 알아봅니다](about-move-process.md).
