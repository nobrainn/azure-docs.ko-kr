---
title: 사용자 지정 정책을 사용 하 여 전화 등록 및 로그인 (미리 보기)
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 응용 프로그램 사용자의 휴대폰으로 문자 메시지의 OTP (일회성 암호)를 보냅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d432912cb0442744061500fc01bdd86a4c5d97ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385351"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Azure AD B2C (미리 보기)에서 사용자 지정 정책을 사용 하 여 전화 등록 및 로그인 설정

Azure Active Directory B2C (Azure AD B2C)의 전화 등록 및 로그인을 사용 하면 문자 메시지에서 휴대폰으로 전송 되는 OTP (일회용 암호)를 사용 하 여 사용자가 응용 프로그램에 등록 하 고 로그인 할 수 있습니다. 일회용 암호를 통해 사용자가 암호를 잊어버리거나 암호를 손상 시킬 수 있는 위험을 최소화할 수 있습니다.

사용자 지정 정책을 사용 하 여 고객이 휴대폰으로 전송 된 일회용 암호를 사용 하 여 응용 프로그램에 등록 하 고 로그인 할 수 있도록 하려면이 문서의 단계를 수행 합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>가격 책정

SMS 문자 메시지를 사용 하 여 사용자에 게 일회용 암호를 보내고, 전송 된 각 메시지에 대해 요금이 청구 될 수 있습니다. 가격 책정 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)의 **별도 요금** 섹션을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

OTP를 설정 하기 전에 다음 리소스를 준비 해야 합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테 넌 트에 [등록 된 웹 응용 프로그램](tutorial-register-applications.md)
* 테 넌 트에 업로드 된 [사용자 지정 정책](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>전화 등록 & 로그인 시작 팩 가져오기

Azure AD B2C 테 넌 트와 함께 작동 하도록 전화 등록 및 로그인 사용자 지정 정책 파일을 업데이트 하 여 시작 합니다.

다음 단계에서는 [필수 구성 요소](#prerequisites) 를 완료 하 고 [사용자 지정 정책 시작 팩][starter-pack] 리포지토리를 로컬 컴퓨터에 이미 복제 했다고 가정 합니다.

1. 시작 팩 리포지토리의 로컬 클론에서 [전화 등록 및 로그인 사용자 지정 정책 파일][starter-pack-phone] 을 찾거나 직접 다운로드 합니다. XML 정책 파일은 다음 디렉터리에 있습니다.

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 각 파일에서 문자열을 `yourtenant` Azure AD B2C 테 넌 트의 이름으로 바꿉니다. 예를 들어 B2C 테 넌 트의 이름이 *contosob2c*인 경우의 모든 인스턴스는가 됩니다 `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .

1. [Azure Active Directory B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)의 [사용자 지정 정책에 응용 프로그램 id 추가](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) 섹션에 설명 된 단계를 완료 합니다. 이 경우, `/phone-number-passwordless/` **`Phone_Email_Base.xml`** *IdentityExperienceFramework* 및 *ProxyIdentityExperienceFramework*필수 구성 요소를 완료할 때 등록 한 두 응용 프로그램의 **응용 프로그램 (클라이언트) id** 로 업데이트 합니다.

## <a name="upload-the-policy-files"></a>정책 파일을 업로드 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 Azure AD B2C 테 넌 트로 이동 합니다.
1. **정책**에서 **Identity Experience Framework**를 선택합니다.
1. **사용자 지정 정책 업로드**를 선택합니다.
1. 다음 순서로 정책 파일을 업로드 합니다.
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

각 파일을 업로드 하면 Azure에서 접두사를 추가 `B2C_1A_` 합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책**에서 **B2C_1A_SignUpOrSignInWithPhone**를 선택 합니다.
1. **응용 프로그램 선택**에서 필수 구성 요소를 완료할 때 등록 한 *webapp1* 응용 프로그램을 선택 합니다.
1. **회신 Url 선택**에 대해를 선택 `https://jwt.ms` 합니다.
1. **지금 실행** 을 선택 하 고 전자 메일 주소 또는 전화 번호를 사용 하 여 등록 합니다.
1. **지금 실행** 을 선택 하 고 동일한 계정으로 로그인 하 여 구성이 올바른지 확인 합니다.

## <a name="get-user-account-by-phone-number"></a>전화 번호로 사용자 계정 가져오기

전화 번호를 사용 하 여 등록 하지만 복구 전자 메일 주소를 제공 하지 않는 사용자는 전화 번호를 로그인 이름으로 사용 하 여 Azure AD B2C 디렉터리에 기록 됩니다. 사용자가 전화 번호를 변경 하려는 경우 지원 센터 또는 지원 팀은 먼저 자신의 계정을 찾은 다음 전화 번호를 업데이트 해야 합니다.

[Microsoft Graph](manage-user-accounts-graph-api.md)를 사용 하 여 전화 번호 (로그인 이름)로 사용자를 찾을 수 있습니다.

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

예를 들면 다음과 같습니다.

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>다음 단계

GitHub에서 전화 등록 및 로그인 사용자 지정 정책 시작 팩 (및 기타 스타터 팩)을 찾을 수 있습니다.

[Azure-Samples/b2c-starterpack/시나리오/전화 번호-암호 less][starter-pack-phone]

시작 팩 정책 파일은 multi-factor authentication 기술 프로필 및 전화 번호 클레임 변환을 사용 합니다.

* [Azure Multi-Factor Authentication 기술 프로필 정의](multi-factor-auth-technical-profile.md)
* [전화 번호 클레임 변환 정의](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
