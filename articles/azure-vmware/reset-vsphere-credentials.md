---
title: Azure VMware Solution에 대한 vSphere 자격 증명 다시 설정
description: Azure VMware Solution 프라이빗 클라우드에 대한 vSphere 자격 증명을 다시 설정하고 HCX 커넥터에 최신 vSphere 자격 증명이 있는지 확인하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 03/31/2021
ms.openlocfilehash: 793b79e42a0adbca54804d1b66102736aff22d7a
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109104"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 vSphere 자격 증명 다시 설정

이 문서에서는 Azure VMware Solution 프라이빗 클라우드에 대한 vCenter Server 및 NSX-T Manager 자격 증명을 다시 설정하는 단계를 안내합니다. 이렇게 하면 HCX 커넥터에 최신 vCenter Server 자격 증명이 있는지 확인할 수 있습니다.

이 방법 외에도 [VCenter CloudAdmin 및 NSX-T 관리자 암호를 다시 설정](https://youtu.be/cK1qY3knj88)하기 위한 비디오를 볼 수 있습니다.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Azure VMware Solution 자격 증명 다시 설정

 먼저 Azure VMare Solution 구성 요소 자격 증명을 다시 설정하겠습니다. vCenter Server CloudAdmin 및 NSX-T 관리자 자격 증명은 만료되지 않습니다. 그러나 다음 단계에 따라 이러한 계정에 대한 새 암호를 생성할 수 있습니다.

> [!NOTE]
> HCX, vRealize Orchestrator, vRealizae Operations Manager 또는 VMware Horizon과 같은 연결된 서비스에 CloudAdmin 자격 증명을 사용하는 경우 암호를 업데이트하면 연결 작동이 중지됩니다.  이러한 서비스는 암호 순환을 시작하기 전에 중지해야 합니다.  그러지 않으면 vCenter CloudAdmin 및 NSX-T 관리자 계정이 일시적으로 잠길 수 있습니다. 이러한 서비스는 이전 자격 증명을 사용하여 계속 호출하기 때문입니다.  연결된 서비스에 대해 별도의 계정을 설정하는 방법에 대한 자세한 내용은 [액세스 및 ID 개념](https://docs.microsoft.com/azure/azure-vmware/concepts-identity)을 참조하세요.

1. Azure Portal에서 Azure Cloud Shell 세션을 엽니다.

2. 다음 명령을 실행하여 vCenter CloudAdmin 암호를 업데이트합니다.  {SubscriptionID}, {ResourceGroup} 및 {PrivateCloudName}을 CloudAdmin 계정이 속한 프라이빗 클라우드의 실제 값으로 바꾸어야 합니다.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. 다음 명령을 실행하여 NSX-T 관리자 암호를 업데이트합니다. {SubscriptionID}, {ResourceGroup} 및 {PrivateCloudName}을 NSX-T 관리자 계정이 속한 프라이빗 클라우드의 실제 값으로 바꾸어야 합니다.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>HCX 커넥터에 최신 vCenter Server 자격 증명이 있는지 확인합니다.

자격 증명을 다시 설정했으므로 다음 단계에 따라 HCX 커넥터에 업데이트된 자격 증명이 있는지 확인합니다.

1. 암호를 변경한 후에는 온-프레미스 HCX 커넥터 웹 인터페이스로 이동합니다. 여기서는 https://{ip of the HCX connector appliance}:443을 사용합니다. 포트 443을 사용해야 합니다. 새 자격 증명을 사용하여 로그인합니다.

2. VMware HCX 대시보드에서 **사이트 페어링** 을 선택합니다.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="사이트 페어링이 강조 표시된 VMware HCX 대시보드의 스크린샷":::입니다.
 
3. Azure VMware Solution에 대한 올바른 연결을 선택하고(둘 이상인 경우) **연결 편집** 을 선택합니다.
 
4. 새 vCenter Server CloudAdmin 사용자 자격 증명을 제공하고 **편집** 을 선택하여 자격 증명을 저장합니다. 저장하면 성공이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution에 대한 vCenter Server 및 NSX-T Manager 자격 증명 다시 설정을 다루었으므로 다음 사항을 알아볼 수 있습니다.

- [Azure VMware Solution에서 NSX 네트워크 구성 요소 구성](configure-nsx-network-components-azure-portal.md)
- [Azure VMware Solution VM의 수명 주기 관리](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Azure VMware Solution을 사용하여 가상 머신의 재해 복구 배포](disaster-recovery-for-virtual-machines.md)
