---
title: 특정 사용자가 앱에 액세스할 수 있는 시기를 확인 합니다.
description: 매우 중요한 사용자가 Azure AD를 사용하여 사용자를 프로비저닝하도록 구성한 애플리케이션에 액세스할 수 있는 시기를 찾는 방법
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: a8f712604ae854a1870fe96112db3ae0f2ed60a8
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203007"
---
# <a name="check-the-status-of-user-provisioning"></a>사용자 프로 비전 상태를 확인 합니다.

Azure AD 프로 비전 서비스는 원본 시스템 및 대상 시스템에 대해 초기 프로 비전 주기를 실행 한 다음 주기적 증분 주기를 실행 합니다. 앱에 대 한 프로 비전을 구성 하는 경우 프로 비전 서비스의 현재 상태를 확인 하 고 사용자가 앱에 액세스할 수 있는 시기를 확인할 수 있습니다.

## <a name="view-the-provisioning-progress-bar"></a>프로 비전 진행률 표시줄 보기

 앱에 대 한 **프로 비전** 페이지에서 Azure AD 프로 비전 서비스의 상태를 볼 수 있습니다. 페이지 아래쪽의 **현재 상태** 섹션에는 프로 비전 주기가 사용자 계정 프로 비전을 시작 했는지 여부가 표시 됩니다. 주기의 진행 상태를 보고, 프로 비전 된 사용자 및 그룹의 수를 확인 하 고, 생성 된 역할 수를 확인할 수 있습니다.

자동 프로 비전을 처음 구성 하는 경우 페이지의 맨 아래에 있는 **현재 상태** 섹션에 초기 프로 비전 주기 상태가 표시 됩니다. 이 섹션은 증분 주기가 실행 될 때마다 업데이트 됩니다. 다음과 같은 세부 정보가 표시 됩니다.
- 현재 실행 중이거나 마지막으로 완료 된 프로 비전 주기 (초기 또는 증분)의 유형입니다.
- 완료 된 프로 비전 주기의 비율을 보여 주는 **진행률 표시줄** 입니다. 비율은 프로 비전 된 페이지 수를 나타냅니다. 각 페이지에는 여러 사용자 또는 그룹이 포함 될 수 있으므로이 비율은 프로 비전 된 사용자, 그룹 또는 역할의 수와 직접적으로 연관 되지 않습니다.
- 보기를 업데이트 된 상태로 유지 하는 데 사용할 수 있는 **새로 고침** 단추입니다.
- 커넥터 데이터 저장소에 있는 **사용자** 및 **그룹** 의 수입니다. 프로 비전 범위에 개체가 추가 될 때마다 수가 늘어납니다. 사용자가 일시 삭제 되었거나 하드 삭제 된 경우에는 커넥터 데이터 저장소에서 개체가 제거 되지 않으므로 개수가 중단 되지 않습니다. 이 수는 CD가 [다시 설정](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 된 후 첫 번째 동기화가 다시 계산 됩니다. 
- 개별 사용자에 대 한 [프로 비전 상태](#use-provisioning-logs-to-check-a-users-provisioning-status) 를 포함 하 여 사용자 프로 비전 서비스에서 실행 하는 모든 작업에 대 한 자세한 내용은 Azure AD 프로 비전 로그를 여는 **감사 로그 보기** 링크를 참조 하세요.

프로 비전 주기가 완료 된 후 **날짜 통계** 섹션에는 마지막 주기의 완료 날짜 및 기간과 함께 날짜에 프로 비전 된 사용자 및 그룹의 누적 수가 표시 됩니다. **활동 ID** 는 가장 최근의 프로 비전 주기를 고유 하 게 식별 합니다. **작업 ID** 는 프로 비전 작업의 고유 식별자 이며, 테 넌 트의 앱에만 적용 됩니다.

프로 비전 진행률은 Azure Portal **Azure Active Directory &gt; Enterprise Apps &gt; \[ 응용 프로그램 이름 \] &gt; 프로 비전** 탭에서 볼 수 있습니다.

![프로 비전 페이지 진행률 표시줄](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>프로 비전 로그를 사용 하 여 사용자의 프로 비전 상태 확인

선택한 사용자에 대 한 프로 비전 상태를 확인 하려면 Azure AD의 [프로 비전 로그 (미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 를 참조 하세요. 사용자 프로 비전 서비스에 의해 실행 되는 모든 작업은 Azure AD 프로 비전 로그에 기록 됩니다. 여기에는 원본 및 대상 시스템에 대 한 모든 읽기 및 쓰기 작업과 각 작업 중에 읽거나 쓴 사용자 데이터가 포함 됩니다.

**Azure Active Directory** &gt; 작업 섹션에서 Azure Active Directory **Enterprise Apps** &gt; **프로 비전 로그 (미리 보기)** **Activity** 를 선택 하 여 Azure Portal에서 프로 비전 로그에 액세스할 수 있습니다. 원본 시스템이 나 대상 시스템에서 사용자의 이름 또는 식별자를 기반으로 프로 비전 데이터를 검색할 수 있습니다. 자세한 내용은 [로그 프로 비전 (미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)을 참조 하세요. 

프로 비전 로그는 프로 비전 서비스에서 수행 하는 모든 작업을 기록 합니다. 예를 들면 다음과 같습니다.

* 프로비저닝 범위에 있는 할당된 사용자에 대해 Azure AD 쿼리
* 해당 사용자의 존재에 대해 대상 앱 쿼리
* 시스템 간의 사용자 개체 비교
* 비교를 기반으로 대상 시스템에서 사용자 계정 추가, 업데이트 또는 비활성화

Azure Portal에서 프로 비전 로그를 읽는 방법에 대 한 자세한 내용은 [프로 비전 보고 가이드](check-status-user-account-provisioning.md)를 참조 하세요.

## <a name="how-long-will-it-take-to-provision-users"></a>사용자를 프로비전하는 데 걸리는 시간은 어느 정도인가요?
응용 프로그램과 함께 자동 사용자 프로 비전을 사용 하는 경우 Azure AD는 정기적으로 예약 된 시간 간격 (일반적으로 40 분 마다)으로 [사용자 및 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 같은 항목에 따라 앱의 사용자 계정을 자동으로 프로 비전 하 고 업데이트 합니다.

지정 된 사용자를 프로 비전 하는 데 걸리는 시간은 주로 프로 비전 작업에서 초기 주기 또는 증분 주기를 실행 하 고 있는지 여부에 따라 달라 집니다.

- **초기 주기**에서 작업 시간은 프로 비전 범위에 있는 사용자 및 그룹 수와 원본 시스템의 총 사용자 및 그룹 수를 비롯 한 여러 요인에 따라 달라 집니다. Azure AD와 앱 간의 첫 번째 동기화는 Azure AD 디렉터리의 크기와 프로비저닝 범위에 있는 사용자 수에 따라 20분에서 몇 시간이 걸릴 수 있습니다. 초기 주기 성능에 영향을 주는 포괄적인 요소 목록은이 섹션의 뒷부분에서 간략하게 설명 합니다.

- 초기 주기 이후 **증분 주기의** 경우 프로 비전 서비스는 초기 주기 후 두 시스템의 상태를 나타내는 워터 마크를 저장 하므로 이후 동기화의 성능이 향상 되기 때문에 작업 시간이 더 빠를 수 있습니다 (예: 10 분 이내). 작업 시간은 해당 프로 비전 주기에서 검색 된 변경 수에 따라 달라 집니다. 사용자 또는 그룹 멤버 자격 변경 내용이 5000 미만이 면 단일 증분 프로 비전 주기 내에서 작업을 완료할 수 있습니다. 

다음 표에는 일반적인 프로비저닝 시나리오의 동기화 시간이 요약되어 있습니다. 이러한 시나리오에서 원본 시스템은 Azure AD이고, 대상 시스템은 SaaS 애플리케이션입니다. 동기화 시간은 SaaS 응용 프로그램 ServiceNow, 작업 공간, Salesforce 및 G Suite에 대 한 동기화 작업의 통계 분석에서 파생 됩니다.


| 범위 구성 | 범위 내 사용자, 그룹 및 멤버 | 초기 주기 시간 | 증분 주기 시간 |
| -------- | -------- | -------- | -------- |
| 할당된 사용자 및 그룹만 동기화 |  1,000 미만 |  30분 미만 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |  1,000 - 10,000 | 142 - 708분 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |   10,000 - 100,000 | 1,170 - 2,340분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 미만 | 30분 미만  | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 - 10,000 | 30 - 120분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  10,000 - 100,000  | 713 - 1,425분 | 30분 미만 |
| Azure AD의 모든 사용자 동기화|  1,000 미만  | 30분 미만 | 30분 미만 |
| Azure AD의 모든 사용자 동기화 | 1,000 - 10,000  | 43 - 86분 | 30분 미만 |

구성 **동기화 할당 된 사용자 및 그룹**의 경우 다음 수식을 사용 하 여 예상 되는 최소 및 최대 **초기 주기** 시간을 확인할 수 있습니다.

- 최소 시간 (분) = 0.01 x [할당 된 사용자, 그룹 및 그룹 구성원 수]
- 최대 시간 (분) = 0.08 x [할당 된 사용자, 그룹 및 그룹 구성원 수]

**초기 주기**를 완료 하는 데 걸리는 시간에 영향을 주는 요인을 요약 한 것입니다.

- 프로비저닝 범위에 속하는 사용자 및 그룹의 총 수

- 원본 시스템(Azure AD)에 있는 사용자, 그룹 및 그룹 멤버의 총 수

- 프로 비전 범위에 있는 사용자가 대상 응용 프로그램의 기존 사용자와 일치 하는지 아니면 처음으로 생성 되어야 하는지 여부입니다. 모든 사용자가 처음으로 만들어진 동기화 작업은 모든 사용자가 기존 사용자와 일치 하는 동기화 작업의 경우 *두 배의* 시간이 걸립니다.

- [프로 비전 로그](check-status-user-account-provisioning.md)의 오류 수입니다. 많은 오류가 있고 프로비저닝 서비스가 격리 상태로 전환된 경우 성능이 저하됩니다. 

- 대상 시스템에서 구현된 요청 비율 한도 및 제한. 일부 대상 시스템은 대량 동기화 작업 중에 성능에 영향을 줄 수 있는 요청 속도 제한 및 제한을 구현 합니다. 이러한 조건에서 너무 많은 요청을 너무 빠르게 받는 앱은 응답 속도가 느려지거나 연결을 닫을 수도 있습니다. 성능을 개선하려면 커넥터가 앱이 처리할 수 있는 속도보다 빠르게 앱 요청이 전송되지 않도록 조정해야 합니다. Microsoft에서 빌드한 프로비저닝 커넥터는 이러한 조정 작업을 수행합니다. 

- 할당된 그룹 수 및 크기. 할당된 그룹 동기화가 사용자 동기화보다 시간이 오래 걸립니다. 할당된 그룹 수와 크기는 둘 다 성능에 영향을 줍니다. 애플리케이션에 [그룹 개체 동기화가 사용되는 매핑](customize-application-attributes.md#editing-group-attribute-mappings)이 있는 경우 그룹 이름 및 멤버 자격과 같은 그룹 속성이 사용자와 함께 동기화됩니다. 이러한 추가 동기화 때문에 사용자 객체만 동기화하는 것보다 시간이 오래 걸립니다.

- 성능이 문제가 되 고 테 넌 트에서 대부분의 사용자 및 그룹을 프로 비전 하려는 경우 범위 지정 필터를 사용 합니다. 범위 지정 필터를 사용하면 특정 특성 값에 따라 사용자를 필터링하여 프로비저닝 서비스가 Azure AD에서 추출하는 데이터를 미세 조정할 수 있습니다. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용 하 여 SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전 해제 자동화](user-provisioning.md)
