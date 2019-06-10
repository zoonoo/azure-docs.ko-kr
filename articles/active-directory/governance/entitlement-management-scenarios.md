---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 일반적인 시나리오
description: Azure Active Directory 권한 관리 (미리 보기)의 일반적인 시나리오에 대해 수행 해야 하는 고급 단계에 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a50f4a8a63022668dac68c974f8c828c72777c9
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473179"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리 (미리 보기)의 일반적인 시나리오

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

여러 가지 방법으로 조직에 대 한 권한 관리를 구성할 수 있습니다. 그러나 하면 이제 막 시작, 경우 관리자, 승인자 및 요청자에 대 한 일반적인 시나리오를 이해 해야 합니다.

## <a name="administrators"></a>관리자

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>권한 관리에 그리고 시작 도움

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | [첫 번째 액세스 패키지를 만드는 자습서를 수행 합니다.](entitlement-management-access-package-first.md) | [![Azure portal 아이콘](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>그룹, 응용 프로그램 또는 SharePoint 사이트에 대 한 액세스를 요청 하려면 내 디렉터리에서 사용자를 허용 하려고 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** [카탈로그에서 새 액세스 패키지 만들기](entitlement-management-access-package-create.md#start-new-access-package) | ![액세스 패키지 만들기](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [패키지 액세스 리소스 역할 추가](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>그룹</li><li>애플리케이션</li><li>SharePoint 사이트</li></ul> | ![리소스 역할 추가](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [정책 추가](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>디렉터리의 사용자에 대 한</li><li>승인 필요</li><li>만료 설정</li></ul> | ![정책 추가](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>그룹, 응용 프로그램 또는 SharePoint 사이트에 액세스를 요청 (사용자를 디렉터리에 아직 포함) 내 비즈니스 파트너 디렉터리에서 사용자를 허용 하려고 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** [카탈로그에서 새 액세스 패키지 만들기](entitlement-management-access-package-create.md#start-new-access-package) | ![액세스 패키지 만들기](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [패키지 액세스 리소스 역할 추가](entitlement-management-access-package-edit.md#add-resource-roles) | ![리소스 역할 추가](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [외부 사용자에 대 한 정책 추가](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>디렉터리에 없는 사용자에 대 한</li><li>승인 필요</li><li>만료 설정</li></ul> | ![외부 사용자에 대 한 정책 추가](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [비즈니스 파트너 액세스 패키지를 요청 하려면 내 액세스 포털 링크 보내기](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>비즈니스 파트너 사용자를 사용 하 여 링크를 공유할 수 있습니다.</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>그룹, 응용 프로그램 또는 SharePoint 사이트 액세스 패키지에서 변경 하려는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** 액세스 패키지를 열으십시오 | ![리소스 역할 추가](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [리소스 역할 추가 또는 제거](entitlement-management-access-package-edit.md#add-resource-roles) | ![리소스 역할 추가](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>그룹, 응용 프로그램 또는 SharePoint 사이트에 대 한 할당 권한이 있는 사용자를 확인 하려는 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** 액세스 패키지를 엽니다. | ![리소스 역할 추가](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [할당 보기](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>액세스 패키지에 액세스할 수 있는 사용자 보기</li><li>뷰는 사용자의 액세스가 만료</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>그룹, 응용 프로그램 또는 SharePoint 사이트 사용자가에 대 한 액세스를 확인 하려는 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | [사용자 지정 보고서 보기](entitlement-management-reports.md)<ul><li>요청 시 보기 및 승인 하는</li></ul> |  |

## <a name="approvers"></a>승인자

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>그룹, 응용 프로그램 또는 SharePoint 사이트에 액세스 하는 요청을 승인 하려면 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** [내 액세스 포털에서 열기 요청](entitlement-management-request-approve.md#open-request) | [![내 액세스 포털 아이콘](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [액세스 요청 승인](entitlement-management-request-approve.md#approve-or-deny-request) | ![액세스 승인](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>요청자

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>그룹, 응용 프로그램 또는 SharePoint 사이트 기능을 모두 확인 하 고 액세스를 요청.

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![내 액세스 포털 아이콘](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 액세스 패키지 찾기 |  |
> | **3.** [액세스 요청](entitlement-management-request-access.md#request-an-access-package) | ![액세스 요청](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>외부 사용자와 그룹, 응용 프로그램 또는 직접 링크를 사용 하 여 SharePoint 사이트에 대 한 액세스를 요청 하려면 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** [받은 내 액세스 포털 링크를 찾습니다](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![내 액세스 포털 아이콘](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [액세스 요청](entitlement-management-request-access.md#request-an-access-package) | ![외부 사용자 액세스를 요청 합니다.](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>이미에 대 한 액세스 그룹, 응용 프로그램 또는 SharePoint 사이트를 확인 하려는 하는 경우

> [!div class="mx-tableFixed"]
> | 단계 | 예 |
> | --- | --- |
> | **1.** [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![내 액세스 포털 아이콘](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 현재 액세스 패키지 보기 |  |

## <a name="next-steps"></a>다음 단계

- [자습서: 첫 번째 액세스 패키지 만들기](entitlement-management-access-package-first.md)
- [기존 액세스 패키지 편집 및 관리](entitlement-management-access-package-edit.md)
