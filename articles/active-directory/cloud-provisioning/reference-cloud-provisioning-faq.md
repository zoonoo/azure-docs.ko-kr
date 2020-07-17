---
title: Azure AD Connect 클라우드 프로비저닝 FAQ
description: 이 문서에서는 클라우드 프로비저닝에 대한 질문과 대답을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef482804c80602771963633bcc46feaf014c363
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373831"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect FAQ

Azure AD(Azure Active Directory) Connect 클라우드 프로비저닝에 대한 질문과 대답을 참조하세요.

## <a name="general-installation"></a>일반 설치

**Q: 클라우드 프로비저닝은 얼마나 자주 실행되나요?**

클라우드 프로비저닝은 2분마다 실행되도록 예약되어 있습니다. 2분마다 모든 사용자, 그룹 및 암호 해시 변경 내용이 Azure AD에 프로비저닝됩니다.

**Q: 첫 번째 실행에서 암호 해시 동기화 실패가 표시됩니다. 그 이유는 무엇일까요?**

예상된 동작입니다. 이 실패는 Azure AD에 없는 사용자 개체로 인해 발생한 것입니다. 사용자가 Azure AD에 프로비저닝되면 이후 실행에서 암호 해시를 프로비저닝해야 합니다. 몇 번의 실행을 기다린 후에 암호 해시 동기화에 오류가 더 이상 없는지 확인합니다.

**Q: Active Directory 인스턴스에 클라우드 프로 비전에서 지원 하지 않는 특성 (예: 디렉터리 확장)이 있는 경우 어떻게 되나요?**

클라우드 프로비저닝은 지원되는 특성을 실행하고 프로비저닝합니다. 지원되지 않는 특성은 Azure AD에 프로비저닝되지 않습니다. Active Directory에서 디렉터리 확장을 검토 하 고 Azure AD로 이동 하는 이러한 특성이 필요 하지 않은지 확인 합니다. 하나 이상의 특성이 필요한 경우 Azure AD Connect 동기화를 사용하거나 필요한 정보를 지원하는 특성 중 하나(예: 확장 특성 1-15)로 이동합니다.

**Q: Azure AD Connect 동기화와 클라우드 프로비저닝의 차이점은 무엇인가요?**

Azure AD Connect 동기화를 사용하면 온-프레미스 동기화 서버에서 프로비저닝이 실행됩니다. 구성은 온-프레미스 동기화 서버에 저장됩니다. Azure AD Connect 클라우드 프로비저닝을 사용하면 프로비저닝 구성이 클라우드에 저장되고 Azure AD 프로비저닝 서비스의 일부로 클라우드에서 실행됩니다. 

**Q: 클라우드 프로비저닝을 사용하여 여러 Active Directory 포리스트에서 동기화할 수 있나요?**

예. 클라우드 프로비저닝을 사용하여 여러 Active Directory 포리스트에서 동기화할 수 있습니다. 다중 포리스트 환경에서는 모든 참조(예: 관리자)가 도메인 내에 있어야 합니다.  

**Q: 에이전트를 업데이트하려면 어떻게 하나요?**

에이전트는 Microsoft에서 자동으로 업그레이드합니다. IT 팀의 경우 이를 통해 새 에이전트 버전을 테스트하고 유효성을 검사해야 하는 부담을 줄일 수 있습니다. 

**Q: 자동 업그레이드를 사용하지 않도록 설정할 수 있나요?**

자동 업그레이드를 사용하지 않도록 설정하는 방법은 지원되지 않습니다.

**Q: 클라우드 프로비저닝에 맞게 원본 앵커를 변경할 수 있나요?**

클라우드 프로비저닝은 기본적으로 ObjectGUID를 원본 앵커로 대체한 ms-ds-consistency-GUID를 사용합니다. 원본 앵커를 변경하는 방법은 지원되지 않습니다.

**Q: 클라우드 프로비저닝을 사용하는 경우 AD 도메인 이름이 있는 새 서비스 주체가 표시됩니다. 필요한가요?**

예, 클라우드 프로비저닝은 도메인 이름을 서비스 주체 이름으로 사용하여 프로비저닝 구성에 대한 서비스 주체를 만듭니다. 서비스 주체 구성은 변경하지 마세요.

**Q: 다음 번에 로그온 할 때 동기화된 사용자가 암호를 변경해야 하는 경우 어떻게 되나요?**

클라우드 프로 비전에서 암호 해시 동기화를 사용 하도록 설정 하 고 동기화 된 사용자가 온-프레미스 AD에서 다음 로그온 할 때 암호를 변경 해야 하는 경우, 클라우드 프로 비전은 Azure AD에 "변경 되지 않은" 암호 해시를 프로 비전 하지 않습니다. 사용자가 암호를 변경하면 사용자 암호 해시가 AD에서 Azure AD로 프로비저닝됩니다.

**Q: 클라우드 프로비저닝에서 모든 개체에 대해 ms-ds-consistencyGUID의 쓰기 저장을 지원하나요?**

아니요, 클라우드 프로비저닝은 모든 개체(사용자 개체 포함)에 대해 ms-ds-consistencyGUID의 쓰기 저장을 지원하지 않습니다. 

**Q: 클라우드 프로비저닝을 사용하여 사용자를 프로비저닝하지만, 구성을 삭제했습니다. Azure AD에서 이전에 동기화된 개체가 계속 표시되는 이유는 무엇인가요?** 

구성을 삭제 하는 경우 클라우드 프로 비전은 Azure AD에서 동기화 된 개체를 자동으로 제거 하지 않습니다. 이전 개체가 없는지 확인하려면 구성 범위를 빈 그룹 또는 조직 구성 단위로 변경합니다. 프로비저닝이 실행되고 개체가 정리되면 구성을 사용하지 않도록 설정하고 삭제합니다. 

**Q:  Exchange 하이브리드가 지원되지 않는다는 것은 무슨 의미인가요?**

Exchange 하이브리드 배포 기능을 통해 온-프레미스 및 Office 365에서 모두 Exchange 사서함을 동시에 존재하게 할 수 있습니다. Azure AD Connect에서는 특성의 특정 세트를 Azure AD에서 온-프레미스 디렉터리로 다시 동기화합니다.  클라우드 프로비저닝 에이전트는 현재 이러한 특성을 온-프레미스 디렉터리로 다시 동기화하지 않으므로 Azure AD Connect의 대체 항목으로 지원되지 않습니다.

**Q:  Windows Server Core에 클라우드 프로저닝 에이전트를 설치할 수 있나요?**

아니요, Server Core에 에이전트를 설치할 수 없습니다.

**Q: 클라우드 프로 비전 에이전트에서 준비 서버를 사용할 수 있나요?**

아니요, 스테이징 서버는 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
