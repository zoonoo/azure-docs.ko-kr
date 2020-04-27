---
title: Azure Marketplace의 컨테이너 제공에 대 한 게시 가이드
description: 이 문서에서는 Azure Marketplace의 컨테이너 제공을 게시 하기 위한 요구 사항을 설명 합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 99aecee930e5d77302ad54babd927588519e33fd
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160463"
---
# <a name="publishing-guide-for-container-offers"></a>컨테이너 제품 게시 가이드

컨테이너를 통해 컨테이너 이미지를 Azure Marketplace에 게시할 수 있습니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

컨테이너 제공은 Azure Marketplace를 통해 배포 되 고 청구 되는 트랜잭션 제품입니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

솔루션이 Kubernetes 기반 Azure Container service 인스턴스로 설정 된 Docker 컨테이너 이미지인 경우 *컨테이너* 제안 유형을 사용 합니다. 

> [!NOTE]
> Kubernetes 기반 Azure container service 인스턴스의 예는 Kubernetes 기반 컨테이너 런타임에 대 한 azure 고객이 선택할 수 있는 Azure Kubernetes 서비스 또는 Azure Container Instances입니다.  

Microsoft는 현재 무료 및 BYOL(사용자 라이선스 필요) 라이선스 모델을 지원합니다.

## <a name="container-offer-requirements"></a>컨테이너 제품 요구 사항

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 청구 및 계량 | 무료 또는 BYOL 청구 모델을 지원합니다.<br><br> |  
| Dockerfile에서 빌드된 이미지 | 컨테이너 이미지는 Docker 이미지 사양을 기반으로 하 고 Dockerfile에서 빌드해야 합니다.<br> <br>Docker 이미지를 작성 하는 방법에 대 한 자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/#usage)의 "사용법" 섹션을 참조 하세요.<br><br> |  
| Azure Container Registry 리포지토리에서 호스팅 | 컨테이너 이미지는 Azure Container Registry 리포지토리에서 호스팅해야 합니다.<br> <br>Azure Container Registry 작업에 대 한 자세한 내용은 빠른 시작 [: Azure Portal를 사용 하 여 개인 컨테이너 레지스트리 만들기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)를 참조 하세요.<br><br> |  
| 이미지 태그 지정 | 컨테이너 이미지는 태그를 하나 이상 포함 해야 합니다 (최대 태그 수: 16).<br><br>이미지에 태그를 지정 하는 방법에 대 `docker tag` 한 자세한 내용은 [Docker 설명서](https://docs.docker.com/engine/reference/commandline/tag) 사이트의 페이지를 참조 하세요.<br><br> |  

## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 Azure Marketplace를 사용 하 여 [클라우드 비즈니스를 확장](https://azuremarketplace.microsoft.com/sell)하는 방법을 알아보세요.

파트너 센터에서 등록 하 고 작업을 시작 하려면:

- [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 하 여 제품을 만들거나 완성 합니다.
- 자세한 내용은 [Azure container 제품 만들기](./partner-center-portal/create-azure-container-offer.md) 를 참조 하세요.
