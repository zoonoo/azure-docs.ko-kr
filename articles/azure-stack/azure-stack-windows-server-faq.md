---
title: Azure Stack Windows Server 관련 Faq | Microsoft Docs
description: Windows Server 용 Azure Stack Marketplace Faq의 목록
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: b498283ee117935438c55e5162e92acdb0f5ebfe
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649903"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Azure Stack Marketplace FAQ의에서 Windows Server

이 문서에서 Windows Server 이미지에 대 한 일부 자주 묻는 질문을 답변 합니다 [Azure Stack Marketplace](azure-stack-marketplace.md)합니다.

## <a name="marketplace-items"></a>Marketplace 항목

### <a name="how-do-i-update-to-a-newer-windows-image"></a>최신 Windows 이미지에 업데이트 하는 방법

먼저, 특정 버전에 Azure Resource Manager 템플릿을 참조 하는 경우를 결정 합니다. 그렇다면 해당 템플릿 업데이트 또는 오래 된 이미지 버전을 유지 합니다. 사용 하는 것이 좋습니다 **버전: 최신**합니다.

다음으로, 모든 Virtual Machine Scale Sets는 특정 버전을 참조 나중에 확장할 수 이러한 및 이전 버전을 유지할지 여부를 결정 하는 여부 사항을 고려해 야 합니다. 이러한 조건 중 둘 다에 적용 하는 경우 새 버전을 다운로드 하기 전에 Marketplace의 이전 이미지를 삭제 합니다. Marketplace 관리를 사용 하 여 원래 다운로드 하는 방법을 하는 경우이 작업을 수행 하 합니다. 그런 다음 최신 버전을 다운로드 합니다.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Azure Stack에 Windows Server Marketplace 이미지에 대 한 라이선스 옵션은 무엇입니까?

Microsoft는 두 가지 버전의 Azure Stack Marketplace를 통해 Windows Server 이미지를 제공합니다.

- **사용 하 여 비용을 지불**: 이러한 이미지 정 Windows 미터를 실행합니다.
   사용 해야 합니다. 사용 하는 EA (기업 계약) 고객은 *소비 청구 모델*; Csp에 게 SPLA 라이선스를 사용 하지 않으려는 합니다.
- **Bring Your Own License (BYOL)**: 이러한 이미지는 기본 미터를 실행합니다.
   사용 해야 합니다. EA 고객은 Windows Server 라이선스로; SPLA 라이선스를 사용 하는 Csp 합니다.

Azure Stack에서 azure 하이브리드 사용 혜택 (AHUB) 지원 되지 않습니다. "용량" 모델을 통해 사용 허가 받은 고객은 BYOL 이미지를 사용 해야 합니다. Azure Stack 개발 키트 (ASDK) 사용 하 여 테스트 하는 경우 이러한 옵션 중 하나를 사용할 수 있습니다.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>내 테 넌 트/사용자에 게 잘못 된 버전을 다운로드 한 경우에 어떨까요?

Marketplace 관리를 통해 잘못 된 버전을 먼저 삭제 합니다. 완전히 완료 될 때까지 기다립니다 (완료 되 면 Marketplace 관리 블레이드가 아님에 대 한 알림을 확인). 올바른 버전을 다운로드 합니다.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>경우에 어떻게 사용자 올바르게 확인란 하지 "라이선스가 있음을" 이전 Windows에서 빌드되고 라이선스가 없는?

참조 [종 량 제도 혜택을 사용 하 여 Windows Server Vm으로 변환](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1)합니다.

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>그렇다면 이전 이미지 있고 시키거나 "있는데 라이선스" 상자에서 고유한 이미지를 사용 하 고 기업 계약 자격 것을 확인 하려면 내 사용자를 잊으셨나요?

참조 [Windows Server 용 Azure Hybrid Benefit을 사용 하 여 기존 VM 변환](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server)합니다. Azure 하이브리드 혜택은 Azure Stack이이 설정의 효과를 적용 되지 않습니다 적용 됩니다.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>SQL 또는 Machine Learning Server와 같은 Windows Server를 사용 하는 다른 Vm의 경우는 어떨까요?

이러한 이미지 적용을 **licenseType** 매개 변수를 사용 하 여 요금이 부과 되기 때문입니다. 이 매개 변수를 설정할 수 있습니다 (이전 FAQ 참조 응답). 이 사용자 고유의 라이선스를 가져오도록 요구 하는 SQL, 등의 계층화 된 제품 필요가 Windows Server 소프트웨어에 적용 됩니다. 지불 라이선스 사용에 적용 되지 않습니다 계층화 된 소프트웨어 제품.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>엔터프라이즈 규약 (EA) 했 고 내 EA Windows Server 라이선스 사용 이미지 올바르게 요금이 청구 됩니다 되었는지 확인 하려면 어떻게 해야 합니까?

추가할 수 있습니다 **licenseType: Windows_Server** Azure Resource Manager 템플릿에서 합니다. 이 설정은 각 가상 머신 리소스 블록에 추가 되어야 합니다.

## <a name="activation"></a>활성화

Azure Stack에 Windows Server 가상 컴퓨터를 활성화 하려면 다음 조건이 true 여야 합니다.

- OEM에 Azure Stack에서 모든 호스트 시스템에 적절 한 BIOS 마커를 설정 합니다.
- Windows Server 2012 R2 및 Windows Server 2016을 사용 해야 [가상 컴퓨터 자동 정품 인증](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))합니다. Azure Stack에서 서비스 KMS (키 관리) 및 기타 정품 인증 서비스 지원 되지 않습니다.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>내 가상 머신이 활성화 되는 어떻게 확인할 수 있습니까?

관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다.

```shell
slmgr /dlv
```

제대로 활성화, 명확 하 게 표시 된이 표시 되 고 호스트 이름을 표시 하는 경우는 `slmgr` 출력 합니다. 회원님의 뒤에 다른 가상 컴퓨터를에서 표시 하는 또는 최신이 아닐 때 화면의 워터 마크에 종속 되지 않습니다.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>내 VM이 AVMA를 어떻게 수정 하는 데 설정 되지?

관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다.

```shell
slmgr /ipk <AVMA key>
```

참조 된 [가상 컴퓨터 자동 정품 인증](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) 이미지에 대해 사용할 키에 대 한 문서.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>내 Windows Server 이미지를 만들려면 하나요, 하려면 어떻게 해야 합니까 AVMA를 사용 하는 게?

실행 하는 것이 좋습니다.는 `slmgr /ipk` 실행 하기 전에 적절 한 키를 사용 하 여 명령줄을 `sysprep` 명령입니다. 또는 모든 Unattend.exe 설정 파일에 AVMA 키를 포함 합니다.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Azure에서 만든 Windows Server 2016 이미지를 사용 하려고 하 고이 정품 인증 또는 KMS 정품 인증을 사용 하 여 되지 않습니다.

`slmgr /ipk` 명령을 실행합니다. Azure 이미지 수 올바르게로 대체 되지 AVMA를 하지만 활성화가 Azure KMS 시스템에 도달 하는 경우. 이러한 Vm AVMA를 사용 하도록 설정 되어 있는지 확인 하는 것이 좋습니다.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>이러한 단계를 모두 수행한 하지만 내 가상 머신 아직 활성화 되지 않습니다.

올바른 BIOS 표식 설치 되었는지 확인 하려면 하드웨어 공급 업체를 문의 하십시오.

### <a name="what-about-earlier-versions-of-windows-server"></a>이전 버전의 Windows Server의 경우는 어떨까요?

[가상 컴퓨터 자동 정품 인증](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) 이전 버전의 Windows Server에서 지원 되지 않습니다. Vm을 수동으로 활성화 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Stack Marketplace 개요](azure-stack-marketplace.md)
- [Azure에서 Azure Stack marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)
