---
title: Azure용 해석 가능한 모듈 및 버전 매트릭스 | 마이크로 소프트 문서
description: Azure용 Ansible 모듈 및 버전 매트릭스
keywords: Ansible, 역할, 매트릭스, 버전, Azure, DevOps
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74155978"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 모듈 및 버전 매트릭스

Ansible에는 Azure 리소스 프로비저닝 및 구성에 사용할 모듈 모음이 포함되어 있습니다. 이러한 리소스에는 가상 머신포함, 확장 집합, 네트워킹 서비스 및 컨테이너 서비스가 포함됩니다. 이 문서에서는 Azure에 대한 다양한 Ansible 모듈과 해당 모듈이 제공하는 Ansible 버전을 나열합니다.

## <a name="ansible-modules-for-azure"></a>Azure용 Ansible 모듈

다음 모듈은 원격 호스트 또는 플레이북을 통해 직접 실행할 수 있습니다.  

이 모듈은 Ansible 공식 릴리스 및 다음 Microsoft 플레이북 역할에서 사용할 수 있습니다.

> [!NOTE]
> Ansible 2.9 이후부터 모든 *_facts 모듈의 이름을 Ansible 명명 규칙을 준수하기 위해 *_info 변경했습니다. 이전 모듈과 이름이 변경된 모듈은 사용 중단 경고를 보는 것 외에도 모든 모듈이 이전과 같이 작동하므로 연결됩니다.

| Azure용 Ansible 모듈                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | 안성 2.8 | 안수 2.9 | Ansible 역할 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **계산**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_availabilityset_info              | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_deployment                         | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_functionapp                        | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_functionapp_info                  | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_image                              | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_image_info                        | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_resource                           | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_resource_info                     | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_resourcegroup                      | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_resourcegroup_info                | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_virtualmachine                     | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachineextension            | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachineimage_info          | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachinescaleset             | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachinescaleset_info       | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | yes          | yes          | yes          |
| **네트워킹**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_dnsrecordset                       | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_dnsrecordset_info                 | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_dnszone                            | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_dnszone_info                      | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_loadbalancer                       | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_loadbalancer_info                 | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_networkinterface                   | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_networkinterface_info             | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_publicipaddress                    | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_publicipaddress_info              | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_route                              | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_routetable                         | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_routetable_info                   | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_securitygroup                      | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_subnet                             | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_virtualnetwork                     | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualnetwork_info               | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | yes          | yes          |
| **스토리지**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_manageddisk_info                  | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_storageaccount                     | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_storageaccount_info               | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_storageblob                        | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| **웹**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_webapp                             | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_webapp_info                       | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | yes          | yes          | yes          |
| **컨테이너**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_aks                                | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_aks_info                          | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_containerinstance                  | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_containerregistry                  | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_containerregistry_info            | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | yes          |
| **데이터베이스**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqldatabase                      | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlserver                        | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqldatabase                 | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqlserver                   | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_sqldatabase                        | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_sqlserver                          | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_sqlserver_info                    | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| **분석**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | yes          | yes          |
| **통합**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | yes          | yes          | yes          |
| **보안**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | yes          | yes          | yes          |
| azure_rm_keyvaultkey                        | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_keyvaultsecret                     | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | yes          | yes          | yes          |
| **데브옵스 (주)데브옵스**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | yes          | yes          | yes          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_autoscale_info                    | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | yes          | yes          |
| **관리 및 거버넌스**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | yes        | yes          |
| **사물 인터넷**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | yes        | yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure에 대한 플레이북 역할 소개

[azure_preview_module 재생 책 역할에는](https://galaxy.ansible.com/Azure/azure_preview_modules/) 모든 최신 Azure 모듈이 포함됩니다. 공식 Ansible 릴리스보다 업데이트 및 버그 수정이 더 적절하게 수행됩니다. Azure 리소스 프로비저닝 목적으로 Ansible을 사용하는 경우 `azure_preview_module` 플레이북 역할을 설치하는 것이 좋습니다.

`azure_preview_module` 플레이북 역할은 3주에 한 번씩 발매됩니다.

## <a name="next-steps"></a>다음 단계

재생책 역할에 대한 자세한 내용은 [재사용 가능한 재생책 만들기를](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)참조하십시오. 