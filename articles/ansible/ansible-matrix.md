---
title: Azure용 Ansible 모듈 및 버전 매트릭스
description: Azure용 Ansible 모듈 및 버전 매트릭스
ms.service: ansible
keywords: Ansible, 역할, 매트릭스, 버전, Azure, DevOps
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 07/02/2018
ms.topic: article
ms.openlocfilehash: 8bbb2563991b45fe7a20ce243751c1c1a1f75b99
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094910"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 모듈 및 버전 매트릭스

## <a name="ansible-modules-for-azure"></a>Azure용 Ansible 모듈
Ansible은 원격 호스트에서 직접 또는 플레이북을 통해 실행할 수 있는 여러 모듈과 함께 제공됩니다.
이 문서에서는 가상 머신, 네트워킹 및 컨테이너 서비스와 같은 Azure 클라우드 리소스를 프로비전할 수 있는 Azure용 Ansible 모듈을 나열합니다. Ansible의 공식 릴리스 또는 Microsoft에서 게시한 다음 플레이북에서 이러한 모듈을 가져올 수 있습니다.

| Azure용 Ansible 모듈                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 |  Playbook 역할 [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------| 
| **Compute**                    |           |                          |                          |                                  | 
| azure_rm_availabilityset                    | yes          | 예                         | 예          | yes                                 | 
| azure_rm_availabilityset_facts              | yes          | 예                         | 예          | yes                                 | 
| azure_rm_deployment                         | yes          | 예                         | 예          | yes                                 | 
| azure_rm_resource                           | -            | -                           | yes          | yes                                 | 
| azure_rm_resource_facts                     | -            | -                           | yes          | yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | yes          | 예                         | 예          | yes                                 | 
| azure_rm_virtualmachineimage_facts          | yes          | 예                         | 예          | yes                                 | 
| azure_rm_resourcegroup                      | yes          | 예                         | 예          | yes                                 | 
| azure_rm_resourcegroup_facts                | yes          | 예                         | 예          | yes                                 | 
| azure_rm_virtualmachine                     | yes          | 예                         | 예          | yes                                 | 
| azure_rm_virtualmachine_extension           | yes          | 예                         | 예          | yes                                 | 
| azure_rm_virtualmachine_scaleset            | yes          | 예                         | 예          | yes                                 | 
| azure_rm_image                              |              | yes                         | 예          | yes                                 | 
| **네트워킹**                    |           |                          |                          |                                  | 
| azure_rm_virtualnetwork                     | yes          | 예                         | 예          | yes                                 | 
| azure_rm_virtualnetwork_facts               | yes          | 예                         | 예          | yes                                 | 
| azure_rm_subnet                             | yes          | 예                         | 예          | yes                                 | 
| azure_rm_networkinterface                   | yes          | 예                         | 예          | yes                                 | 
| azure_rm_networkinterface_facts             | yes          | 예                         | 예          | yes                                 | 
| azure_rm_publicipaddress                    | yes          | 예                         | 예          | yes                                 | 
| azure_rm_publicipaddress_facts              | yes          | 예                         | 예          | yes                                 | 
| azure_rm_dnsrecordset                       | yes          | 예                         | 예          | yes                                 | 
| azure_rm_dnsrecordset_facts                 | yes          | 예                         | 예          | yes                                 | 
| azure_rm_dnszone                            | yes          | 예                         | 예          | yes                                 | 
| azure_rm_dnszone_facts                      | yes          | 예                         | 예          | yes                                 | 
| azure_rm_loadbalancer                       | yes          | 예                         | 예          | yes                                 | 
| azure_rm_loadbalancer_facts                 | yes          | 예                         | 예          | yes                                 | 
| azure_rm_appgw                              | -            | -                           | -            | yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | yes                                 |
| azure_rm_securitygroup                      | yes          | 예                         | 예          | yes                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | yes                                 | 
| **Storage**                    |           |                          |                          |                                  | 
| azure_rm_storageaccount                     | yes          | 예                         | 예          | yes                                 | 
| azure_rm_storageaccount_facts               | yes          | 예                         | 예          | yes                                 | 
| azure_rm_storageblob                        | yes          | 예                         | 예          | yes                                 | 
| azure_rm_managed_disk                       | yes          | 예                         | 예          | yes                                 | 
| azure_rm_managed_disk_facts                 | yes          | 예                         | 예          | yes                                 | 
| **컨테이너**                    |           |                          |                          |                                  | 
| azure_rm_aks                                | -            | -                           | yes          | yes                                 | 
| azure_rm_aks_facts                          | -            | -                           | yes          | yes                                 | 
| azure_rm_acs                                | yes          | 예                         | 예          | yes                                 | 
| azure_rm_containerinstance                  | -            | yes                         | 예          | yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -            | yes                                 | 
| azure_rm_containerregistry                  | -            | yes                         | 예          | yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | yes                                 | 
| **Azure 기능**                    |           |                          |                          |                                  | 
| azure_rm_functionapp                        | yes          | 예                         | 예          | yes                                 | 
| azure_rm_functionapp_facts                  | yes          | 예                         | 예          | yes                                 | 
| **데이터베이스**                    |           |                          |                          |                                  | 
| azure_rm_sqlserver                          | -            | yes                         | 예          | yes                                 | 
| azure_rm_sqlserver_facts                    | -            | yes                         | 예          | yes                                 | 
| azure_rm_sqldatabase                        | -            | yes                         | 예          | yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | yes                                 | 
| azure_rm_mysqlserver                        | -            | yes                         | 예          | yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | yes                                 | 
| azure_rm_mysqldatabase                      | -            | yes                         | 예          | yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | yes                                 | 
| azure_rm_postgresqlserver                   | -            | yes                         | 예          | yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | yes                                 | 
| azure_rm_postgresqldatabase                 | -            | yes                         | 예          | yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | yes                                 | 
| **Key Vault**                    |           |                          |                          |                                  | 
| azure_rm_keyvault                           | -            | yes                         | 예          | yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -            | yes                                 |
| azure_rm_keyvaultkey                        | -            | yes                         | 예          | yes                                 |
| azure_rm_keyvaultsecret                     | -            | yes                         | 예          | yes                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Azure에 대한 플레이북 역할 소개
[azure_preview_module 플레이북 역할](https://galaxy.ansible.com/Azure/azure_preview_modules/)은 가장 완벽한 역할로써 최신 Azure 모듈을 모두 포함합니다. 공식 Ansible 릴리스보다 업데이트 및 버그 수정이 더 적절하게 수행됩니다. Azure 리소스를 프로비전할 목적으로 Ansible를 사용하는 경우 azure_preview_module 역할을 설치하는 것이 좋습니다.

azure_preview_module 플레이북 역할은 3주마다 릴리스됩니다.

## <a name="next-steps"></a>다음 단계
플레이북 역할과 관련된 자세한 내용은 [다시 사용할 수 있는 플레이북 만들기](http://docs.ansible.com/ansible/latest/playbooks_reuse.html)를 참조하세요. 
