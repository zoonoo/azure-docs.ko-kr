---
title: 음향 효과에 대한 Azure 계정 설정 - Cognitive Services
description: 음향 효과를 사용하는 데 필요한 Azure Batch 및 Storage 계정을 설정하기 위한 이 가이드를 따릅니다.
services: cognitive-services
author: ashtat
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: d5e78df2cb17e8275aef3694dda90a705ef4bdaa
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181960"
---
# <a name="create-an-azure-batch-account"></a>Azure Batch 계정 만들기
음향 효과를 사용하는 데 필요한 Azure Batch 및 Storage 계정을 설정하기 위한 이 가이드를 따릅니다. 프로젝트 Acoustics의 일환으로 개발된 Unity 플러그 인에 대한 내용은 [음향 효과란?](what-is-acoustics.md)을 참조하세요. Unity 프로젝트에 음향 효과를 통합하는 방법에 대한 내용은 [시작하기](getting-started.md)를 참조하세요.  

## <a name="get-an-azure-subscription"></a>Azure 구독 가져오기
Batch 및 Storage 계정을 설정하려면 [Azure 구독](https://azure.microsoft.com/free/)이 필요합니다. 처음 등록하는 경우 Azure는 시간이 제한된 몇 가지 무료 리소스와 $200 크레딧을 제공합니다.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch 및 Storage 계정 만들기
이제 [다음 지침](https://docs.microsoft.com/azure/batch/batch-account-create-portal)에 따라 Azure Batch 및 관련 Azure Storage 계정을 설정합니다.

Batch 및 Storage 계정에 대한 기본 옵션을 선택합니다.
  
  ![새 Batch 계정](media/NewBatchAccountCreate.png)

  ![새 Storage 계정](media/BatchStorageAccountCreate.png)

Azure에서 계정을 배포하는 데 몇 분 정도 걸립니다. 포털의 오른쪽 위 모서리에서 완료 알림을 찾습니다.
  
  ![계정이 배포됨](media/BatchAccountsDeployNotification.png)

이제 대시보드에 계정이 표시됩니다.
  
  ![포털 대시보드](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Azure 자격 증명을 사용하여 음향 효과 준비 UI 설정
대시보드에서 Batch 계정 링크를 클릭한 다음, Batch 계정 페이지의 **키**를 클릭하여 자격 증명에 액세스합니다.
  
  ![Batch 키 링크](media/BatchAccessKeys.png)

  ![Batch 계정 자격 증명](media/BatchKeysInfo.png)

페이지의 **Storage 계정** 링크를 클릭하여 Azure Storage 계정 자격 증명에 액세스합니다.
  
  ![Storage 계정 자격 증명](media/StorageKeysInfo.png)

[준비 UI 연습](bake-ui-walkthrough.md)에 설명된 대로 준비 탭에 이러한 자격 증명을 입력합니다.

## <a name="node-types-and-region-support"></a>노드 유형 및 지역 지원
프로젝트 Acoustics에서는 모든 Azure 지역에서 지원되지는 않을 수 있는 F 및 H 시리즈 계산 최적화 Azure VM 노드가 필요합니다. [이 표](https://azure.microsoft.com/global-infrastructure/services)를 확인하여 Batch 계정에 대한 적절한 위치를 선택하세요. 현재, H 시리즈 가상 머신은 미국 동부, 미국 중북부, 미국 중남부, 미국 서부, 미국 서부 2, 유럽 북부, 유럽 서부 및 일본 서부에서 지원됩니다.

## <a name="upgrading-your-quota"></a>할당량 업그레이드
Azure Batch 계정은 계정 생성 시 프로비전되며, 20개의 계산 코어로 제한됩니다. 여러 노드에서 음향 워크로드를 병렬화할 수 있으므로 더 빠른 준비 시간을 위해 장면의 프로브 점 수까지 이 제한을 늘릴 수 있습니다. 클릭 하 여 할당량 증가 요청할 수 있습니다 합니다 Azure Batch 포털 페이지에서 **할당량** 링크를 클릭하고 **할당량 증가 요청**을 클릭하여 할당량 증가를 요청할 수 있습니다.

![Azure 할당량 증가](media/azurequotas.png)

## <a name="next-steps"></a>다음 단계
* [Unity 프로젝트에 음향 통합](getting-started.md) 시작하기
* [샘플 장면](sample-walkthrough.md) 탐색

