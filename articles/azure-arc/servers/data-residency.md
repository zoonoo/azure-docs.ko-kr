---
title: 데이터 상주
description: 데이터 상주 및 Azure Arc 사용 서버에 대 한 정보입니다.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559508"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc 사용 서버: 데이터 상주

이 문서에서는 데이터 상주의 개념과 Azure Arc 사용 서버에 적용 되는 방식을 설명 합니다.

Azure Arc 사용 서버는 **미국, 유럽, 영국, 오스트레일리아 및 아시아 태평양** 에서 **[사용할 수](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** 있습니다.

## <a name="data-residency"></a>데이터 상주

Azure Arc 사용 서버는 연결 된 컴퓨터에서을 사용 하도록 설정 하기 전에 확장을 지정 해야 하는 [AZURE VM 확장](manage-vm-extensions.md) 구성 설정 (즉, 속성 값)을 저장 합니다. 예를 들어 Log Analytics VM 확장을 사용 하도록 설정 하면 Log Analytics **작업 영역 ID** 및 **기본 키** 를 요청 합니다.

연결 된 컴퓨터에 대 한 메타 데이터 정보도 수집 됩니다. 특히 다음에 대한 내용을 설명합니다.

* 운영 체제 이름, 유형 및 버전
* 컴퓨터 이름
* 컴퓨터 FQDN(정규화된 도메인 이름)
* Connected Machine 에이전트 버전
* Active Directory 및 DNS FQDN (정규화 된 도메인 이름)
* UUID (BIOS ID)
* 연결 된 컴퓨터 에이전트 하트 비트
* Connected Machine 에이전트 버전
* 관리 id에 대 한 공개 키
* 정책 준수 상태 및 세부 정보 (Azure Policy 게스트 구성 정책을 사용 하는 경우)

Arc 사용 서버를 사용 하면 데이터가 저장 될 지역을 지정할 수 있습니다. Microsoft는 데이터 복원 력을 위해 다른 지역에 복제 될 수 있지만, Microsoft는 지리 외부에서 데이터를 복제 하거나 이동 하지 않습니다. 이 데이터는 Azure Arc 컴퓨터 리소스가 구성 된 지역에 저장 됩니다. 예를 들어 컴퓨터가 미국 동부 지역의 호에 등록 된 경우이 데이터는 미국 지역에 저장 됩니다.

지역 복원 력 및 규정 준수 지원에 대 한 자세한 내용은 [Azure 지리](https://azure.microsoft.com/global-infrastructure/geographies/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure 복원 력](/azure/architecture/reliability/architect)설계에 대해 자세히 알아보세요.
