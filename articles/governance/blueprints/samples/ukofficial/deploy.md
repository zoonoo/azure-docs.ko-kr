---
title: -영국 공식 및 영국 NHS blueprint-샘플 배포 단계
description: 영국 공식 및 영국 NHS blueprint 샘플의 단계를 배포 합니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 43aae882f27031d3e51ac8a4f5a68d243a973d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453204"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>영국 공식 및 영국 NHS blueprint 샘플 배포

영국 공식 및 영국 NHS blueprint 샘플을 배포 하려면 다음 단계를 수행 해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨**으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. **모든 서비스**를 선택하고 왼쪽 창에서 **정책**을 검색하여 선택합니다. **정책** 페이지에서 **청사진**을 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. 찾을 합니다 **영국 공식** 또는 **영국 NHS** 에서 blueprint 샘플 _다른 샘플_ 선택한 **이 샘플을 사용 하 여**.

1. 청사진 샘플의 _기본 사항_을 입력합니다.

   - **청사진 이름**: 청사진 샘플 사본의 이름을 지정합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트**를 선택합니다.

1. 청사진 샘플을 구성하는 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장**을 선택합니다.

## <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시**해야만 할당하고 배포할 수 있습니다. Blueprint 샘플의 복사본을 수정 표준에서 이동할 수 있지만 해당 환경 및 요구 사항에 사용자 지정할 수 있습니다.

1. **모든 서비스**를 선택하고 왼쪽 창에서 **정책**을 검색하여 선택합니다. **정책** 페이지에서 **청사진**을 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시**를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전**을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. 제공할 **정보를 변경** "첫 번째 버전 영국 공식 또는 영국 NHS blueprint 샘플에서 게시 합니다."와 같은 그런 후 페이지 아래쪽의 **게시**를 선택합니다.

## <a name="assign-the-sample-copy"></a>샘플 사본 할당

청사진 샘플 사본이 **게시**되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 샘플 사본의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. **모든 서비스**를 선택하고 왼쪽 창에서 **정책**을 검색하여 선택합니다. **정책** 페이지에서 **청사진**을 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당**을 선택합니다.

1. 청사진 할당의 매개 변수 값을 지정합니다.

   - 기본 사항

     - **구독**: 청사진 샘플 사본을 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진의 이름에 따라 이 이름이 미리 채워집니다.
       필요에 따라 변경하거나 그대로 둡니다.
     - **Location**: 관리 ID를 만들 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값 _시스템이 할당한_ 관리 ID 옵션을 유지합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당**을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 1 시간이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료**입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 아티팩트 매개 변수의 목록을 제공합니다.

아티팩트 이름|아티팩트 형식|매개 변수 이름|설명|
|-|-|-|-|
|영국 공식 또는 영국 NHS 이니셔티브 blueprint|정책 할당 |감사 진단 로그에 대 한 리소스 유형 (정책: 영국 공식 또는 영국 NHS 이니셔티브를 blueprint) |진단 로그 설정을 사용 하도록 설정 해야 하는 경우를 감사 하려면 리소스 형식의 목록입니다.  허용 되는 값을 참조 하세요 [Azure 진단 로그에 대 한 서비스, 스키마 및 범주를 지 원하는](../../../../azure-monitor/platform/diagnostic-logs-schema.md)합니다. |
|[미리 보기]: Linux VM용 Log Analytics 에이전트 배포 |정책 할당 |선택 사항: 지원 되는 범위에 추가 하는 Linux OS는 VM 이미지 목록 (정책: [Preview]: Linux Vm에 대 한 Log Analytics 에이전트 배포) |(선택 사항) 기본값은 _none_합니다. 자세한 내용은 [Azure portal에서 Log Analytics 작업 영역 만들기](../../../../azure-monitor/learn/quick-create-workspace.md)합니다. |
|[미리 보기]: Windows VM용 Log Analytics 에이전트 배포 |정책 할당 |선택 사항: 지원 되는 범위에 추가할 Windows OS는 VM 이미지 목록 (정책: [Preview]: Windows Vm에 대 한 Log Analytics 에이전트 배포) |(선택 사항) 기본값은 _none_합니다. 자세한 내용은 [Azure portal에서 Log Analytics 작업 영역 만들기](../../../../azure-monitor/learn/quick-create-workspace.md)합니다. |

## <a name="next-steps"></a>다음 단계

영국 공식 및 영국 NHS blueprint 샘플을 배포 하는 단계를 검토 했으므로 이제는 컨트롤 매핑을 확인 하 고 개요에 대해 자세히 알아보려면 다음 문서를 방문 합니다.

> [!div class="nextstepaction"]
> [영국 공식 및 영국 NHS 청사진-개요](./index.md)
> [영국 공식 및 영국 NHS 청사진-매핑을 제어 합니다.](./control-mapping.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
