---
title: 양식 인식기 샘플 레이블 지정 도구를 배포 하는 방법
titleSuffix: Azure Cognitive Services
description: 감독 학습에 도움이 되도록 양식 인식기 샘플 레이블 지정 도구를 배포할 수 있는 다양 한 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207870"
---
# <a name="deploy-the-sample-labeling-tool"></a>샘플 레이블 도구 배포

폼 인식기 샘플 레이블 지정 도구는 Docker 컨테이너에서 실행 되는 응용 프로그램입니다. 이는 감독 학습을 위해 양식 문서에 수동으로 레이블을 지정할 때 사용할 수 있는 유용한 UI를 제공 합니다. [레이블 및 학습](./quickstarts/label-tool.md) 빠른 시작에서는 로컬 컴퓨터에서 도구를 실행 하는 방법을 보여 줍니다 .이는 가장 일반적인 시나리오입니다. 

이 가이드에서는 샘플 레이블 지정 도구를 배포 하 고 실행할 수 있는 다른 방법을 설명 합니다. 

## <a name="deploy-with-azure-container-instances"></a>Azure Container Instances를 사용 하 여 배포

Docker 웹 앱 컨테이너에서 레이블 도구를 실행할 수 있습니다. 먼저 Azure Portal에 [새 웹 앱 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.WebSite) . 구독 및 리소스 그룹 세부 정보로 양식을 작성 합니다. 필수 필드에 다음 정보를 입력 합니다.
* **게시**: Docker 컨테이너
* **운영** 시스템: Linux

다음 페이지에서 Docker 컨테이너 설정에 대해 다음 필드를 입력 합니다.

* **옵션**: 단일 컨테이너
* **이미지 원본**: Azure Container Registry
* **액세스 형식**: public
* **이미지 및 태그**: mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest

다음 단계는 선택 사항입니다. 앱 배포가 완료 되 면이를 실행 하 고 온라인으로 레이블 도구에 액세스할 수 있습니다.

### <a name="connect-to-azure-ad-for-authorization"></a>권한 부여를 위해 Azure AD에 연결

자격 증명이 있는 사용자만 로그인 하 여 앱을 사용할 수 있도록 웹 앱을 AAD (Azure Active Directory)에 연결 하는 것이 좋습니다. [App Service 앱 구성](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) 의 지침에 따라 AAD에 연결 합니다.

## <a name="next-steps"></a>다음 단계

도구를 사용 하 여 학습 데이터에 수동으로 레이블을 만들고 감독 학습을 수행 하는 방법을 알아보려면 [레이블으로 학습](./quickstarts/label-tool.md) 빠른 시작으로 돌아갑니다.
