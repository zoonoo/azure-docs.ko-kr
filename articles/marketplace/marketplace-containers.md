---
title: Azure Marketplace의 컨테이너 제공에 대 한 게시 가이드
description: 이 문서에서는 Azure Marketplace의 컨테이너 제공을 게시 하기 위한 요구 사항을 설명 합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95741664"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Azure container 제품 게시 가이드

Azure 컨테이너를 통해 컨테이너 이미지를 Azure Marketplace에 게시할 수 있습니다. 이 가이드를 사용 하 여이 제품 유형에 대 한 요구 사항을 이해 합니다.

Azure 컨테이너 제공은 Azure Marketplace를 통해 배포 되 고 청구 되는 트랜잭션 제품입니다. 사용자에 게 표시 되는 나열 옵션은 "지금 가져오기"입니다.

솔루션이 Kubernetes 기반 Azure Container 인스턴스로 설정 된 Docker 컨테이너 이미지인 경우 Azure Container 제안 유형을 사용 합니다.

> [!NOTE]
> Azure Container instance는 가상 머신을 관리 하지 않고 상위 수준의 서비스를 채택 하지 않고도 Azure에서 컨테이너를 실행 하는 가장 빠르고 간단한 방법을 제공 하는 런타임 docker 인스턴스입니다. 컨테이너 인스턴스는 azure에 직접 배포 하거나 azure Kubernetes Services 또는 Azure Kubernetes Service Engine을 통해 오케스트레이션 수 있습니다.  

Microsoft는 현재 무료 및 BYOL(사용자 라이선스 필요) 라이선스 모델을 지원합니다.

## <a name="container-offer-requirements"></a>컨테이너 제품 요구 사항

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 청구 및 계량 | 무료 또는 BYOL 청구 모델을 지원합니다.<br><br> |  
| Dockerfile에서 빌드된 이미지 | 컨테이너 이미지는 Docker 이미지 사양을 기반으로 하 고 Dockerfile에서 빌드해야 합니다.<br> <br>Docker 이미지를 작성 하는 방법에 대 한 자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/#usage)의 "사용법" 섹션을 참조 하세요.<br><br> |  
| Azure Container Registry 리포지토리에서 호스팅 | 컨테이너 이미지는 Azure Container Registry 리포지토리에서 호스팅해야 합니다.<br> <br>Azure Container Registry 작업에 대 한 자세한 내용은 빠른 시작 [: Azure Portal를 사용 하 여 개인 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-portal.md)를 참조 하세요.<br><br> |  
| 이미지 태그 지정 | 컨테이너 이미지는 태그를 하나 이상 포함 해야 합니다 (최대 태그 수: 16).<br><br>이미지에 태그를 지정 하는 방법에 대 한 자세한 내용은 `docker tag` [Docker 설명서](https://docs.docker.com/engine/reference/commandline/tag) 사이트의 페이지를 참조 하세요.<br><br> |  

## <a name="next-steps"></a>다음 단계

- 컨테이너 제품에 대 한 기술 자산을 준비 하려면 [Azure 컨테이너 기술 자산 만들기](create-azure-container-technical-assets.md)를 참조 하세요.

- Azure container 제품을 만들려면 [Azure Marketplace에서 azure container 제품 만들기](create-azure-container-offer.md) 를 참조 하세요.
