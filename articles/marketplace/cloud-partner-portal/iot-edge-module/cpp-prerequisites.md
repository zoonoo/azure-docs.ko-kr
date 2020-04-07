---
title: Azure IoT 에지 모듈 필수 구성 조건 | Azure 마켓플레이스
description: IoT Edge 모듈을 게시하기 위한 필수 구성 요소입니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 93e0418004ea65f93ad436eec1ce523424a194ba
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744974"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 모듈 게시 필수 구성 요소

>[!Important]
>2020년 3월 30일부터 파트너 센터로 IoT Edge 모듈의 관리를 이전합니다. 마이그레이션 후 파트너 센터에서 오퍼를 만들고 관리합니다. IoT [Edge 생성 모듈 오퍼의](https://aka.ms/AzureCreateIoT) 지침에 따라 마이그레이션된 오퍼를 관리합니다.

이 문서에서는 IoT Edge 모듈 제품을 게시하기 위한 필수 구성 요소를 설명합니다.  아직 수행하지 않은 경우 [IoT Edge 모듈 게시 가이드를 검토하십시오.](../..//iot-edge-module.md)


## <a name="publishing-prerequisites"></a>게시 필수 구성 요소

IoT Edge 모듈을 Azure Marketplace에 게시하려면 다음 필수 구성 요소를 충족해야 합니다.

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 액세스합니다. 자세한 내용은 [Azure Marketplace 및 AppSource 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)를 참조하세요.
- [Azure Marketplace 약관](https://azure.microsoft.com/support/legal/marketplace-terms/)에 대한 규약
- Azure Container Registry에서 IoT Edge 모듈 기술 자산을 호스트합니다.  자세한 내용은 [IoT Edge 모듈 기술 자산을 준비하는 방법](./cpp-create-technical-assets.md)을 참조하세요.
- IoT Edge 모듈 메타데이터를 사용할 수 있도록 준비합니다. 예를 들어 다음 자산을 준비합니다.
    - 제목
    - 설명(HTML 형식)
    - 로고 이미지(PNG 형식 및 40x40px, 90x90px, 115x115px, 255x115px를 포함한 고정 이미지 크기)
    - 사용 약관 및 개인정보처리방침
    - 경로, 쌍 desired 속성, createOptions, 환경 변수를 포함하는 기본 모듈 구성
    - 모듈 설명서
    - 지원 연락처


## <a name="next-steps"></a>다음 단계

[IoT Edge 모듈 기술 자산을 준비한](./cpp-create-technical-assets.md)후에는 [IoT Edge 모듈 오퍼를 만들](./cpp-create-offer.md)준비가 된 것입니다. 
