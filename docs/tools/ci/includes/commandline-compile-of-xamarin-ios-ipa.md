---
ms.openlocfilehash: 31c8d1b781648f2d9c69062c52e71478fc7a496b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "69529118"
---

In der folgenden Befehlszeile wird ein Releasebuild der Projekt Mappe " **SOLUTION_FILE. sln** " für das iPhone angegeben. Der Speicherort der IPA kann festgelegt werden, indem Sie die `IpaPackageDir`-Eigenschaft in der Befehlszeile angeben:

- Auf dem Mac mit **xbuild**:

  ```
  xbuild /p:Configuration="Release" \ 
         /p:Platform="iPhone" \ 
         /p:IpaPackageDir="$HOME/Builds" \
         /t:Build MyProject.sln
  ```

Der **xbuild** -Befehl befindet sich in der Regel im Verzeichnis **/Library/Frameworks/Mono.Framework/Commands**.

- Unter Windows mit **MSBuild**:

  ```
  msbuild /p:Configuration="Release" 
          /p:Platform="iPhone" 
          /p:IpaPackageDir="%USERPROFILE%\Builds" 
          /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
          /t:Build MyProject.sln
  ```

**MSBuild** erweitert nicht automatisch `$( )` Ausdrücke, die von der Befehlszeile übermittelt werden. Aus diesem Grund wird empfohlen, beim Festlegen des `IpaPackageDir` in der Befehlszeile einen vollständigen Pfad zu verwenden.

Weitere Informationen zur `IpaPackageDir`-Eigenschaft finden Sie in den [Anmerkungen zu dieser Version für IOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) .
