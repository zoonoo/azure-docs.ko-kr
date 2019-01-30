---
title: Batch AI 리소스 정보 - Azure | Microsoft Docs
description: Microsoft Azure Batch AI 서비스의 작업 영역, 클러스터, 파일 서버, 실험 및 작업에 대한 개요입니다.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407748"
---
# <a name="overview-of-resources-in-batch-ai"></a>Batch AI의 리소스 개요

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Batch AI 서비스를 사용하여 처음 시작하는 경우 사용 가능한 Batch AI 리소스를 알아볼 수 있습니다. 다른 Azure 서비스와 마찬가지로, 하나 이상의 Azure *리소스 그룹*에 Batch AI 리소스를 만듭니다. 리소스 그룹에 하나 이상의 Batch AI *작업 영역*을 만듭니다. 각 작업 영역에는 Batch AI *클러스터*, *파일 서버* 및 *실험*이 혼합되어 있습니다. Batch AI 실험은 *작업* 그룹을 캡슐화합니다.

다음 이미지는 Batch AI에 대한 리소스 계층의 예를 보여 줍니다. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

다음 섹션에서는 Batch AI 리소스에 대해 자세히 설명합니다.

## <a name="workspace"></a>작업 영역

Batch AI의 작업 영역은 나머지 Batch AI 리소스의 최상위 컬렉션입니다. 작업 영역은 다른 그룹이나 프로젝트에 속한 작업을 구분하는 데 도움이 됩니다. 예를 들어 테스트 작업 영역을 만들 수 있습니다.

## <a name="cluster"></a>프로비전

Batch AI의 클러스터에는 작업을 실행하기 위한 계산 리소스가 포함되어 있습니다. 클러스터의 모든 노드에는 동일한 VM 크기 및 OS 이미지가 있습니다. Batch AI는 다양한 요구에 맞게 사용자 지정된 클러스터를 만들 수 있도록 여러 옵션을 제공합니다. 일반적으로 프로젝트를 완료하는 데 필요한 처리 성능의 범주마다 다른 클러스터를 설정합니다. 수요와 예산에 따라 Batch AI 클러스터의 규모를 확장 또는 축소합니다. 자세한 내용은 [Batch AI 클러스터로 작업](clusters.md)을 참조하세요.

## <a name="file-server"></a>파일 서버

선택적으로 Batch AI에 파일 서버를 만들어 데이터, 교육 스크립트 및 출력 로그를 저장합니다. Batch AI 파일 서버는 중앙에서 쉽게 액세스할 수 있는 작업용 저장소 위치를 제공하기 위해 클러스터 노드에 자동으로 탑재될 수 있는 관리 단일 노드 NFS입니다. 대부분의 경우 하나의 작업 영역에는 하나의 파일 서버만 필요하며, 학습 작업용 데이터는 다른 디렉터리로 구분할 수 있습니다. NFS가 워크로드에 적합하지 않은 경우 Batch AI는 [Azure Storage](use-azure-storage.md) 또는 사용자 지정 솔루션(예: Gluster 또는 Lustre 파일 시스템)을 비롯한 다른 스토리지 옵션을 지원합니다.

## <a name="experiment"></a>실험

실험은 쿼리하고 함께 관리하는 관련 작업 컬렉션을 그룹화합니다. 각 작업 영역에는 여러 실험이 있을 수 있으며 각 실험에서 특정 문제를 해결하려고 시도합니다.

## <a name="job"></a>작업

작업은 실행해야 할 단일 작업 또는 스크립트(예: Deep Learning 모델을 교육하는 경우)입니다. 각 작업은 작업 영역의 한 클러스터에서 특정 스크립트를 실행합니다. 스크립트는 Batch AI 파일 서버 또는 기타 저장소 솔루션에 저장될 수도 있습니다. 각 Batch AI 작업에는 TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, 사용자 지정 MPI 또는 사용자 지정 등과 관련된 프레임워크 형식이 있습니다. 각 프레임워크에 대해 Batch AI 서비스는 필요한 인프라를 설정하고 작업 프로세스를 관리합니다. 각 실험에는 다른 매개 변수에 대한 몇 가지 변경 사항을 제외하고는 유사한 여러 작업이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 첫 번째 [Batch AI 교육 작업](quickstart-tensorflow-training-cli.md)을 실행합니다.

* 다양한 프레임워크에 대한 샘플 [교육 레시피](https://github.com/Azure/BatchAI/tree/master/recipes)를 살펴봅니다.