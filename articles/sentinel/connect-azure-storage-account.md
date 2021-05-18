---
title: Azure Sentinel에 Azure Storage 계정 진단 로그 연결
description: Azure Policy를 사용하여 Azure Sentinel에 Azure Storage 계정 진단 로그를 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: e9f27b5cc3c4cb526dbcf6e6511fcd53e3c2fe67
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891723"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Azure Storage 계정 진단 로그 연결

> [!IMPORTANT]
> Azure Storage 계정 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Storage 계정은 최신 데이터 스토리지 시나리오를 위한 클라우드 솔루션입니다. Blob, 파일, 큐, 테이블, 디스크 등 모든 데이터 개체가 포함되어 있습니다.

이 커넥터를 사용하면 Azure Sentinel에 Azure Storage 계정의 진단 로그를 스트림하여 조직 전체의 모든 Azure Storage 리소스에서 지속적으로 활동을 모니터링하고 보안 위협을 탐지할 수 있습니다.

[Azure Storage 계정 모니터링](../storage/common/storage-analytics-logging.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Sentinel에 Azure Storage 계정 진단 로그를 수집하려면 다음이 필요합니다.

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Policy를 사용하여 Azure Storage 리소스에 로그 스트리밍 정책을 적용하려면 정책 할당 범위에 대한 소유자 역할이 있어야 합니다.

## <a name="connect-to-azure-storage-account"></a>Azure Storage 계정에 연결

이 커넥터는 Azure Policy를 사용하여 범위로 정의된 Azure Storage 계정 리소스 컬렉션에 단일 로그 스트리밍 구성을 적용합니다. 커넥터 페이지의 왼쪽에 있는 **데이터 형식** 에서 사용 가능한 Azure Storage 로그 및 메트릭 유형을 볼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure Storage 계정** 을 선택하고 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 **Stream diagnostics logs from your Azure Storage Account at scale**(대규모로 Azure Storage 계정의 진단 로그 스트리밍)을 확장합니다.

1. **Azure Policy 할당 마법사 시작** 단추를 선택합니다.

    정책 할당 마법사가 열리고 **Log Analytics 작업 영역에 스토리지 계정용 진단 설정 구성** 이라는 새 정책을 만들 준비가 되었습니다.

    1. **기본 사항** 탭에서 **범위** 옆 세 점이 있는 단추를 클릭하여 구독을 선택하고 필요에 따라 리소스 그룹을 선택합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭에서 다음을 수행합니다.
        - **Log Analytics 작업 영역** 드롭다운 목록에서 Azure Sentinel 작업 영역을 선택합니다.
        - **배포할 스토리지 서비스** 드롭다운 목록에서 진단 설정을 배포할 스토리지 리소스 종류(파일, 테이블, 큐 등)를 선택합니다.
        - **설정 이름** 및 **효과** 필드는 그대로 둡니다.
        - 나머지 드롭다운 필드는 사용 가능한 진단 로그 및 메트릭 유형을 나타냅니다. 수집하려는 모든 유형을 “True”로 표시된 상태로 둡니다.

    1. 위의 단계를 수행하면 이후의 모든 스토리지 리소스에 정책이 적용됩니다. 기존 리소스에 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 만들기** 확인란을 표시합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 지난 14일 동안 특정 시점에 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆 연결 아이콘)가 ‘연결됨’(녹색)으로 표시됩니다. 데이터 수집 없이 14일이 경과되면 커넥터는 연결 끊김 상태로 표시됩니다. 추가 데이터가 수신되는 순간 ‘연결됨’ 상태가 반환됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에 Azure Storage 계정을 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
