---
title: Windows 가상 데스크톱 (클래식) 웹 클라이언트 연결-Azure
description: 웹 클라이언트를 사용 하 여 Windows 가상 데스크톱 (클래식)에 연결 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: efe97c86ebfac8e130489b3105a97302866d6822
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270382"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>웹 클라이언트를 사용 하 여 Windows 가상 데스크톱 (클래식)에 연결

>[!IMPORTANT]
>이 콘텐츠는 windows 가상 데스크톱 개체 Azure Resource Manager를 지원 하지 않는 Windows 가상 데스크톱 (클래식)에 적용 됩니다. Azure Resource Manager Windows 가상 데스크톱 개체를 관리 하려는 경우 [이 문서](../connect-web.md)를 참조 하세요.

웹 클라이언트를 사용하면 시간이 오래 걸리는 설치 프로세스 없이 웹 브라우저에서 Windows Virtual Desktop 리소스에 액세스할 수 있습니다.

>[!NOTE]
>웹 클라이언트에서는 현재 모바일 OS를 지원하지 않습니다.

## <a name="supported-operating-systems-and-browsers"></a>지원되는 운영 체제 및 브라우저

HTML5 지원 브라우저가 제대로 작동하는 동안에는 다음 운영 체제 및 브라우저를 공식적으로 지원합니다.

| 브라우저           | 지원되는 OS                     | 메모               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 버전 55 이상 |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>원격 리소스 피드에 액세스

브라우저에서 <https://rdweb.wvd.microsoft.com/webclient>의 Windows Virtual Desktop 웹 클라이언트로 이동한 후 사용자 계정으로 로그인합니다.

>[!NOTE]
>Azure Resource Manager 통합을 통해 Windows 가상 데스크톱을 사용 하는 경우에는에서 리소스에 연결 <https://rdweb.wvd.microsoft.com/arm/webclient> 합니다.

>[!NOTE]
>Windows Virtual Desktop에 사용하려는 계정이 아닌 Azure Active Directory 계정으로 이미 로그인한 경우에는 로그아웃하거나 프라이빗 브라우저 창을 사용해야 합니다.

이제 로그인한 후에는 리소스 목록이 표시됩니다. **모든 리소스** 탭에서 일반 앱과 같이 선택하여 리소스를 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

웹 클라이언트를 사용하는 방법에 대한 자세한 내용은 [웹 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)을 참조하세요.
