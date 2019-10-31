---
title: CAF Migration 방문 영역 청사진 샘플 - 개요
description: CAF Migration 방문 영역 청사진 샘플의 개요 및 아키텍처입니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
ms.custom: fasttrack-new
ms.openlocfilehash: 28a6bbb21cfd32d21d5d2ffdcaf10faf1a1f8a9f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163541"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Azure에 대한 Microsoft 클라우드 채택 프레임워크 Migration 방문 영역 청사진 샘플의 개요

Azure에 대한 Microsoft CAF(클라우드 채택 프레임워크) Migration 방문 영역 청사진은 첫 번째 워크로드 마이그레이션을 설정하고 CAF와 연계하여 클라우드 자산을 관리하는 데 도움이 되는 인프라 세트입니다.

[CAF Foundation](../caf-foundation/index.md) 청사진 샘플에서는이 샘플을 확장합니다.

## <a name="architecture"></a>아키텍처

CAF Migration 방문 영역 청사진 샘플은 조직에서 가상 머신을 마이그레이션하기 위한 구독을 준비하는 데 사용할 수 있는 Azure의 파운데이션 인프라 리소스를 배포합니다. 또한 클라우드 자산을 관리하는 데 필요한 거버넌스 컨트롤을 배치하는 데도 도움이 됩니다. 이 샘플은 조직이 Azure를 안전하게 시작할 수 있도록 리소스, 정책 및 템플릿을 배포하고 적용합니다.

![CAF Migration 방문 영역, 이미지는 초기 방문 영역에 대한 CAF 지침의 일부로 설치되는 항목을 설명합니다. ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

이 환경은 안전하고 완벽하게 모니터링되는 엔터프라이즈 지원 거버넌스를 제공하는 데 사용되는 여러 가지 Azure 서비스로 구성됩니다. 이 환경은 다음과 같은 요소로 구성됩니다.

- 공유 서비스 환경에 배포된 인증서, 키 및 비밀에 사용되는 비밀을 호스팅하는 데 사용되는 [Azure Key Vault](../../../../key-vault/key-vault-overview.md) 인스턴스
- [Log Analytics](../../../../azure-monitor/overview.md) 배포 - 마이그레이션을 시작하는 순간부터 모든 작업과 서비스가 중앙 위치에 기록되도록 배포됩니다.
- [Azure Security Center](../../../../security-center/security-center-intro.md)(표준 버전) 배포 - 마이그레이션된 워크로드에 대한 위협 방지 기능을 제공합니다.
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) 배포 - 가상 머신에 대한 격리된 네트워크 및 서브넷을 제공합니다.
- [Azure Migrate 프로젝트](../../../..//migrate/migrate-overview.md) 배포 - 검색 및 평가용입니다. 서버 평가, 서버 마이그레이션, 데이터베이스 평가 및 데이터베이스 마이그레이션을 위한 도구를 추가하고 있습니다.  


이러한 모든 요소는 [Azure 아키텍처 센터 - 참조 아키텍처](/azure/architecture/reference-architectures/)에 게시된 검증된 사례를 따릅니다.

> [!NOTE]
> CAF Migration 청사진은 워크로드에 대한 방문 영역을 배치합니다. 이 기본 아키텍처를 기반으로 Virtual Machines/데이터베이스의 평가 및 마이그레이션을 수행해야 합니다.

더 자세한 내용은 [Azure에 대한 Microsoft 클라우드 채택 프레임워크 - 마이그레이션](/azure/architecture/cloud-adoption/migrate/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

CAF Migrate 방문 영역 청사진 샘플의 개요 및 아키텍처를 검토했습니다.

> [!div class="nextstepaction"]
>  [CAF Migration 방문 영역 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기