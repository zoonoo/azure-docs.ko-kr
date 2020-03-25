---
title: CAF Foundation 청사진 샘플 개요
description: Azure에 대한 CAF(Cloud Adoption Framework) Foundation 청사진 샘플의 개요 및 아키텍처입니다.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: 79b47127b81caf148054f4223e5fa7ad9fbf86fe
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2020
ms.locfileid: "75562564"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Azure에 대한 Microsoft 클라우드 채택 프레임워크 Foundation 청사진 샘플

Azure에 대한 Microsoft CAF(클라우드 채택 프레임워크) Foundation 청사진은 첫 번째 프로덕션 등급 Azure 애플리케이션에 필요한 핵심 인프라 리소스 및 정책 컨트롤 세트를 배포합니다. 이 파운데이션 청사진은 CAF에 있는 권장 패턴을 기반으로 합니다.

## <a name="architecture"></a>Architecture

CAF Foundation 청사진 샘플은 조직에서 클라우드 자산을 관리하는 데 필요한 기본 컨트롤을 배치하는 데 사용할 수 있는 권장 인프라 리소스를 Azure에 배포합니다. 이 샘플은 조직이 Azure를 안전하게 시작할 수 있도록 리소스, 정책 및 템플릿을 배포하고 적용합니다.

![CAF Foundation, 이미지는 Azure를 시작하기 위한 기초를 만들기 위해 CAF 지침의 일부로 설치되는 항목을 설명합니다.](../../media/caf-blueprints/caf-foundation-architecture.png)

이 구현에서는 안전하고 완벽하게 모니터링되는 엔터프라이즈 지원 파운데이션을 제공하는 데 사용되는 여러 Azure 서비스를 통합합니다. 이 환경은 다음과 같은 요소로 구성됩니다.

- 공유 서비스 환경에 배포된 VM에 사용되는 비밀을 호스팅하는 데 사용되는 [Azure Key Vault](../../../../key-vault/key-vault-overview.md) 인스턴스
- [Log Analytics](../../../../azure-monitor/overview.md) 배포 - 진단 로깅을 위해 [스토리지 계정](../../../../storage/common/storage-introduction.md)에 보안 배포를 시작하는 순간부터 모든 작업과 서비스가 중앙 위치에 기록되도록 배포됩니다.
- [Azure Security Center](../../../../security-center/security-center-intro.md)(표준 버전) 배포 - 마이그레이션된 워크로드에 대한 위협 방지 기능을 제공합니다.
- 또한 청사진은 [Azure 정책](../../../policy/overview.md)을 정의하고 배포합니다. 
  - 리소스 그룹에 적용된 태그 지정(CostCenter)
  - CostCenter 태그를 사용하여 리소스 그룹에 리소스 추가
  - 리소스 및 리소스 그룹에 허용되는 Azure 지역
  - 허용되는 스토리지 계정 SKU(배포 중 선택)
  - 허용되는 Azure VM SKU(배포 중 선택)
  - 네트워크 감시를 배포해야 함 
  - Azure Storage 계정 보안 전송 암호화 필요
  - 리소스 유형 거부(배포 중 선택)  
- 이니셔티브
  - Azure Security Center에서 모니터링 사용(89 정책)

이러한 모든 요소는 [Azure 아키텍처 센터 - 참조 아키텍처](/azure/architecture/reference-architectures/)에 게시된 검증된 사례를 따릅니다.

> [!NOTE]
> CAF Foundation은 워크로드에 대한 기본 아키텍처를 배치합니다.
> 이러한 기본 아키텍처 뒤에 워크로드를 배포해야 합니다.

더 자세한 내용은 [Azure에 대한 Microsoft 클라우드 채택 프레임워크 - 준비](/azure/cloud-adoption-framework/ready/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

CAF Foundation 청사진 샘플의 개요 및 아키텍처를 검토했습니다.

> [!div class="nextstepaction"]
> [CAF Foundation 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
