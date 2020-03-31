---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597838"
---
Azure Files는 시나리오의 성능 및 가격 요구 사항에 맞게 공유를 조정할 수 있도록 프리미엄 및 표준이라는 두 가지 계층의 저장소를 제공합니다.

- **프리미엄 파일 공유**: 프리미엄 파일 공유는 솔리드 스테이트 드라이브(SSD)에 의해 지원되며 **FileStorage 저장소 계정** 유형에 배포됩니다. 프리미엄 파일 공유는 IO 집약적인 워크로드를 위해 대부분의 IO 작업에 대해 한 자리 수 밀리초 내에 일관된 고성능 및 낮은 대기 시간을 제공합니다. 따라서 데이터베이스, 웹 사이트 호스팅 및 개발 환경과 같은 다양한 워크로드에 적합합니다. 프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다. 프리미엄 파일 공유에 대한 프로비저닝된 청구 모델에 대한 자세한 내용은 [프리미엄 파일 공유에 대한 프로비저닝 이해를](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)참조하십시오.
- **표준 파일 공유**: 표준 파일 공유는 하드 디스크 드라이브(HDD)에 의해 지원되며 **범용 버전 2(GPv2) 저장소 계정** 유형에 배포됩니다. 표준 파일 공유는 범용 파일 공유 및 개발/테스트 환경과 같은 성능 가변성에 덜 민감한 IO 워크로드에 안정적인 성능을 제공합니다. 종량제 청구 모델에서만 제공됩니다.