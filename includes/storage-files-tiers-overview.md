---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597838"
---
Azure Files는 프리미엄 및 표준의 두 가지 계층을 제공 하 여 시나리오의 성능 및 가격 요구 사항에 대 한 공유를 조정할 수 있도록 합니다.

- **프리미엄 파일 공유**: 프리미엄 파일 공유는 ssd (반도체 드라이브)에 의해 지원 되며 **FileStorage storage 계정** 형식으로 배포 됩니다. 프리미엄 파일 공유는 IO 집약적 워크 로드에 대해 대부분의 IO 작업에 대해 단일 자릿수 밀리초 내에 일관 된 고성능 및 짧은 대기 시간을 제공 합니다. 이를 통해 데이터베이스, 웹 사이트 호스팅, 개발 환경 등 다양 한 워크 로드에 적합 합니다. 프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다. 프리미엄 파일 공유에 대 한 프로 비전 된 청구 모델에 대 한 자세한 내용은 [프리미엄 파일 공유에 대 한 프로 비전 이해](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)를 참조 하세요.
- **표준 파일 공유**: 표준 파일 공유는 hdd (하드 디스크 드라이브)로 지원 되며 **GPv2 (범용 버전 2) 저장소 계정** 형식으로 배포 됩니다. 표준 파일 공유는 일반적인 용도의 파일 공유 및 개발/테스트 환경과 같은 성능 변동에 대 한 낮은 수준의 IO 작업 부하에 대 한 안정적인 성능을 제공 합니다. 종량제 청구 모델에서만 제공됩니다.