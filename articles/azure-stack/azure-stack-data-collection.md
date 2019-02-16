---
title: Azure Stack 로그 및 데이터 처리 | Microsoft Docs
description: Azure Stack에서 정보를 수집 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56318941"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack 로그 및 고객 데이터 처리 
*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*  

범위 내에 Microsoft는 프로세서 또는 Azure Stack과 관련 하 여 개인 데이터의 subprocessor, Microsoft는 모든 고객에 게 효과적인 25, 2018 년 5 월의 약정에 (a) 개인 데이터를 "처리 "데이터 보호 조건" 섹션의 GDPR"프로 비전 합니다 [Online Services 약관](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) 및 (b) 용어로 유럽 연합 일반 데이터 보호 규정의 첨부 파일 4에서 합니다 [Online Services 약관](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0)합니다. 

Microsoft만을 통해 Microsoft와 공유 되는 데이터의 데이터 컨트롤러는 Azure Stack 고객 데이터 센터에 있으므로 [진단](azure-stack-diagnostics.md)하십시오 [원격 분석](azure-stack-telemetry.md), 및 [청구](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>데이터 액세스 제어 
Microsoft 직원, 특정 지원 사례를 조사에 할당 되는 암호화 된 데이터에 대 한 읽기 전용 액세스를 부여 됩니다. Microsoft 직원에는 필요한 경우 데이터를 삭제 하려면 사용할 수 있는 도구에 액세스할을 수도 있습니다. 고객 데이터에 대 한 모든 액세스는 감사 및 기록 합니다.  

데이터 액세스 제어:
1.  데이터는 최대 90 일 동안 대/소문자를 닫으면만 유지 됩니다.
2.  고객 데이터를 언제 든 지 90 일 기간에서 제거를 항상 있습니다.
3.  Microsoft 직원 상황별으로의 데이터 액세스를 부여 하 고 지원 문제를 해결 하는 데 필요한 만큼만 합니다. 
4.  이벤트 Microsoft OEM 파트너와 고객 데이터를 공유 해야 고객 동의 필수입니다.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>데이터 주체 요청 (DSR) 컨트롤 수행 고객 있나요?
앞에서 설명한 대로 Microsoft 고객 요청에 따라 요청 시 데이터 삭제를 지원 합니다. 고객 지원 엔지니어는 언제 든 지 데이터는 영구적으로 삭제 하기 전에 고객을 선택 하는 지정된 된 경우에 대 한 모든 로그를 삭제 하도록 요청할 수 있습니다.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Microsoft은 데이터 삭제 될 때 고객에 게 알리지 않습니다.
자동화 된 데이터 삭제 작업 (90 일 경우 닫은 날)에 대해 수행 되지 사전에 고객에 게 연락 하 고 삭제에 대해 알릴 수 없습니다. 

요청 시 데이터 삭제 작업에 대 한 Microsoft 지원 엔지니어는 요청 시 데이터 삭제를 시작할 수 있습니다 하 고 제공할 수 있습니다 확인 휴대폰에서 고객과 함께 완료 되 면 도구에 액세스할 수 있습니다.

## <a name="diagnostic-data"></a>진단 데이터
지원 프로세스의 일부로, Azure Stack 운영자 수 [진단 로그 공유](azure-stack-diagnostics.md) 문제 해결 용이 하 게 Azure Stack 지원 및 엔지니어링 팀과 함께 합니다.

Microsoft 제공 도구 및 스크립트 수집 및 업로드 하는 고객에 대 한 진단 로그 파일을 요청 합니다. 수집 된 로그 파일을 통해 전송할지 HTTPS Microsoft에 암호화 된 연결을 보호 합니다. 없기 때문에 네트워크를 통해 암호화를 제공 하는 HTTPS, 전송 중에서 암호화에 필요한 암호가 없습니다. 수신 된 후 로그 암호화 되 고 지원 사례를 닫은 후 90 일 동안 자동으로 삭제 될 때 까지는 저장 합니다.

## <a name="telemetry-data"></a>원격 분석 데이터
[Azure Stack 원격 분석](azure-stack-telemetry.md) 시스템 데이터 연결 사용자 환경을 통해 Microsoft에 자동으로 업로드 합니다. Azure Stack 운영자는 언제 든 지 원격 분석 기능 및 개인 정보 설정을 사용자 지정 하는 컨트롤이 있습니다.

Microsoft는 신용 카드 번호, 사용자 이름 및 암호, 전자 메일 주소 또는 유사한 중요 한 정보 등의 중요 한 데이터를 수집 하려고 하지 않습니다. 중요 한 정보가 실수로 수신 확인을 삭제 합니다. 

## <a name="billing-data"></a>청구 데이터
[Azure Stack 청구](azure-stack-usage-reporting.md) 글로벌 Azure 청구 및 사용량 파이프라인 이므로 Microsoft 규정 준수 지침을 활용 합니다.

Azure Stack 운영자는 Azure Stack 청구에 대 한 azure 사용량 정보를 전달 하도록 구성할 수 있습니다. 이것이-수-종 청구 모델을 선택 하는 다중 노드 Azure Stack 고객에 대 한 필요 합니다. 사용 현황 보고 원격 분석에서 독립적으로 제어 됩니다 및 용량 모델을 선택 하는 다중 노드 고객 또는 Azure Stack Development Kit 사용자에 대해 필요 하지 않습니다. 이러한 시나리오에 대 한 사용 보고 설정 해제할 수 있습니다 사용 하 여 [등록 스크립트](azure-stack-usage-reporting.md)합니다.


## <a name="next-steps"></a>다음 단계 
[Azure Stack 보안에 자세히 알아보기](azure-stack-security-foundations.md) 
