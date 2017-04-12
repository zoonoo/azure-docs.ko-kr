---
title: "Azure Active Directory 보고 FAQ | Microsoft Docs"
description: "Azure Active Directory 보고 FAQ입니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: e6dcd3f6f9c7c8765409c3b0d50e4b3843bab5c6
ms.openlocfilehash: e39ee63d190308b87ebeb43adeb8b3e5db86df57
ms.lasthandoff: 02/22/2017


---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory 보고 FAQ

이 문서에는 Azure Active Directory 보고에 대한 FAQ(질문과 대답)가 포함되어 있습니다.  
자세한 내용은 [Azure Active Directory 보고](active-directory-reporting-azure-portal.md)를 참조하세요. 

**Q: Azure Portal에서 활동 로그 (감사 및 로그인)의 데이터 보존은 어떻게 됩니까?** 

**A:** 무료 고객의 경우 7일의 데이터 보존을 제공하고 Azure AD Premium 1 또는 2 Premium 라이선스로 전환하면 최대 30일간 데이터에 액세스할 수 있습니다. 보존에 대한 자세한 내용은 [Azure Active Directory 보고서 보존 정책](active-directory-reporting-retention.md)을 참조하세요.

--- 

**Q: 내 작업을 완료한 후 얼마나 지나야 활동 데이터를 볼 수 있습니까?**

**A:** 감사 활동 로그에는 15분에서 1시간까지의 대기 시간이 있습니다. 로그인 활동 로그의 대기 시간은 15분(대부분의 레코드)에서 최대 2시간(일부 레코드)입니다.

---

**Q: Azure Portal에서 해당 활동을 보거나 API를 통해 데이터를 가져오기 위해 전역 관리자 권한이 필요합니까?**

**A:** 아니요. **보안 읽기 권한자**, **보안 관리자** 또는 **전역 관리자**라면 Azure Portal에서 데이터를 보거나 API를 통해 액세스하여 데이터를 볼 수 있습니다.

---

**Q: Azure Portal을 통해 Office 365 활동 로그 정보를 얻을 수 있습니까?**

**A:** Office 365 활동 및 Azure AD 활동 로그가 많은 디렉터리 리소스를 공유하더라도 Office 365 활동 로그를 전체 보기로 보려는 경우, Office 365 관리 센터로 이동하여 Office 365 활동 로그 정보를 얻을 수 있습니다.

---


**Q: Office 365 활동 로그에 대한 정보를 얻기 위해 어떤 API를 사용해야 합니까?**

**A:** Office 365 관리 API를 사용하여 [API를 통해 Office 365 활동 로그](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview)에 액세스합니다.

---

**Q: Azure Portal에서 몇 개의 레코드를 다운로드할 수 있습니까?**

**A:** Azure Portal에서 최대 120,000개의 레코드를 다운로드할 수 있습니다. 레코드는 *가장 최근* 순으로 정렬되며 기본적으로 가장 최근의 120,000개의 레코드를 볼 수 있습니다. 

---

**Q: API 활동을 통해 몇 개의 레코드를 쿼리할 수 있습니까?**

**A:** 최대 1백만 개의 레코드를 쿼리할 수 있습니다(top 연산자를 사용하지 않는 경우 가장 최근 순으로 레코드를 정렬함). "top" 연산자를 사용하면 500,000개의 레코드를 쿼리할 수 있습니다. 여기[여기](active-directory-reporting-api-getting-started.md)에서 API를 사용하는 방법에 대한 샘플 쿼리를 찾을 수 있습니다.

---

**Q: Premium 라이선스는 어떻게 받을 수 있나요?**

**A:** 이 질문에 대한 답변은 [Azure Active Directory Premium 시작하기](active-directory-get-started-premium.md)를 참조하세요.

---

**Q: Premium 라이선스를 받은 후 활동 데이터를 보는 데 얼마나 걸리나요?**

**A:** 무료 라이선스로서 활동 데이터가 이미 있는 경우 동일한 데이터를 볼 수 있습니다. 모든 데이터가 없으면&1;일이나&2;일 정도 걸립니다.

---

**Q: Azure AD Premium 라이선스를 받은 후 지난 달의 데이터를 볼 수 있습니까?**

**A:**: 최근에 Premium 버전(평가판 버전을 포함)으로 전환한 경우 최대 7일간 데이터를 볼 수 있습니다. 데이터가 누적된 경우 최대 30일간 볼 수 있습니다.

 
---


