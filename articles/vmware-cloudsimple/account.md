---
title: 계정 관리-CloudSimple 포털 별 Azure VMware 솔루션
description: CloudSimple 포털을 통해 Azure VMware 솔루션에서 계정을 관리 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025370"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>CloudSimple 포털을 통해 Azure VMware 솔루션에서 계정 관리

CloudSimple 서비스를 만들 때 CloudSimple에 계정을 만듭니다. 이 계정은 서비스가 있는 Azure 구독과 연결 됩니다. 구독에서 소유자 및 참가자 역할이 있는 모든 사용자는 CloudSimple 포털에 액세스할 수 있습니다. CloudSimple 서비스와 연결 된 Azure 구독 ID 및 테 넌 트 ID는 계정 페이지에서 찾을 수 있습니다.

CloudSimple 포털에서 계정을 관리 하려면 [포털에 액세스](access-cloudsimple-portal.md) 하 고 측면 메뉴에서 **계정** 을 선택 합니다.

회사의 CloudSimple 구성에 대 한 정보를 보려면 **요약** 을 선택 합니다. 사설 클라우드 수, 총 저장소, vSphere 클러스터 구성, 노드 수, 계산 코어 수를 포함 하 여 클라우드 구성의 현재 용량이 표시 됩니다. 현재 구성이 모든 요구를 충족 하지 않는 경우 추가 노드를 구매 하기 위한 링크가 포함 됩니다.

## <a name="email-alerts"></a>메일 경고

사설 클라우드 구성 변경 내용에 대해 알릴 사용자의 메일 주소를 추가할 수 있습니다.

1. **추가 전자 메일 경고** 영역에서 **새로 추가**를 클릭 합니다.
2. 전자 메일 주소를 입력 합니다.
3. Return 키를 누릅니다.  

항목을 제거 하려면 **X**를 클릭 합니다.

## <a name="cloudsimple-operator-access"></a>CloudSimple operator access

운영자 액세스 설정을 사용 하면 지원 엔지니어가 CloudSimple 포털에 로그인 하도록 허용 하 여 클라우드 문제 해결에 도움이 됩니다.  설정은 기본적으로 사용 하도록 설정 되어 있습니다. 고객 계정에 로그인 할 때 지원 엔지니어가 수행 하는 모든 작업은 **활동**  >  **감사** 페이지에서 기록 되 고 검토에 사용할 수 있습니다.

**Cloudsimple operator access 사용** 설정/해제를 클릭 하 여 액세스를 설정 하거나 해제 합니다.
