---
title: Custom Vision의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Custom Vision에 대한 뉴스가 포함되어 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: a87e76d4a726f7a01b96d602e7f41d60409dae56
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521495"
---
# <a name="whats-new-in-custom-vision"></a>Custom Vision의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목은 릴리스 정보, 비디오, 블로그 게시물 및 기타 유형의 정보입니다. 서비스를 최신 상태로 유지하려면 이 페이지에 책갈피를 설정합니다.

## <a name="july-2020"></a>2020년 7월

### <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

* Custom Vision은 Azure 리소스에 대한 개별 액세스를 관리하는 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. Custom Vision 프로젝트에 대한 액세스를 관리하는 방법을 알아보려면 [Azure 역할 기반 액세스 제어](./role-based-access-control.md)를 참조하세요.

### <a name="subset-training"></a>하위 집합 학습

* 개체 감지 프로젝트를 학습하는 경우 적용된 태그의 하위 집합에 대해서만 선택적으로 학습을 수행할 수 있습니다. 특정 태그는 아직 충분히 적용하지 않았지만 다른 태그는 충분히 적용한 경우 이 작업을 수행할 수 있습니다. 자세히 알아보려면 C# 또는 Python의 [클라이언트 라이브러리 빠른 시작](./quickstarts/object-detection.md)을 참조하세요.

### <a name="azure-storage-notifications"></a>Azure 스토리지 알림

* Custom Vision 프로젝트를 Azure Blob Storage 큐와 통합하여 프로젝트 학습/내보내기 작업 및 게시된 모델의 백업 복사본에 대한 푸시 알림을 받을 수 있습니다. 이 기능은 작업이 오래 실행될 때 결과를 위해 서비스를 지속적으로 폴링하는 것을 방지하는 데 유용합니다. 대신 스토리지 큐 알림을 워크플로에 통합할 수 있습니다. 자세히 알아보려면 [스토리지 통합](./storage-integration.md) 가이드를 참조하세요.

### <a name="copy-and-move-projects"></a>프로젝트 복사 및 이동

* 이제 한 Custom Vision 계정에서 다른 계정으로 프로젝트를 복사할 수 있습니다. 개발 환경에서 프로덕션 환경으로 프로젝트를 이동하거나 데이터 보안을 강화하기 위해 프로젝트를 다른 Azure 지역의 계정으로 백업할 수 있습니다. 자세한 내용은 [프로젝트 복사 및 이동](./copy-move-projects.md) 가이드를 참조하세요.

## <a name="september-2019"></a>2019년 9월

### <a name="suggested-tags"></a>제안된 태그

* [Custom Vision 웹 사이트](https://www.customvision.ai/)의 Smart Labeler 도구는 학습 이미지에 대한 제안된 태그를 생성합니다. 이렇게 하면 Custom Vision 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다. 이 기능을 사용하는 방법에 대한 지침은 [제안된 태그](./suggested-tags.md)를 참조하세요.

## <a name="cognitive-service-updates"></a>Cognitive Service 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)