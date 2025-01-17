---
title: Binden von nativen Frameworks
description: In diesem Dokument wird beschrieben, wie Sie die Option "-Framework" von Target Sharpie verwenden, um eine Bindung an eine als Framework verteilte Bibliothek zu erstellen.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 78c489518833705432610e83453c3c04bf1cca53
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016091"
---
# <a name="binding-native-frameworks"></a>Binden von nativen Frameworks

Manchmal wird eine native Bibliothek als [Framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)verteilt. Ziel-Sharpie bietet eine praktische Funktion zum Binden ordnungsgemäß definierter Frameworks über die `-framework`-Option.

Beispielsweise ist die Bindung des [Adobe Creative SDK-Frameworks](https://creativesdk.adobe.com/downloads.html) für IOS unkompliziert:

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

In einigen Fällen gibt ein Framework eine **Info. plist** -Datei an, die angibt, mit welchem SDK das Framework kompiliert werden soll. Wenn diese Informationen vorhanden sind und keine explizite `-sdk` Option übergeben wird, leitet der Ziel-Sharpie ihn aus der " **Info. plist** "-Datei des Frameworks (`DTSDKName` Schlüssel oder einer Kombination aus der `DTPlatformName` und `DTPlatformVersion` Schlüssel) ab.

Die Option `-framework` lässt nicht zu, dass explizite Header Dateien übermittelt werden. Die Header Datei des Headers wird anhand der Konvention basierend auf dem frameworknamen ausgewählt. Wenn ein Ober Schirm nicht gefunden werden kann, versucht der Ziel-Sharpie nicht, das Framework zu binden, und Sie müssen die Bindung manuell ausführen, indem Sie die richtigen zu pargenden Header Dateien und alle frameworgumente für clang bereitstellen (z. b. die `-F` frameworksuchpfad-Option).

Im Hintergrund ist das Angeben von `-framework` nur eine Verknüpfung. Die folgenden Bindungs Argumente sind identisch mit der `-framework` Kurzzeit oben.
Eine besondere Wichtigkeit ist der für clang bereitgestellte Suchpfad für `-F .` Framework (Beachten Sie den Bereich und den Zeitraum, der als Teil des Befehls erforderlich ist).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
