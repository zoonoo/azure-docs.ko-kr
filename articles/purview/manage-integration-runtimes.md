---
title: Integration Runtime 만들기 및 관리
description: 이 문서에서는 Azure Purview에서 Integration Runtime을 만들고 관리하는 단계를 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 73144611e835ac1bea20ab92212e52941af84eef
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463744"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기 및 관리

이 문서에서는 Azure Purview에서 데이터 원본을 검사할 수 있는 SHIR(자체 호스팅 통합 런타임)을 만들고 관리하는 방법을 설명합니다.

> [!NOTE]
> Purview Integration Runtime은 동일한 컴퓨터의 Azure Synapse Analytics 또는 Azure Data Factory Integration Runtime으로 공유할 수 없습니다. 분리된 컴퓨터에 설치해야 합니다.

## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

1. Purview Studio 홈페이지의 왼쪽 탐색 창에서 **관리 센터** 를 선택합니다.

2. 왼쪽 창의 **원본 및 스캔** 에서 **통합 런타임** 을 선택한 다음 **+ 새로 만들기** 를 선택합니다.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR을 클릭합니다.":::

3. **통합 런타임 설정** 페이지에서 **자체 호스팅** 을 선택하여 자체 호스팅 IR을 만든 다음 **계속** 을 선택합니다.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="새 SHIR을 만듭니다.":::

4. IR의 이름을 입력하고 생성을 선택합니다.

5. **통합 런타임 설정** 페이지에서 **수동 설정** 섹션의 단계를 따릅니다. 다운로드 사이트에서 실행하려는 VM 또는 컴퓨터에 통합 런타임을 다운로드해야 합니다.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="키 가져오기":::

   - 인증 키를 복사하여 붙여넣습니다.

   - 로컬 Windows 컴퓨터에 [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 통합 런타임을 다운로드합니다. 설치 관리자를 실행합니다. 5\.4.7803.1 및 5.6.7795.1 등의 자체 호스팅 통합 런타임 버전이 지원됩니다. 

   - 이전에 저장한 두 키 중 하나를 **Integration Runtime(자체 호스팅) 등록** 페이지에 붙여넣고 **등록** 을 선택합니다.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="키를 입력합니다.":::

   - **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침** 을 선택합니다.

6. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 창이 표시됩니다.

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="성공적으로 등록 되었습니다.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 관리

자체 호스팅 통합 런타임을 **관리 센터** 내부에 위치하는 **통합 런타임** 메뉴로 이동해 IR을 선택하고 편집을 클릭해 편집할 수 있습니다. 이제 설명을 업데이트하거나, 키를 복사하거나, 새 키를 다시 생성할 수 있습니다.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR을 편집합니다.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR 세부 정보를 편집합니다.":::

관리 센터의 **통합 런타임** 메뉴로 이동해 IR을 선택하고 **삭제** 를 클릭해 자체 호스팅 통합 런타임을 삭제할 수 있습니다. IR이 삭제되면 IR을 사용하는 진행 중인 검사가 실패합니다.

## <a name="next-steps"></a>다음 단계

[검사를 통해 삭제된 자산을 검색하는 방법](concept-detect-deleted-assets.md)
