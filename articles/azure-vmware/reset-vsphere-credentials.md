---
title: Azure VMware 솔루션에 대 한 vSphere 자격 증명 다시 설정
description: Azure VMware 솔루션 사설 클라우드의 vSphere 자격 증명을 다시 설정 하 고 HCX 커넥터에 최신 vSphere 자격 증명이 있는지 확인 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603197"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 vSphere 자격 증명 다시 설정

이 문서에서는 Azure VMware 솔루션 사설 클라우드의 vSphere 자격 증명을 다시 설정 하는 단계를 안내 합니다. 그러면 HCX 커넥터에 최신 vSphere 자격 증명이 있는지 확인할 수 있습니다.

## <a name="reset-your-vsphere-credentials"></a>VSphere 자격 증명 다시 설정

 먼저 vSphere 자격 증명을 다시 설정 하겠습니다. VCenter CloudAdmin 및 NSX 관리자 자격 증명이 만료 되지 않습니다. 그러나 다음 단계를 수행 하 여 이러한 계정에 대 한 새 암호를 생성할 수 있습니다.

> [!NOTE]
> HCX, vCenter Orchestrator, vCloud Director 또는 Vcloud와 같은 연결 된 서비스에 CloudAdmin 자격 증명을 사용 하는 경우 암호를 업데이트 하면 연결이 작동을 중지 합니다.  이러한 서비스는 암호 순환을 시작 하기 전에 중지 해야 합니다.  이렇게 하지 않으면 해당 서비스가 이전 자격 증명을 사용 하 여 지속적으로 호출 하므로 vCenter CloudAdmin 및 NSX admin 계정에 일시적 잠금이 발생할 수 있습니다.  연결 된 서비스에 대 한 별도의 계정을 설정 하는 방법에 대 한 자세한 내용은 [액세스 및 Id 개념](https://docs.microsoft.com/azure/azure-vmware/concepts-identity)을 참조 하세요.

1. Azure VMware 솔루션 포털에서 명령줄을 엽니다.

2. 다음 명령을 실행 하 여 vCenter CloudAdmin 암호를 업데이트 합니다.  {SubscriptionID}, {ResourceGroup} 및 {PrivateCloudName}을 CloudAdmin 계정이 속한 사설 클라우드의 실제 값으로 바꾸어야 합니다.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. 다음 명령을 실행 하 여 NSX-T 관리자 암호를 업데이트 합니다. {SubscriptionID}, {ResourceGroup} 및 {PrivateCloudName}을 NSX admin 계정이 속한 사설 클라우드의 실제 값으로 바꾸어야 합니다.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>HCX 커넥터에 최신 vSphere 자격 증명이 있는지 확인 합니다.

자격 증명을 재설정 했으므로 다음 단계에 따라 HCX 커넥터에 업데이트 된 자격 증명이 있는지 확인 합니다.

1. 암호를 변경한 후에는 온-프레미스 HCX 커넥터 웹 인터페이스로 이동 합니다. 여기서는 https://{HCX connector 어플라이언스의 ip}: 443을 사용 합니다. 포트 443을 사용 해야 합니다. 새 자격 증명을 사용 하 여 로그인 합니다.

2. VMware HCX 대시보드에서 **사이트 페어링** 을 선택 합니다.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="사이트 페어링이 강조 표시 된 VMware HCX 대시보드의 스크린샷":::
 
3. AVS에 대 한 올바른 연결 (둘 이상 있는 경우)을 선택 하 고 **연결 편집** 을 선택 합니다.
 
4. 새 vSphere 자격 증명을 제공 하 고 **편집** 을 선택 하 여 자격 증명을 저장 합니다. 저장 하면 성공이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware 솔루션에 대 한 vSphere 자격 증명 재설정을 살펴보았으므로 다음에 대해 알아볼 수 있습니다.

- [Azure VMware 솔루션에서 NSX 네트워크 구성 요소를 구성](configure-nsx-network-components-azure-portal.md)하는 중입니다.
- [Azure VMware 솔루션 vm의 수명 주기 관리](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Azure VMware 솔루션을 사용 하 여 가상 머신의 재해 복구 배포](disaster-recovery-for-virtual-machines.md)
