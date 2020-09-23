---
title: Azure 센티널의 Advanced 다단계 공격 감지
description: Azure 센티널의 Fusion 기술을 사용 하 여 경고 피로를 줄이고 고급 다단계 공격 감지를 기반으로 하는 조치 가능한 인시던트를 만듭니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: ba872f221f3bde29f0bb48b04dc2259d3ab4938a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906287"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure 센티널의 Advanced 다단계 공격 감지


> [!IMPORTANT]
> Azure 센티널의 일부 Fusion 기능은 현재 **공개 미리 보기로**제공 됩니다.
> 이러한 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

기계 학습을 기반으로 하는 Fusion 기술을 사용 하 여 Azure 센티널은 kill 체인의 다양 한 단계에서 관찰 되는 비정상 동작 및 의심 스러운 활동의 조합을 식별 하 여 다단계 공격을 자동으로 감지할 수 있습니다. 이러한 검색을 기준으로 Azure 센티널은 catch 하기 어려운 인시던트를 생성 합니다. 이러한 인시던트는 둘 이상의 경고 또는 활동으로 구성 됩니다. 기본적으로 이러한 인시던트는 볼륨이 낮고, 높은 충실도 이며, 높은 심각도입니다.

사용자 환경에 맞게 사용자 지정 된이 검색 기술은 거짓 긍정 요금을 줄일 뿐만 아니라 제한 되거나 누락 된 정보를 사용 하 여 공격을 검색할 수도 있습니다.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>고급 다단계 공격 검색 구성

이 검색은 기본적으로 Azure 센티널에서 사용 하도록 설정 됩니다. 상태를 확인 하거나, 대체 솔루션을 사용 하 여 여러 경고를 기반으로 인시던트를 만드는 경우 해당 상태를 사용 하지 않도록 설정 하려면 다음 지침을 따르십시오.

1. 아직 수행 하지 않은 경우 [Azure Portal](https://portal.azure.com)에 로그인 합니다.

1. **Azure 센티널**  >  **구성**  >  **분석** 으로 이동 합니다.

1. **활성 규칙**을 선택한 다음 **Fusion** 규칙 유형에 대 한 목록을 필터링 하 여 **이름** 열에서 **고급 다단계 공격 감지** 를 찾습니다. **상태** 열을 확인 하 여이 검색이 사용 되는지 여부를 확인 합니다.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-텍스트}":::

1. 상태를 변경 하려면이 항목을 선택 하 고 **Advanced 다단계 Attack 검색** 블레이드에서 **편집**을 선택 합니다.

1. **규칙 만들기 마법사** 블레이드에서 상태 변경이 자동으로 선택 되므로 **다음: 검토**를 선택 하 고 **저장**을 선택 합니다. 

 **Fusion** 규칙 형식에는 수정할 수 없는 규칙이 하나만 포함 되어 있으므로이 규칙 형식에는 규칙 템플릿을 적용할 수 없습니다.

> [!NOTE]
> Azure 센티널은 현재 30 일간의 기록 데이터를 사용 하 여 기계 학습 시스템을 학습 합니다. 이 데이터는 machine learning 파이프라인을 통과할 때 Microsoft의 키를 사용 하 여 항상 암호화 됩니다. 그러나 Azure 센티널 작업 영역에서 CMK를 사용 하도록 설정한 경우에는 Azure [CMK (고객 관리 키)](customer-managed-keys.md) 를 사용 하 여 학습 데이터를 암호화 하지 않습니다. Fusion에서 옵트아웃 (opt out) 하려면 **Azure 센티널**   \>  **구성**   \>  **분석 \> 활성 규칙 \> 고급 다단계 공격 감지** 로 이동 하 고 **상태** 열에서 **사용 안 함을 선택 합니다.**

## <a name="attack-detection-scenarios"></a>공격 검색 시나리오

다음 섹션에서는 Azure 센티널에서 Fusion 기술을 사용 하 여 검색 하는 위협 분류 별로 그룹화 된 상관 관계 시나리오의 유형을 나열 합니다.

위에서 언급 한 것 처럼 Fusion은 다양 한 제품의 여러 보안 경고를 서로 연결 하 여 고급 다단계 공격을 감지 하므로 성공적인 Fusion 검색은 Azure 센티널 **인시던트** 페이지에서 **fusion 인시던트** 로 표시 되 고 **로그**의 **보안 경고** 테이블에는 **경고가** 표시 되지 않습니다.

이러한 Fusion 지원 공격 검색 시나리오를 사용 하도록 설정 하려면 연결 된 Azure 센티널 데이터 커넥터를 사용 하 여 나열 된 모든 데이터 원본을 수집 해야 합니다.

> [!NOTE]
> 이러한 시나리오 중 일부는 **공개 미리 보기로**제공 됩니다. 표시 됩니다.

## <a name="compute-resource-abuse"></a>리소스 남용 계산

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>의심 스러운 Azure Active Directory 로그인 후 여러 VM 만들기 작업
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 영향 

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 리소스 하이재킹 (T1496)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 의심 스러운 로그인 후 단일 세션에서 비정상적인 Vm 수가 생성 되었음을 나타냅니다. 이 유형의 경고는 높은 신뢰도로 Fusion 인시던트 설명에 명시 된 계정이 손상 되어 암호화 마이닝 작업 실행과 같이 무단으로 새 Vm을 만드는 데 사용 됨을 나타냅니다. 여러 VM 만들기 작업 경고를 사용 하 여 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **여러 VM 만들기 작업으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **여러 VM 만들기 작업으로 이어지는 잘 모르는 위치에서 로그인 이벤트**

- **여러 VM 만들기 작업을 시작 하는 감염 된 장치에서의 로그인 이벤트**

- **여러 VM 만들기 작업으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **여러 VM 만들기 작업으로 이어지는 유출 자격 증명이 있는 사용자의 로그인 이벤트**

## <a name="data-exfiltration"></a>데이터 반출

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 후 Office 365 사서함 반출

**MITRE at&t&접시 헤드 전술:** 초기 액세스, Exfiltration, 컬렉션

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 전자 메일 수집 (T1114), 자동화 된 Exfiltration (T1020)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 의심 스러운 로그인을 수행 하 여 사용자의 받은 편지함에 의심 스러운 수신함 전달 규칙이 설정 되었음을 나타냅니다. 이 표시는 사용자 계정 (Fusion 인시던트 설명에 표시 됨)이 손상 되었으며, 실제 사용자의 지식 없이 사서함 전달 규칙을 사용 하도록 설정 하 여 조직의 네트워크에서 데이터를 탈취 하는 데 사용 되었다는 확신을 제공 합니다. Office 365 사서함 반출 경고를 사용 하 여 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **Office 365 사서함 반출으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **Office 365 사서함 반출으로 이어지는 익숙하지 않은 위치의 로그인 이벤트**

- **Office 365 사서함 반출으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **Office 365 사서함 반출으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **Office 365 사서함 반출을 사용 하는 누출 자격 증명이 있는 사용자의 로그인 이벤트**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>대량 파일 다운로드 의심 스러운 Azure AD 로그인

**MITRE at&t&접시 헤드 전술:** 초기 액세스, Exfiltration

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 의심 스러운 로그인 후 사용자가 비정상적인 파일 수를 다운로드 했음을 나타냅니다. 이 표시는 Fusion 인시던트 설명에 명시 된 계정이 손상 되었으며 조직의 네트워크에서 데이터를 탈취 하는 데 사용 되었다는 확신을 제공 합니다. 대량 파일 다운로드 경고가 발생 한 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **대용량 파일 다운로드로 이어지는 불규칙 한 위치로 이동 불가능**

- **대량 파일 다운로드를 위한 익숙하지 않은 위치의 로그인 이벤트**

- **대량 파일 다운로드를 위한 감염 된 장치에서의 로그인 이벤트**

- **대량 파일 다운로드에 대 한 익명 IP의 로그인 이벤트**

- **대량 파일 다운로드를 위한 누출 자격 증명이 있는 사용자의 로그인 이벤트**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 다음에 대량 파일 공유

**MITRE at&t&접시 헤드 전술:** 초기 액세스, Exfiltration

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 웹 서비스를 통한 Exfiltration (T1567)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 특정 임계값을 초과 하는 파일 수가 Azure AD 계정에 대 한 의심 스러운 로그인을 따라 다른 사용자에 게 공유 되었음을 나타냅니다. 이 표시는 인증 되지 않은 사용자가 악의적인 목적으로 문서, 스프레드시트 등의 파일을 공유 하 여 조직 네트워크에서 데이터를 탈취 하는 데 사용할 수 있는 높은 신뢰도를 제공 합니다. 대용량 파일 공유 경고를 사용 하 여 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **대용량 파일 공유로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **대량 파일 공유에 대 한 익숙하지 않은 위치의 로그인 이벤트**

- **대량 파일 공유에 대 한 감염 된 장치에서의 로그인 이벤트**

- **대량 파일 공유로 향하는 익명 IP 주소의 로그인 이벤트**

- **대용량 파일 공유에 대 한 유출 된 자격 증명이 있는 사용자의 로그인 이벤트**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>의심 스러운 수신함 조작 규칙이 의심 스러운 Azure AD 로그인 다음에 설정 되었습니다.
이 시나리오는 **데이터 반출** 및 **측면 이동**이라는 두 가지 위협 분류에 속합니다. 명확성을 위해 두 섹션에 모두 표시 됩니다.

이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 측면 이동, Exfiltration

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 내부 스피어 피싱 (T1534)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 대 한 의심 스러운 로그인을 수행 하 여 사용자의 받은 편지함에 비정상적인 수신함 규칙이 설정 되었음을 나타냅니다. 이는 Fusion 인시던트 설명에 표시 된 계정이 손상 되었으며 악의적인 목적을 위해 사용자의 전자 메일 받은 편지함 규칙을 조작 하는 데 사용 되었음을 나타내는 높은 신뢰도를 제공 합니다. 공격자가 조직의 네트워크에서 데이터를 탈취 하는 것일 수 있습니다. 또는 공격자가 추가 사용자 및/또는 권한 있는 계정에 액세스 하 여 수평을 이동 하기 위해 조직 내에서 피싱 메일을 생성 (외부 원본에서 전자 메일을 대상으로 하는 피싱 검색 메커니즘 무시) 하려고 할 수 있습니다. 의심 스러운 수신함 조작 규칙 경고에 대 한 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **의심 스러운 받은 편지함 조작 규칙으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **의심 스러운 받은 편지함 조작 규칙으로 이어지는 잘 모르는 위치에서 로그인 이벤트**

- **의심 스러운 수신함 조작 규칙으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **의심 스러운 받은 편지함 조작 규칙으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **의심 스러운 자격 증명이 있는 사용자의 로그인 이벤트 (의심 스러운 수신함 조작 규칙)**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 다음에 공유 작업을 공유 하는 여러 Power BI 보고서 
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, Exfiltration 

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 웹 서비스를 통한 Exfiltration (T1567)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 비정상적인 수의 Power BI 보고서가 Azure AD 계정에 대 한 의심 스러운 로그인 후 단일 세션에서 공유 되었음을 나타냅니다. 이 표시는 Fusion 인시던트 설명에 명시 된 계정이 손상 되었으며 권한이 없는 사용자와 Power BI 보고서를 악의적인 목적으로 공유 하 여 조직의 네트워크에서 데이터를 탈취 하는 데 사용 되었음을 나타냅니다. 여러 Power BI 보고서 공유 작업으로 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **여러 Power BI 보고서 공유 활동으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **여러 Power BI 보고서 공유 활동으로 이어지는 잘 모르는 위치에서 로그인 이벤트**

- **여러 Power BI 보고서 공유 작업을 위한 감염 된 장치에서의 로그인 이벤트**

- **여러 Power BI 보고서 공유 활동으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **여러 Power BI 보고서 공유 작업으로 이어지는 사용자의 로그인 이벤트**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Power BI 보고서 공유 의심 스러운 Azure AD 로그인
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, Exfiltration 

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 웹 서비스를 통한 Exfiltration (T1567)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 의심 스러운 로그인 후에 의심 스러운 Power BI 보고서 공유 활동이 발생 했음을 나타냅니다. Power BI 보고서는 자연어 처리를 사용 하 여 식별 된 중요 한 정보를 포함 하 고 있으며, 외부 메일 주소와 공유 하거나, 웹에 게시 하거나, 외부에서 구독 한 전자 메일 주소에 스냅숏으로 배달 했기 때문에 공유 활동은 의심 스러운 것으로 식별 되었습니다. 이 경고는 Fusion 인시던트 설명에 명시 된 계정이 손상 되었으며 권한이 없는 사용자와 악성 목적을 위해 Power BI 보고서를 공유 하 여 조직의 중요 한 데이터를 탈취 하는 데 사용 되었다는 확신 함을 나타냅니다. 의심 스러운 Azure AD 로그인 경고의 순열은 의심 스러운 Power BI 보고서 공유에 있습니다.  

- **의심 스러운 위치로 이동 하 여 의심 스러운 Power BI 보고서 공유로 이동할 수 없습니다.**

- **의심 스러운 위치에서 의심 스러운 Power BI 보고서 공유에 대 한 로그인 이벤트**

- **의심 스러운 Power BI 보고서 공유에 대 한 감염 된 장치에서의 로그인 이벤트**

- **의심 스러운 Power BI 보고서 공유로 향하는 익명 IP 주소의 로그인 이벤트**

- **의심 스러운 자격 증명이 있는 사용자의 로그인 이벤트로 의심 되는 Power BI 보고서 공유**

## <a name="data-destruction"></a>데이터 소멸

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 후 대량 파일 삭제

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 영향

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 데이터 소멸 (T1485)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 대 한 의심 스러운 로그인을 수행 하 여 비정상적인 개수의 고유 파일이 삭제 되었음을 나타냅니다. 이는 Fusion 인시던트 설명에 표시 된 계정이 손상 되었을 수 있으며 악의적인 목적을 위해 데이터를 제거 하는 데 사용 되었음을 나타냅니다. 대량 파일 삭제 경고가 발생 한 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **대용량 파일 삭제로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **대량 파일 삭제를 위한 익숙하지 않은 위치의 로그인 이벤트**

- **대량 파일 삭제를 위한 감염 된 장치에서의 로그인 이벤트**

- **대량 파일 삭제로 이어지는 익명 IP 주소의 로그인 이벤트**

- **대량 파일 삭제를 초래 하는 누출 자격 증명이 있는 사용자의 로그인 이벤트**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 후 의심 스러운 이메일 삭제 활동
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 영향 

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 데이터 소멸 (T1485)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 대 한 의심 스러운 로그인 후에 단일 세션에서 비정상적인 메일 수를 삭제 했음을 나타냅니다. 이는 Fusion 인시던트 설명에 언급 된 계정이 손상 되었을 수 있으며, 조직을 저하 하거나 스팸 관련 전자 메일 작업을 숨기는 등 악의적인 목적으로 데이터를 제거 하는 데 사용 되었음을 나타냅니다. 의심 스러운 이메일 삭제 활동 경고와 함께 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.   

- **의심 스러운 이메일 삭제 활동으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **의심 스러운 이메일 삭제 활동으로 이어지는 익숙하지 않은 위치에서 로그인 이벤트**

- **의심 스러운 이메일 삭제 활동으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **의심 스러운 이메일 삭제 활동으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **의심 스러운 자격 증명이 있는 사용자의 로그인 이벤트를 의심 스러운 전자 메일 삭제 작업으로 보냅니다.**

## <a name="denial-of-service"></a>서비스 거부

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인을 수행 하는 여러 VM 삭제 작업
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 영향

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 끝점 거부 서비스 (T1499)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 의심 스러운 로그인 후에 단일 세션에서 비정상 개수의 Vm이 삭제 되었음을 나타냅니다. 이 표시는 Fusion 인시던트 설명에 명시 된 계정이 손상 되었으며 조직의 클라우드 환경을 방해 하거나 제거 하는 데 사용 되었다는 확신을 제공 합니다. 여러 VM 삭제 작업 경고를 사용 하 여 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **여러 VM 삭제 작업으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **여러 VM 삭제 작업으로 이어지는 잘 모르는 위치에서 로그인 이벤트**

- **여러 VM 삭제 작업으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **여러 VM 삭제 작업으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **여러 VM 삭제 작업으로 이어지는 유출 자격 증명이 있는 사용자의 로그인 이벤트**

## <a name="lateral-movement"></a>수평 이동

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Office 365 가장 의심 스러운 Azure AD 로그인

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 측면 이동

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 내부 스피어 피싱 (T1534)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에서 의심 스러운 로그인을 수행 하 여 비정상적인 수의 가장 동작이 발생 했음을 나타냅니다. 일부 소프트웨어에는 사용자가 다른 사용자를 가장할 수 있도록 허용 하는 옵션이 있습니다. 예를 들어 전자 메일 서비스를 사용 하 여 사용자는 다른 사용자에 게 전자 메일을 보낼 수 있는 권한을 부여할 수 있습니다. 이 경고는 Fusion 인시던트 설명에 명시 된 계정이 손상 되었으며 맬웨어 배포 또는 측면 이동에 대 한 피싱 전자 메일 전송과 같은 악의적 목적을 위해 가장 활동을 수행 하는 데 사용 되었음을 확신 함을 나타냅니다. Office 365 가장 경고에 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **Office 365 가장으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **Office 365 가장으로 이어지는 익숙하지 않은 위치의 로그인 이벤트**

- **Office 365 가장으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **Office 365 가장으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **Office 365 가장에 대 한 유출 자격 증명이 있는 사용자의 로그인 이벤트**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>의심 스러운 수신함 조작 규칙이 의심 스러운 Azure AD 로그인 다음에 설정 되었습니다.
이 시나리오는이 목록에서 **측면 이동** 및 **데이터 반출**이라는 두 가지 위협 분류에 속합니다. 명확성을 위해 두 섹션에 모두 표시 됩니다.

이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 측면 이동, Exfiltration

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 내부 스피어 피싱 (T1534), 자동화 된 Exfiltration (T1020)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 대 한 의심 스러운 로그인을 수행 하 여 사용자의 받은 편지함에 비정상적인 수신함 규칙이 설정 되었음을 나타냅니다. 이 표시는 Fusion 인시던트 설명에 명시 된 계정이 손상 되었으며 악의적인 목적을 위해 사용자의 전자 메일 받은 편지함 규칙을 조작 하는 데 사용 되었다는 확신을 제공 합니다. 공격자가 조직의 네트워크에서 데이터를 탈취 하는 것일 수 있습니다. 또는 공격자가 추가 사용자 및/또는 권한 있는 계정에 액세스 하 여 수평을 이동 하기 위해 조직 내에서 피싱 메일을 생성 (외부 원본에서 전자 메일을 대상으로 하는 피싱 검색 메커니즘 무시) 하려고 할 수 있습니다. 의심 스러운 수신함 조작 규칙 경고에 대 한 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **의심 스러운 받은 편지함 조작 규칙으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **의심 스러운 받은 편지함 조작 규칙으로 이어지는 잘 모르는 위치에서 로그인 이벤트**

- **의심 스러운 수신함 조작 규칙으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **의심 스러운 받은 편지함 조작 규칙으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **의심 스러운 자격 증명이 있는 사용자의 로그인 이벤트 (의심 스러운 수신함 조작 규칙)**

## <a name="malicious-administrative-activity"></a>악의적인 관리 활동

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 다음에 의심 스러운 클라우드 앱 관리 활동

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 지 속성, 방어 Evasion, 측면 이동, 컬렉션, Exfiltration 및 영향

**MITRE at&t&헤드 기술:** 해당 없음

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 동일한 계정에서 의심 스러운 Azure AD 로그인을 수행 하 여 단일 세션에서 비정상적인 수의 관리 활동을 수행 했음을 나타냅니다. 이는 Fusion 인시던트 설명에 표시 된 계정이 손상 되었을 수 있으며 악의적인 의도를 사용 하 여 무단으로 무단 관리 작업을 수행 하는 데 사용 되었음을 나타냅니다. 또한 관리자 권한이 있는 계정이 손상 되었을 수 있음을 나타냅니다. 의심 스러운 클라우드 앱 관리 활동 경고에 대 한 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **의심 스러운 클라우드 앱 관리 활동으로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **의심 스러운 클라우드 앱 관리 활동으로 이어지는 익숙하지 않은 위치의 로그인 이벤트**

- **의심 스러운 클라우드 앱 관리 활동으로 이어지는 감염 된 장치에서 로그인 이벤트**

- **의심 스러운 클라우드 앱 관리 활동으로 이어지는 익명 IP 주소의 로그인 이벤트**

- **의심 스러운 자격 증명이 있는 사용자의 로그인 이벤트로 의심 되는 클라우드 앱 관리 활동**

## <a name="malicious-execution-with-legitimate-process"></a>합법적인 프로세스로 악성 실행

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell에서 의심 스러운 네트워크 연결을 수행한 후 Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상적인 트래픽이 발생 했습니다.
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 문제점

**MITRE at&t&헤드 기술:** 명령 및 스크립팅 인터프리터 (T1059)

**데이터 커넥터 원본:** Microsoft Defender for Endpoint (이전의 Microsoft Defender Advanced Threat Protection 또는 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 PowerShell 명령을 통해 아웃 바운드 연결 요청이 발생 했음을 나타내며, Palo Alto Networks 방화벽에서 비정상적인 인바운드 활동을 감지 했습니다. 공격자가 네트워크에 대 한 액세스 권한을 획득 하 여 악의적인 작업을 수행 하 려 할 가능성이 있음을 나타냅니다. PowerShell에서이 패턴을 따르는 연결 시도는 맬웨어 명령 및 제어 작업, 추가 맬웨어 다운로드 요청 또는 원격 대화형 액세스를 설정 하는 공격자에 게 표시 될 수 있습니다. 모든 "육지를 벗어난" 공격과 마찬가지로이 활동은 PowerShell을 합법적인 방법으로 사용할 수 있습니다. 그러나 PowerShell 명령 실행 후 의심 스러운 인바운드 방화벽 작업을 수행 하면 PowerShell이 악성 방식으로 사용 되 고 있으며 추가로 조사 되어야 한다는 확신도 높아집니다. Palo Alto 로그에서 Azure 센티널은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용 되는 경우 (의심 스러운 데이터, 파일, 홍수, 패킷, 검색, 스파이웨어, url, 바이러스, 취약점, 산, wildfires) 트래픽이 의심 스러운 것으로 간주 됩니다. 또한 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열 된 [위협/내용 유형에](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 해당 하는 Palo Alto 위협 로그를 참조 하세요.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>의심 스러운 원격 WMI 실행 후 Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상적인 트래픽
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 실행, 검색

**MITRE at&t&헤드 기술:** WMI(Windows Management Instrumentation) (T1047)

**데이터 커넥터 원본:** Microsoft Defender for Endpoint (이전의 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 WMI (Windows Management Interface) 명령이 시스템에서 원격으로 실행 되었으며이에 따라 의심 스러운 인바운드 활동이 Palo Alto Networks 방화벽에 의해 검색 되었음을 나타냅니다. 공격자가 네트워크에 대 한 액세스 권한을 획득 하 고 수평 이동, 권한 상승 및/또는 악의적인 페이로드 실행을 시도할 수 있음을 나타냅니다. 모든 "육지를 벗어난" 공격과 마찬가지로이 활동은 WMI를 합법적으로 사용할 수 있습니다. 그러나 원격 WMI 명령 실행 후 의심 스러운 인바운드 방화벽 작업을 수행 하면 WMI가 악성 방식으로 사용 되 고 있으며 추가로 조사 해야 한다는 확신도 높아집니다. Palo Alto 로그에서 Azure 센티널은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용 되는 경우 (의심 스러운 데이터, 파일, 홍수, 패킷, 검색, 스파이웨어, url, 바이러스, 취약점, 산, wildfires) 트래픽이 의심 스러운 것으로 간주 됩니다. 또한 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열 된 [위협/내용 유형에](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 해당 하는 Palo Alto 위협 로그를 참조 하세요.

## <a name="malware-c2-or-download"></a>맬웨어 C2 또는 다운로드

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상 트래픽 뒤에 익명화 서비스에 대 한 네트워크 요청입니다.
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 명령 및 제어

**MITRE at&t&헤드 기술:** 암호화 된 채널 (T1573), 프록시 (T1090)

**데이터 커넥터 원본:** Microsoft Defender for Endpoint (이전의 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 익명화 서비스에 대 한 아웃 바운드 연결 요청이 발생 했음을 나타내며, 그 다음에는 Palo Alto Networks 방화벽에서 비정상적인 인바운드 활동을 감지 했습니다. 공격자가 네트워크에 대 한 액세스 권한을 획득 하 고 해당 작업 및 의도를 숨기 려 함을 나타냅니다. 이 패턴을 따라 하는 연결 네트워크에 대 한 연결은 맬웨어 명령 및 제어 작업, 추가 맬웨어 다운로드 요청 또는 원격 대화형 액세스를 설정 하는 공격자를 나타냅니다. Palo Alto 로그에서 Azure 센티널은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용 되는 경우 (의심 스러운 데이터, 파일, 홍수, 패킷, 검색, 스파이웨어, url, 바이러스, 취약점, 산, wildfires) 트래픽이 의심 스러운 것으로 간주 됩니다. 또한 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열 된 [위협/내용 유형에](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 해당 하는 Palo Alto 위협 로그를 참조 하세요.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Palo Alto Networks 방화벽으로 플래그가 지정 된 비정상 트래픽 다음에 무단 액세스 시도 기록을 사용 하 여 IP에 대 한 아웃 바운드 연결
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 명령 및 제어

**MITRE at&t&헤드 기술:** 해당 없음

**데이터 커넥터 원본:** Microsoft Defender for Endpoint (이전의 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 무단 액세스 시도 기록이 설정 된 IP 주소에 대 한 아웃 바운드 연결을 설정 하 고 Palo Alto Networks 방화벽에서 비정상적인 활동을 검색 했음을 나타냅니다. 공격자가 네트워크에 대 한 액세스 권한을 얻을 가능성이 있음을 나타내는 표시를 제공 합니다. 이 패턴을 수행 하는 연결 시도는 맬웨어 명령 및 제어 작업, 추가 맬웨어 다운로드 요청 또는 원격 대화형 액세스를 설정 하는 공격자에 게 표시 될 수 있습니다. Palo Alto 로그에서 Azure 센티널은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용 되는 경우 (의심 스러운 데이터, 파일, 홍수, 패킷, 검색, 스파이웨어, url, 바이러스, 취약점, 산, wildfires) 트래픽이 의심 스러운 것으로 간주 됩니다. 또한 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열 된 [위협/내용 유형에](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 해당 하는 Palo Alto 위협 로그를 참조 하세요.

## <a name="ransomware"></a>랜섬웨어

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>의심 스러운 Azure AD 로그인 후 랜 섬 웨어 실행

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 영향

**MITRE at&t&헤드 기술:** 유효한 계정 (T1078), 영향을 위해 암호화 된 데이터 (T1486)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 Azure AD 계정에 대 한 의심 스러운 로그인 후에 랜 섬 웨어 공격을 나타내는 비정상 사용자 동작이 검색 되었음을 나타냅니다. 이 표시는 Fusion 인시던트 설명에 표시 된 계정이 손상 되었으며 데이터 소유자를 extorting 하거나 데이터에 대 한 데이터 소유자의 액세스를 거부 하기 위해 데이터를 암호화 하는 데 사용 되었음을 의미 합니다. 랜 섬 웨어 실행 경고에 대 한 의심 스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **클라우드 앱에서 랜 섬 웨어로 이어지는 불규칙 한 위치로 이동할 수 없습니다.**

- **클라우드 앱에서 랜 섬 웨어로 이어지는 익숙하지 않은 위치의 로그인 이벤트**

- **클라우드 앱에서 랜 섬 웨어를 주도하는 감염 된 장치에서의 로그인 이벤트**

- **클라우드 앱에서 랜 섬 웨어로 향하는 익명 IP 주소의 로그인 이벤트**

- **클라우드 앱에서 랜 섬 웨어를 초래 하는 누출 자격 증명이 있는 사용자의 로그인 이벤트**

## <a name="remote-exploitation"></a>원격 악용

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>공격 프레임 워크의 의심 스러운 사용, Palo Alto Networks 방화벽에 의해 플래그가 지정 된 비정상 트래픽
이 시나리오는 현재 **공개 미리 보기로**제공 됩니다.

**MITRE at&t&접시 헤드 전술:** 초기 액세스, 실행, 측면 이동, 권한 상승

**MITRE at&t&헤드 기술:** T1190 (공용 응용 프로그램) 악용, 클라이언트 실행을 위한 악용 (T1203), 원격 서비스의 악용 (T1210), 권한 상승에 대 한 악용 (T1068)

**데이터 커넥터 원본:** Microsoft Defender for Endpoint (이전의 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 Metasploit와 같은 공격 프레임 워크를 사용 하는 것과 유사한 프로토콜의 비표준 사용을 감지 하 여 Palo Alto Networks 방화벽에서 의심 스러운 인바운드 활동을 검색 했음을 나타냅니다. 이는 공격자가 네트워크 리소스에 대 한 액세스 권한을 얻기 위해 서비스를 악용 했거나 공격자가 이미 액세스 권한을 획득 했 고 사용 가능한 시스템/서비스를 활용 하 여 수평을 이동 하거나 권한을 에스컬레이션 하는 초기 표시 일 수 있습니다. Palo Alto 로그에서 Azure 센티널은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용 되는 경우 (의심 스러운 데이터, 파일, 홍수, 패킷, 검색, 스파이웨어, url, 바이러스, 취약점, 산, wildfires) 트래픽이 의심 스러운 것으로 간주 됩니다. 또한 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열 된 [위협/내용 유형에](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 해당 하는 Palo Alto 위협 로그를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 고급 다단계 공격 검색에 대해 자세히 알아보았습니다. 다음 빠른 시작을 사용 하 여 데이터 및 잠재적 위협에 대 한 가시성을 얻는 방법에 대해 알아볼 수 있습니다. [Azure 센티널 시작](quickstart-get-visibility.md).

사용자를 위해 생성 된 인시던트를 조사할 준비가 된 경우 다음 자습서: [Azure 센티널로 인시던트 조사](tutorial-investigate-cases.md)를 참조 하세요.

