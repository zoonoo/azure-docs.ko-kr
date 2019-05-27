---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127740"
---
### <a name="installation-failures"></a>설치 오류
| **샘플 오류 메시지** | **권장 작업** |
|--------------------------|------------------------|
|ERROR 계정을 로드하지 못했습니다. 오류: System.IO.IOException: CS 서버를 설치 및 등록할 때 전송 연결에서 데이터를 읽을 수 없습니다.| TLS 1.0이 컴퓨터에서 활성화되어 있는지 확인합니다. |

### <a name="registration-failures"></a>등록 오류
**%ProgramData%\ASRLogs** 폴더의 로그를 검토하여 등록 오류를 디버그할 수 있습니다

| **샘플 오류 메시지** | **권장 작업** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>메시지: ACS50008: SAML 토큰이 잘못되었습니다.<br>추적 ID: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>상관관계 ID: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>타임스탬프: **2016-12-12 14:50:08Z<br>** | 시스템 시계 시간이 현지 시간보다 15분 이상 늦지 않도록 합니다. 설치 관리자를 다시 실행하여 등록을 완료합니다.|
|**09:35:27**: 선택한 인증서::Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message에 대한 모든 재해 복구 자격 증명 모음을 가져오려고 하는 동안 DRRegistrationExceptione이 발생했습니다. ACS50008: SAML 토큰이 잘못되었습니다.<br>추적 ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>상관 관계 ID: abe9deb8-3e64-464d-8375-36db9816427a<br>타임스탬프: **2016-05-19 01:35:39Z**<br> | 시스템 시계 시간이 현지 시간보다 15분 이상 늦지 않도록 합니다. 설치 관리자를 다시 실행하여 등록을 완료합니다.|
|06:28:45: 인증서를 만들지 못했습니다.<br>06:28:45: 설치를 진행할 수 없습니다. Site Recovery를 인증하는 데 필요한 인증서를 만들 수 없습니다. 설치를 다시 실행합니다. | 로컬 관리자로 설치 프로그램을 실행하고 있는지 확인합니다. |
