---
title: 계정 관리 - 클라우드심플 포털별 Azure VMware 솔루션
description: CloudSimple 포털을 통해 Azure VMware 솔루션에서 계정을 관리하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025370"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>CloudSimple 포털을 통해 Azure VMware 솔루션의 계정 관리

CloudSimple 서비스를 만들 면 CloudSimple에 계정을 만듭니다. 계정이 서비스가 있는 Azure 구독과 연결됩니다. 구독에서 소유자 및 기여자 역할을 가진 모든 사용자는 CloudSimple 포털에 액세스할 수 있습니다. CloudSimple 서비스와 연결된 Azure 구독 ID 및 테넌트 ID는 계정 페이지에서 찾을 수 있습니다.

CloudSimple 포털에서 계정을 관리하려면 [포털에 액세스하고](access-cloudsimple-portal.md) 사이드 메뉴에서 **계정을** 선택합니다.

요약을 **Summary** 선택하여 회사의 CloudSimple 구성에 대한 정보를 봅니다. 프라이빗 클라우드 수, 총 스토리지, vSphere 클러스터 구성, 노드 수 및 계산 코어 수를 포함하여 클라우드 구성의 현재 용량이 표시됩니다. 현재 구성이 모든 요구 사항을 충족하지 못하는 경우 추가 노드를 구입하는 링크가 포함되어 있습니다.

## <a name="email-alerts"></a>메일 경고

프라이빗 클라우드 구성의 변경 사항에 대해 알리려는 모든 사람의 이메일 주소를 추가할 수 있습니다.

1. 추가 전자 메일 경고 영역에서 **새 위치 추가를** **클릭합니다.**
2. 이메일 주소를 입력합니다.
3. 반환을 누릅니다.  

항목을 제거하려면 **X를**클릭합니다.

## <a name="cloudsimple-operator-access"></a>클라우드 단순 연산자 액세스

운영자 액세스 설정을 사용하면 지원 엔지니어가 CloudSimple 포털에 로그인할 수 있도록 하여 문제 해결을 지원할 수 있습니다.  이 설정은 기본적으로 활성화되어 있습니다. 고객 계정에 로그인할 때 지원 엔지니어가 수행한 모든 작업이 기록되고 **활동** > **감사** 페이지에서 검토에 사용할 수 있습니다.

**CloudSimple 연산자 액세스를 클릭하여** 토글을 사용하여 액세스를 켜거나 끕니다.
