---
title: B2B 공동 작업 사용자에 대 한 조건부 액세스-Azure AD
description: Azure Active Directory B2B 협업 기능은 회사 애플리케이션에 대한 선택적 액세스를 위해 MFA(Multi-Factor Authentication)를 지원합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909588"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대 한 조건부 액세스

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B 사용자에 대한 다단계 인증
Azure AD B2B 협업을 통해 조직에서는 B2B 사용자에 대한 MFA(Multi-Factor Authentication) 정책을 적용할 수 있습니다. 조직의 전일제 직원과 구성원에 대해 이러한 정책을 사용하는 것과 같은 방법으로 이러한 정책을 테넌트, 앱 또는 개별 사용자 수준에서 적용할 수 있습니다. MFA 정책은 리소스 조직에서 적용됩니다.

예:
1. 회사 A의 관리자 또는 정보 근로자가 회사 B의 사용자를 회사 A의 *Foo* 애플리케이션에 초대합니다.
2. 회사 A의 애플리케이션 *Foo*는 액세스 시 MFA를 요구하도록 구성됩니다.
3. 회사 B의 사용자가 회사 A 테넌트에서 *Foo* 앱에 액세스하려고 하면 MFA 챌린지를 완료하도록 요구됩니다.
4. 사용자는 회사 A와 MFA를 설정할 수 있고 해당 MFA 옵션을 선택합니다.
5. 이러한 시나리오는 어떤 ID에도 잘 맞습니다(Azure AD 또는 회사 B의 사용자가 소셜 ID를 사용하여 인증을 받는 경우 MSA).
6. 회사 A는 MFA를 지원하는 충분한 Azure AD 프리미엄 라이선스가 있어야 합니다. 회사 B의 사용자는 회사 A의 이 라이선스를 사용합니다.

파트너 조직에 MFA 기능이 있더라도 초대하는 테넌시는 항상 파트너 조직 사용자의 MFA를 책임집니다.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B 협업 사용자에 대한 MFA 설정
B2B 협업 사용자에 대한 MFA를 설정하는 작업이 얼마나 간단한지 알아보려면 다음 비디오를 시청하세요.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B 사용자는 제안 상환을 위해 MFA 환경을 사용합니다.
다음 애니메이션을 통해 상환 환경을 확인할 수 있습니다.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B 협업 사용자에 대해 다시 설정된 MFA
현재 관리자는 다음 PowerShell cmdlet을 사용하여 B2B 협업 사용자에게 다시 입증하도록 요구할 수 있습니다.

1. Azure에 연결

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. 증명 방법이 있는 모든 사용자 가져오기

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   다음은 예제입니다.

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. 특정 사용자에 대한 MFA 방법을 다시 설정하여 B2B 협업 사용자가 증명 방법을 다시 설정하도록 합니다. 예:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>리소스 테넌트에서 MFA를 수행하는 이유는 무엇인가요?

현재 릴리스에서 MFA는 예측이 가능하도록 하기 위해 항상 리소스 테넌트에 있습니다. 예를 들어 Contoso 사용자(Sally)가 Fabrikam에 초대되고 Fabrikam이 B2B 사용자에 대한 MFA를 사용하도록 설정했다고 가정해 보겠습니다.

Contoso에 App2가 아닌 App1에 대해서만 설정된 MFA 정책이 있는 경우 토큰의 Contoso MFA 클레임에서 다음 문제가 확인될 수 있습니다.

* 1일: Contoso의 한 사용자에게 MFA가 있고 이 사용자가 App1에 액세스하는 경우 Fabrikam에 추가 MFA 프롬프트가 표시되지 않습니다.

* 2일: 이 사용자는 Contoso의 App2에 액세스할 수 있지만 이제 Fabrikam에 액세스할 때 MFA에 등록해야 합니다.

이 프로세스는 혼동을 일으킬 수 있으며 로그인이 중단될 수도 있습니다.

도한 Contoso에 MFA 기능이 있는 경우에도 Fabrikam에서 항상 Contoso MFA 정책을 신뢰하는 것은 아닙니다.

마지막으로 리소스 테넌트 MFA는 MSA 및 소셜 ID에도 적용되고 MFA가 설정되지 않은 파트너 조직에도 적용됩니다.

따라서 B2B 사용자의 경우 항상 초대하는 테넌트의 MFA를 요구하는 것이 좋습니다. 이렇게 하면 MFA가 2개가 될 수도 있지만 초대하는 테넌트에 액세스할 때마다 최종 사용자 경험을 예측할 수 있습니다. Sally는 초대하는 테넌트를 사용하여 MFA에 등록해야 합니다.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>B2B 사용자에 대 한 장치 기반, 위치 기반 및 위험 기반 조건부 액세스

Contoso가 회사 데이터에 대 한 장치 기반 조건부 액세스 정책을 사용 하도록 설정 하면 contoso에서 관리 되지 않고 Contoso 장치 정책을 준수 하지 않는 장치에서 액세스가 차단 됩니다.

B2B 사용자의 디바이스가 Contoso에서 관리되지 않는 경우 이러한 정책이 적용된 컨텍스트가 무엇이든 파트너 조직 B2B 사용자의 액세스는 차단됩니다. 그러나 Contoso는 장치 기반 조건부 액세스 정책에서 제외할 특정 파트너 사용자가 포함 된 제외 목록을 만들 수 있습니다.

#### <a name="mobile-application-management-policies-for-b2b"></a>B2B에 대 한 모바일 응용 프로그램 관리 정책

초대 하는 조직이 B2B 사용자의 홈 조직을 볼 수 없기 때문에 조건부 액세스 앱 보호 정책을 B2B 사용자에 게 적용할 수 없습니다.

#### <a name="location-based-conditional-access-for-b2b"></a>B2B에 대 한 위치 기반 조건부 액세스

초대 하는 조직에서 파트너 조직을 정의 하는 신뢰할 수 있는 IP 주소 범위를 만들 수 있는 경우 위치 기반 조건부 액세스 정책을 B2B 사용자에 게 적용할 수 있습니다.

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B에 대 한 위험 기반 조건부 액세스

위험 평가는 B2B 사용자의 홈 조직에서 수행되기 때문에 현재는 위험 기반 로그인 정책을 B2B 사용자에게 적용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

* [Azure AD B2B 협업이란?](what-is-b2b.md)
* [Azure AD B2B 협업 라이선스](licensing-guidance.md)
* [Azure Active Directory B2B 협업 자주 묻는 질문(FAQ)](faq.md)
