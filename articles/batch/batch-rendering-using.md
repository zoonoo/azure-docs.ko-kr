---
title: 렌더링 기능 - Azure Batch
description: Azure Batch 렌더링 기능을 사용하는 방법
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 2dff44f0b5b4b02c39c4c63f23ff64d55ca9d833
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337609"
---
# <a name="using-azure-batch-rendering"></a>Azure Batch 렌더링 사용

몇 가지 방법으로 Azure Batch 렌더링을 사용할 수 있습니다.

* API:
  * Batch API 중 하나를 사용하여 코드를 작성합니다.  개발자는 클라우드나 온-프레미스 여부에 관계없이 Azure Batch 기능을 기존 애플리케이션이나 워크플로에 통합할 수 있습니다.
* 명령줄 도구:
  * [Azure 명령줄](https://docs.microsoft.com/cli/azure/) 또는 [PowerShell](https://docs.microsoft.com/powershell/azure/overview)을 사용하여 Batch 사용을 스크립트로 작성할 수 있습니다.
  * 특히 [Batch CLI 템플릿 지원](https://docs.microsoft.com/azure/batch/batch-cli-templates)을 사용하면 풀을 만들고 작업을 제출하는 것이 훨씬 쉽습니다.
* Batch Explorer UI:
  * [Batch Explorer](https://github.com/Azure/BatchLabs)는 Batch 계정의 관리 및 모니터를 지원하는 플랫폼 간 클라이언트 도구입니다.
  * 각각의 렌더링 애플리케이션에 대해 간편하게 풀을 만들고 작업을 제출하는 데 사용할 수 잇는 많은 풀과 작업 템플릿이 제공됩니다.  템플릿 집합은 GitHub에서 액세스할 수 있는 템플릿 파일과 함께 애플리케이션 UI에 나열됩니다.
  * 사용자 지정 템플릿을 처음부터 새로 작성하거나, GitHub에서 제공하는 템플릿을 복사하여 수정할 수 있습니다.
* 클라이언트 애플리케이션 플러그 인:
  * 플러그 인을 통해 클라이언트 디자인 및 모델링 애플리케이션 안에서 직접 Batch 렌더링을 사용하도록 할 수 있습니다.  플러그 인은 주로 현재 3D 모델에 대한 컨텍스트 정보를 통해 Batch Explorer 애플리케이션을 호출하며 자산 관리를 지원하는 기능을 포함합니다.

개발자나 Azure 전문가가 아닌 최종 사용자가 Azure Batch 렌더링을 사용해보는 가장 간단한 방법은 직접 또는 클라이언트 애플리케이션 플러그인에서 호출하여 Batch Explorer 애플리케이션을 사용하는 것입니다.

## <a name="using-batch-explorer"></a>Batch Explorer 사용

Batch Explorer를 사용하여 렌더링을 수행하는 단계별 자습서는 [Blender 자습서](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)를 참조하세요.

### <a name="download-and-install"></a>다운로드 및 설치

Batch Explorer [다운로드](https://azure.github.io/BatchExplorer/)는Windows, OSX 및 Linux용으로 제공됩니다.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>템플릿을 사용하여 풀 만들기 및 작업 실행

Batch Explorer에 다양한 템플릿을 사용하면 풀, 작업, 태스크를 Batch로 직접 만드는 데 필요한 모든 속성을 일일이 지정하지 않고도 간편하게 풀을 만들고 다양한 렌더링 애플리케이션에 대해 작업을 제출할 수 있습니다.  Batch Explorer에서 사용할 수 있는 템플릿은 [GitHub 저장소](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)에 저장 및 표시됩니다.

![Batch Explorer 갤러리](./media/batch-rendering-using/batch-explorer-gallery.png)

템플릿은 Marketplace 렌더링 VM 이미지에 있는 모든 애플리케이션에 대해 준비되어 제공됩니다.  각 애플리케이션에 대해 CPU 및 GPU 풀, Windows 풀 및 Linux 풀을 서비스하는 풀 템플릿 등, 여러 템플릿이 존재하며 작업 템플릿은 전체 프레임 또는 타일 방식 Blender 렌더링 및 V-Ray 분산 렌더링을 포함합니다. 제공된 템플릿 집합은 시간에 따라 풀 자동 크기 조정과 같은 다른 Batch 기능을 서비스하기 위해 확장됩니다.

고객 템플릿은 처음부터 새로 만들거나 제공된 템플릿을 수정하여 만들 수 있습니다. Batch Explorer의 '갤러리' 섹션에서 '로컬 템플릿' 항목을 선택하여 사용자 지정 템플릿을 사용할 수 있습니다.

### <a name="file-system-and-data-movement"></a>파일 시스템 및 데이터 이동

Batch Explorer의 '데이터' 섹션에서는 로컬 파일 시스템과 Azure Storage 계정 간에 파일을 복사할 수 있습니다.

## <a name="client-application-plug-ins"></a>클라이언트 애플리케이션 플러그 인

일부 클라이언트 애플리케이션에는 플러그 인을 사용할 수 있습니다.  플러그 인을 사용하면 풀 및 작업을 애플리케이션에서 직접 만들거나 Batch Explorer를 호출할 수 있습니다.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>다음 단계

Batch 렌더링 예제는 다음 두 자습서를 사용해 보세요.

* [Azure CLI를 사용한 렌더링](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Batch ExplorerI를 사용한 렌더링](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)