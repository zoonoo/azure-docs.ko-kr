---
title: 프로젝트 소음 Azure Batch 계정 설정
titlesuffix: Azure Cognitive Services
description: 이 방법 설명 프로젝트 소음 Unity 및 Unreal 엔진 통합을 사용 하 여 사용 하 여 Azure Batch 계정 설정을 합니다.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335760"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>프로젝트 소음 Azure Batch 계정 설정
이 방법 설명 프로젝트 소음 Unity 및 Unreal 엔진 통합을 사용 하 여 사용 하 여 Azure Batch 계정 설정을 합니다.

## <a name="get-an-azure-subscription"></a>Azure 구독 가져오기
Batch 및 Storage 계정을 설정하려면 [Azure 구독](https://azure.microsoft.com/free/)이 필요합니다. 처음 등록하는 경우 Azure는 시간이 제한된 몇 가지 무료 리소스와 $200 크레딧을 제공합니다.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch 및 Storage 계정 만들기
이제 [다음 지침](https://docs.microsoft.com/azure/batch/batch-account-create-portal)에 따라 Azure Batch 및 관련 Azure Storage 계정을 설정합니다.

Batch 및 Storage 계정에 대한 기본 옵션을 선택합니다.
  
  ![스크린 샷 Azure Batch의 새 계정 옵션 기본 설정을 보여 주는](media/new-batch-account-create.png)

  ![스크린 샷의 Azure Storage 새 계정 옵션 기본 설정을 보여 주는](media/batch-storage-account-create.png)

Azure에서 계정을 배포하는 데 몇 분 정도 걸립니다. 포털의 오른쪽 위 모서리에서 완료 알림을 찾습니다.
  
  ![Azure의 스크린 샷 계정 배포 알림](media/batch-accounts-deploy-notification.png)

이제 대시보드에 계정이 표시됩니다.
  
  ![Batch 및 Storage 계정을 보여주는 스크린샷의 Azure 포털 대시보드](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Azure 자격 증명을 사용하여 음향 효과 준비 UI 설정
대시보드에서 Batch 계정 링크를 클릭한 다음, Batch 계정 페이지의 **키**를 클릭하여 자격 증명에 액세스합니다.
  
  ![링크 강조 표시 된 [키] 페이지를 사용 하 여 Azure Batch의 스크린 샷 계정](media/batch-access-keys.png)

  ![액세스 키를 사용 하 여 Azure Batch의 스크린 샷 계정 키 페이지](media/batch-keys-info.png)

페이지의 **Storage 계정** 링크를 클릭하여 Azure Storage 계정 자격 증명에 액세스합니다.
  
  ![액세스 키를 사용 하 여 스크린 샷의 Azure Storage 계정 키 페이지](media/storage-keys-info.png)

이러한 자격 증명을 입력 합니다 [Unity 적용 플러그 인](unity-baking.md) 또는 [플러그 인 Unreal 적용](unreal-baking.md)합니다.

## <a name="node-types-and-region-support"></a>노드 유형 및 지역 지원
프로젝트 소음 Fsv2 시리즈 및 H 모든 Azure 지역에서 지원 되지 않습니다 노드인 Azure VM 액세스에 최적화 된 계산에 필요 합니다. [이 표](https://azure.microsoft.com/global-infrastructure/services)를 확인하여 Batch 계정에 대한 적절한 위치를 선택하세요.
![지역별 Azure Virtual Machines를 보여 주는 스크린샷](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>할당량 업그레이드
Azure Batch 계정은 계정 생성 시 프로비전되며, 20개의 계산 코어로 제한됩니다. 장면에 프로브 점 개수까지 여러 노드에서 소음 워크 로드를 병렬화 할 수 있으므로 적용 시간을 단축이 제한을 늘릴 선택 될 수 있습니다. 클릭 하 여 할당량 증가 요청할 수 있습니다 합니다 Azure Batch 포털 페이지에서 **할당량** 링크를 클릭하고 **할당량 증가 요청**을 클릭하여 할당량 증가를 요청할 수 있습니다.

![Azure 할당량의 스크린 샷 페이지](media/azure-quotas.png)

## <a name="next-steps"></a>다음 단계
* 프로젝트 소음 플러그 인에 통합 하 [Unity](unity-integration.md) 하거나 [Unreal](unreal-integration.md) 프로젝트

