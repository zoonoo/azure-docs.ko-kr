---
title: Microsoft Azure 스택 개발 키트 릴리스 정보 | Microsoft Docs
description: 향상 된 기능, 수정 및 Azure 스택 개발 키트에 대 한 알려진된 문제입니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 185cd6f20cdb19531777ccb58e72be14ea656549
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850123"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure 스택 개발 키트 릴리스 정보  
이러한 릴리스 정보에서는 향상 된 기능, 수정 및 Azure 스택 개발 키트의 알려진된 문제에 대 한 정보를 제공 합니다. 실행 중인 버전을 잘 모르는 경우 다음을 할 수 있습니다 [포털을 사용 하 여](.\.\azure-stack-updates.md#determine-the-current-version)합니다.

> 구독 하 여 ASDK는의 새로운 기능을 통해 최신 정보는 [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [피드](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)합니다.

## <a name="build-11805147"></a>빌드 1.1805.1.47

> [!TIP]  
> Microsoft Azure 스택에 사용 중인 버전 스키마에는 업데이트는 고객의 의견에 따라. 새 스키마 1805,이 업데이트를 시작 합니다. 현재 클라우드 버전을 나타내는 더 잘 합니다.  
> 
> 버전 스키마는 이제 *Version.YearYearMonthMonth.MinorVersion.BuildNumber* 두 번째 및 세 번째 집합의 버전 및 릴리스 여기서 나타냅니다. 예를 들어 1805.1 나타냅니다는 *제조 하도록 릴리스* 1805 (RTM) 버전입니다.  


### <a name="new-features"></a>새로운 기능 
이 빌드 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack now includes a *Syslog* client** as a *preview feature*. This client allows the forwarding of audit and security logs related to the Azure Stack infrastructure to a Syslog server or security information and event management (SIEM) software that is external to Azure Stack. Currently, the Syslog client only supports unauthenticated UDP connections over default port 514. The payload of each Syslog message is formatted in Common Event Format (CEF). 

  Syslog 클라이언트를 구성 하려면 사용 하 여는 **집합 SyslogServer** cmdlet은 권한 있는 끝점에 노출 합니다. 

  이 미리 보기에서는 다음 세 가지 경고를 볼 수 있습니다. 이러한 경고를 포함 하는 Azure 스택에서 나타나면 *설명* 및 *재구성* 지침. 
  - 해제 코드 무결성 제목:  
  - 감사 모드에서 코드 무결성 제목: 
  - 제목: 사용자 계정 생성

  이 기능은 미리 보기 상태인 동안 것 신뢰 해서는 시 프로덕션 환경에서 합니다.   


### <a name="fixed-issues"></a>해결된 문제

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- <!-- 2551834 - IS, ASDK --> When you select **Overview** for a storage account in either the admin or user portals, the information from the *Essentials* pane does not display.  The Essentials pane displays information about the account like its *Resource group*, *Location*, and *Subscription ID*.  Other options for Overview  are accessible, like *Services* and *Monitoring*, as well as options to *Open in Explorer* or to *Delete storage account*.  

  사용할 수 없는 정보를 보려면 사용 하 여는 [Get azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell cmdlet. 

- <!-- TBD - IS ASDK --> Do not use the new administrative subscription types of *Metering subscription*, and *Consumption subscription*. These new subscription types were introduced with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다 하 고 시간이 지남에 따라 자동으로 닫힙니다.  

- <!-- 2392907 – ASDK -->   You might see a *critical* alert for **Low memory capacity**. This alert has the following description: *The region has consumed more than 95.00 % of available memory. Creating virtual machines with large amounts of memory may fail.*

  Azure 스택 올바르게 계정 하지 메모리 사용에 대 한 Azure 스택 개발 키트에서 하는 경우이 경고를 생성할 수 있습니다.  

  이 경고는 무시할 수 있으며 문제 가상 컴퓨터의 배치에 영향을 주지 않습니다. 

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


#### <a name="compute"></a>컴퓨팅
- <!-- TBD - IS, ASDK --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  문제를 해결 VM을 배포 하는 다음 방법 중 하나를 사용 합니다. 각 방법에 사용할 VM 크기를 지정 해야 합니다.

  - **Azure 리소스 관리자 템플릿:** 서식 파일을 사용 하면 설정는 *vmSize* 템플릿의 사용 하려는 VM 크기와 같아야 합니다. 사용 하는 VM을 배포 하려면 다음 항목이 사용 되는 예를 들어는 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수 있습니다는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 VM 크기 비슷합니다 매개 변수로 지정 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [새로 AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 v M 크기를 지정 하는 매개 변수와 함께 `-VMSize "Standard_F32s_v2"`합니다.


- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>네트워킹
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.


- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  이 문제를 해결 하려면 계획 이미 구독과 연결 된 경우 네트워크 할당량을 늘려야 하는 추가 기능 계획을 사용 합니다. 자세한 내용은 참조 하는 방법 [플랜을 통해 추가 기능을 사용할 수 있도록](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)합니다.

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.  

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.

#### <a name="usage"></a>사용 현황  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->



## <a name="build-201805131"></a>빌드 20180513.1

### <a name="new-features"></a>새로운 기능 
이 빌드 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  이러한 새 구독 유형은 볼 수 있지만 기본 공급자 구독을 보호 하 고 쉽게 SQL 호스팅 서버와 마찬가지로 공유 리소스를 배포 하는 큰 변경의 일부입니다. 

  현재 사용할 수 있는 세 개의 구독 유형은 다음과 같습니다.  
  - 기본 공급자 구독:이 구독 유형을 사용 하 여 계속 합니다. 
  - 구독을 계량: *이 구독의 유형은 사용 하지 마십시오.*
  - 소비 구독: *이 구독의 유형은 사용 하지 마십시오*

### <a name="fixed-issues"></a>해결된 문제
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>추가 릴리스가이 업데이트 시간이 초과 되었습니다.  
다음 사용할 수 있지만 Azure 스택 업데이트 1804 필요 하지 않습니다.
- **Microsoft Azure 스택 System Center Operations Manager 모니터링 팩으로 업데이트**합니다. 새 버전 (1.0.3.0)는 Microsoft System Center Operations Manager 모니터링 팩에 대 한 Azure 스택의를 사용할 수 있는 [다운로드](https://www.microsoft.com/download/details.aspx?id=55184)합니다. 이 버전에서는 연결 된 Azure 스택 배포를 추가 하면 서비스 사용자를 사용할 수 있습니다. 또한이 버전의 Operations Manager 내에서 직접 수정 조치를 취할 수 있는 업데이트 관리 경험을 기능 합니다. 리소스 공급자를 표시, 단위, 확장 및 단위 노드를 확장 하는 새 대시보드도 있습니다.

- **새로운 Azure 스택 관리자 PowerShell 버전 1.3.0**합니다.  Azure 스택 PowerShell 1.3.0 설치용으로 제공 되었습니다. 이 버전에는 Azure 스택을 관리 하는 모든 관리자 리소스 공급자에 대 한 명령을 제공 합니다.  이 릴리스에서 일부 콘텐츠를 Azure 스택 도구 GitHub에서 중단 예정 [리포지토리](https://github.com/Azure/AzureStack-Tools)합니다. 

   설치 세부 정보에 따라는 [지침](.\.\azure-stack-powershell-install.md) 또는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure 스택 모듈에 대 한 콘텐츠입니다. 

- **초기 버전의 Azure 스택 API Rest 참조**합니다. [모든 Azure 스택 관리자 리소스 공급자에 대 한 API 참조](https://docs.microsoft.com/rest/api/azure-stack/) 이제 게시 되었습니다. 

### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Azure 스택 개발 키트를 사용 하 여 https://aka.ms/azurestackforum합니다.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  

#### <a name="marketplace"></a>Marketplace
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>네트워킹
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  이 문제를 해결 하려면 계획 이미 구독과 연결 된 경우 네트워크 할당량을 늘려야 하는 추가 기능 계획을 사용 합니다. 자세한 내용은 참조 하는 방법 [플랜을 통해 추가 기능을 사용할 수 있도록](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)합니다.

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>사용 현황  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

<!-- #### Identity -->



## <a name="build-201803021"></a>빌드 20180302.1

### <a name="new-features-and-fixes"></a>새 기능 및 수정
새로운 기능 및 수정 사항에 대 한 통합 된 시스템 버전 1803 Azure 스택 개발 키트에 적용 하는 Azure 스택 해제 합니다. 참조는 [새로운 기능](.\.\azure-stack-update-1803.md#new-features) 및 [해결 된 문제](.\.\azure-stack-update-1803.md#fixed-issues) Azure 스택 1803의 섹션 자세한 내용은 릴리스 정보를 업데이트 합니다.  
> [!IMPORTANT]    
> 에 나열 된 항목 중 일부는 **새로운 기능** 및 **해결 된 문제** 섹션에서는 Azure 스택 통합 시스템에만 적용 됩니다.

### <a name="changes"></a>변경 내용
- 새로 만든된 제품의 상태를 변경 하는 방법은 *개인* 를 *공용* 또는 *폐기 된* 변경 되었습니다. 자세한 내용은 참조 [제안을 만들](.\.\azure-stack-create-offer.md)합니다. 


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- 기능 [새 지원 요청을 드롭다운 목록에서 열려는](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 개발 키트를 사용 하 여 https://aka.ms/azurestackforum합니다.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- 표시는 **활성화 필요** Azure 스택 개발 키트를 등록 해야 한다는 경고 성 알림입니다. 이 동작은 사용할 수 있습니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 업데이트 타일 업데이트에 대 한 정보를 표시 하는 실패 합니다. 이 문제를 해결 하려면 타일 새로 고침을 클릭 합니다.

-   관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 알림이 표시 될 수 있습니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

- 관리 포털 및 사용자 포털 모두에서 개요 블레이드에 이전 API 버전을 사용 하 여 만든 저장소 계정에 대 한 개요 블레이드를 선택 하면 로드에 실패 (예: 2015-06-15). 

  이 문제를 해결 PowerShell 실행을 사용 하는 **시작 ResourceSynchronization.ps1** 저장소 계정 세부 정보에 대 한 액세스를 복원 하는 스크립트입니다. [이 스크립트는 GitHub에서 사용할 수 있는]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)는 ASDK를 사용 하는 경우 개발 키트 호스트에서 서비스 관리자 자격 증명으로 실행 해야 합니다.  

- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  

- Azure 스택 관리자 포털에서 이름으로 중요 한 알림이 표시 될 수 있습니다 **외부 인증서 만료 보류 중인**합니다.  이 경고는 무시 해도 됩니다 및 Azure 스택 개발 키트의 작업에 영향을 줍니다. 


#### <a name="marketplace"></a>Marketplace
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

-  VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>네트워킹
- 아래 **네트워킹**누르면, **연결** VPN 연결을 설정 하려면 **VNet 대 VNet** 가능한 연결 유형으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 현재만 **사이트 (IPsec)** 옵션은 지원 됩니다.

- VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.



- 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.



#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- 사용자가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수 최대 1 시간이 걸릴 수 있습니다.

- 리소스 공급자 및 사용자 작업에 사용 하기 위해 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>App Service
- 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` TLSv1.2 GitHub 리포지토리에에서 다운로드할 때 사용할 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.





