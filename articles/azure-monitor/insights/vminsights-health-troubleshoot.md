---
title: VM용 Azure Monitor 게스트 상태 (미리 보기) 문제 해결
description: VM용 Azure Monitor 상태와 관련 된 문제가 있을 때 수행할 수 있는 문제 해결 단계를 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 048230bb85d2ac599efc6bd3fb326f98038cce57
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687021"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>VM용 Azure Monitor 게스트 상태 (미리 보기) 문제 해결
이 문서에서는 VM용 Azure Monitor 상태와 관련 된 문제가 있을 때 수행할 수 있는 문제 해결 단계를 설명 합니다.

## <a name="error-message-that-no-data-is-available"></a>데이터를 사용할 수 없다는 오류 메시지 

![데이터 없음](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>가상 머신이 구성 요구 사항을 충족 하는지 확인 합니다.

1. 가상 머신이 Azure 가상 머신 인지 확인 합니다. 서버에 대 한 Azure Arc는 현재 지원 되지 않습니다.
2. 가상 컴퓨터에서 지원 되는 [운영 체제](vminsights-health-enable.md?current-limitations.md)를 실행 하 고 있는지 확인 하십시오.
3. 가상 컴퓨터가 지원 되는 [지역](vminsights-health-enable.md?current-limitations.md)에 설치 되어 있는지 확인 하세요.
4. Log Analytics 작업 영역이 지원 되는 [지역](vminsights-health-enable.md?current-limitations.md)에 설치 되어 있는지 확인 합니다.

### <a name="verify-that-the-vm-is-properly-onboarded"></a>VM이 제대로 등록 확인
Azure Monitor 에이전트 확장 및 게스트 VM 상태 에이전트가 가상 머신에서 성공적으로 프로 비전 되었는지 확인 합니다. Azure Portal의 가상 컴퓨터 메뉴에서 **확장** 을 선택 합니다. 두 에이전트가 표시 되 면를 참조 하십시오 []() .

![VM 확장](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>가상 머신에서 시스템 할당 id가 사용 하도록 설정 되어 있는지 확인 합니다.
시스템 할당 id가 가상 머신에서 사용 하도록 설정 되어 있는지 확인 합니다. Azure Portal의 가상 컴퓨터 메뉴에서 **id** 를 선택 합니다. 두 에이전트가 표시 되 면를 참조 하십시오 []() .

![시스템 할당 ID](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>데이터 수집 규칙 확인
상태 확장을 데이터 원본으로 지정 하는 데이터 수집 규칙이 가상 머신과 연결 되어 있는지 확인 합니다.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>권한 부족으로 인해 잘못 된 요청에 대 한 오류 메시지
이 오류는 **WorkloadMonitor** 리소스 공급자가 구독에 등록 되지 않았음을 나타냅니다. 이 리소스 공급자를 등록 하는 방법에 대 한 자세한 내용은 [Azure 리소스 공급자 및 형식](/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider) 을 참조 하세요. 

![잘못된 요청](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>다음 단계

- [VM용 Azure Monitor의 게스트 상태 기능 개요를 확인 하세요.](vminsights-health-overview.md)
