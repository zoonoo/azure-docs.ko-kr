---
title: Azure Portal를 사용 하 여 Cloud Services (확장 지원)에 대 한 ARM 템플릿 생성
description: Azure Portal를 사용 하 여 Cloud Services (확장 지원)에 대 한 ARM 템플릿 및 매개 변수 파일을 생성 하 고 다운로드 합니다.
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 215abb1ce8d65b5ecdd25aeb78c17c70e801a9d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555628"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Cloud Services (확장 지원)에 대 한 ARM 템플릿 생성

이 문서에서는 클라우드 서비스 (확장 지원)를 배포한 후 [Azure Portal](https://portal.azure.com) 에서 ARM 템플릿 및 매개 변수 파일을 가져오는 방법을 설명 합니다. ARM 템플릿 및 매개 변수 파일은 클라우드 서비스를 업그레이드 하거나 업데이트 하기 위해 이후 배포에서 사용할 수 있습니다 (확장 지원).

## <a name="get-arm-template-via-portal"></a>포털을 통해 ARM 템플릿 가져오기

  1. 리소스 그룹으로 이동 하 고 배포를 선택 합니다.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="이미지 Azure Portal의 리소스 그룹에서 배포를 선택 하는 방법을 보여 줍니다.":::
  
  2. 클라우드 서비스 (확장 지원)를 선택 하 고 템플릿을 클릭 합니다.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="이미지에는 Azure Portal의 클라우드 서비스 (확장 지원)에서 템플릿을 선택 하는 것이 표시 됩니다.":::
  
  3. 템플릿 및 매개 변수 파일을 다운로드 합니다. PowerShell을 통해 향후 배포에 사용할 수 있습니다.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="이미지는 Azure Portal에서 템플릿 파일을 다운로드 하는 방법을 보여 줍니다.":::
  
## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md) 를 사용 하 여 클라우드 서비스 배포 (확장 지원)
  
