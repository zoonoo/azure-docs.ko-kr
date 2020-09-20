---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e75cb7d13fb74d32191ab7f076d73ad66976503d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606314"
---
Azure Files는 프리미엄, 트랜잭션 최적화, 핫, 쿨 등의 4가지 스토리지 계층을 제공하여 시나리오의 성능 및 가격 요구 사항에 맞게 공유를 조정할 수 있도록 지원합니다.

- **프리미엄**: 프리미엄 파일 공유는 SSD(반도체 드라이브)로 지원되며 **FileStorage 스토리지 계정** 유형에서 배포됩니다. 프리미엄 파일 공유는 IO 집약적 워크로드에서 대다수 IO 작업에 대해 한 자릿수 밀리 초 이내에 일관된 고성능과 짧은 지연 시간을 제공합니다. 프리미엄 파일 공유는 데이터베이스, 웹 사이트 호스팅, 개발 환경 등 다양한 워크로드에 적합합니다. 프리미엄 파일 공유는 SMB(서버 메시지 블록) 및 NFS(네트워크 파일 시스템) 프로토콜과 함께 사용할 수 있습니다.
- **트랜잭션 최적화**: 트랜잭션 최적화 파일 공유는 프리미엄 파일 공유에서 제공되는 대기 시간이 필요 없는 트랜잭션이 많은 워크로드를 지원합니다. 트랜잭션 최적화 파일 공유는 HDD(하드 디스크 드라이브)가 지원하는 표준 스토리지 하드웨어에서 제공되며 **GPv2(범용 버전 2) 스토리지 계정** 유형에서 배포됩니다. 트랜잭션 최적화는 이전에는 이 스토리지 계층을 "표준"이라고 불렀지만, 이는 계층 자체보다는 스토리지 미디어 유형을 의미합니다(핫 및 쿨 계층도 표준 스토리지 하드웨어에 있으므로 "표준" 계층입니다).
- **핫**: 핫 파일 공유는 팀 공유 및 Azure 파일 동기화 같은 범용 파일 공유 시나리오에 최적화된 스토리지를 제공합니다. 핫 파일 공유는 HDD가 지원하는 표준 스토리지 하드웨어에서 제공되며 **GPv2(범용 버전 2) 스토리지 계정** 유형에서 배포됩니다.
- **쿨**: 쿨 파일 공유는 온라인 보관 스토리지 시나리오에 최적화된 비용 효율적인 스토리지를 제공합니다. Azure 파일 동기화는 또한 변동이 적은 워크로드에 적합할 수 있습니다. 쿨 파일 공유는 HDD가 지원하는 표준 스토리지 하드웨어에서 제공되며 **GPv2(범용 버전 2) 스토리지 계정** 유형에서 배포됩니다.

프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다. 프리미엄 파일 공유의 프로비저닝된 청구 모델에 대한 자세한 내용은 [프리미엄 파일 공유에 대한 프로비저닝 이해](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)를 참조하세요. 트랜잭션 최적화, 핫 및 쿨 파일 공유를 포함한 표준 파일 공유는 종량제 청구를 통해 제공됩니다.

핫 및 쿨 파일 공유는 모든 Azure 공용 및 Azure Government 지역에서 사용할 수 있습니다. 트랜잭션 최적화 파일 공유는 국가 클라우드 지역을 포함한 모든 Azure 중국 및 Azure 독일 지역에서 사용할 수 있습니다.

핫 또는 쿨 파일 공유를 배포하려면 [핫 또는 쿨 파일 공유 만들기](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share)를 참조하세요. 