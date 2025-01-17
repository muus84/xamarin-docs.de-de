---
title: Fehlende Visual Studio-Erweiterungen nach der Installation
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 6cd5acb529d617c72d8c9e1bd642a831f90b2e8c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013746"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Fehlende Visual Studio-Erweiterungen nach der Installation

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Fehlermeldung: dieses Projekt ist mit der aktuellen Edition von Visual Studio nicht kompatibel.

Stellen Sie sicher, dass Visual Studio 2017 (Community, Professional oder Enterprise) oder neuer installiert ist.

Siehe auch die [Windows-Anforderungen](~/cross-platform/get-started/requirements.md#windows-requirements).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Mögliche Korrektur 1: Ändern Sie die Installation, um sicherzustellen, dass die Visual Studio-Erweiterungen installiert sind.

In bestimmten Situationen kann das xamarin-Installationsprogramm automatisch die Installationsoptionen für die Visual Studio-Erweiterungen deaktivieren. Wenn dies die Ursache des Problems ist, installieren Sie die fehlenden Visual Studio-Erweiterungen mit dem **Änderungs** Befehl des Installers. So installieren Sie z. b. die Erweiterungen für Visual Studio 2013:

1. Öffnen Sie die Systemsteuerung " **Programme und Funktionen** " von Windows.

2. Klicken Sie mit der rechten Maustaste auf den Eintrag **xamarin** , und wählen Sie **ändern**.

3. Klicken Sie auf **weiter**und dann auf **ändern**.

4. Stellen Sie sicher, dass die Option **xamarin für Visual Studio 2013** auf Installieren festgelegt ist:

    ![](missing-vs-extensions-images/installer.png "Enable Xamarin for Visual Studio 2013 installation option")

5. Fahren Sie mit dem Rest des Installations-Assistenten fort.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Mögliche Korrektur 2: bitten Sie Visual Studio, die Erweiterungen erneut einzurichten.

1. Überprüfen Sie, ob die xamarin-Erweiterungen in den Visual Studio-Ordner Erweiterungen kopiert wurden:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Wenn die Erweiterungen ordnungsgemäß installiert sind (für Version 3.1.228), befinden sich 60 Elemente im Ordner:

    ![](missing-vs-extensions-images/folder.png "List of 'Xamarin\3.1.228.0' folder contents in Explorer")

2. Nachdem Sie sich vergewissert haben, dass dieser Ordner korrekt aussieht, teilen Sie Visual Studio mit, dass Sie die Erweiterungen erneut einrichten:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Mögliche Korrektur 3: Testen Sie die Neuinstallation von xamarin.

1. Deinstallieren Sie in der Windows-Systemsteuerung eine der folgenden Elemente:

    * Xamarin

    * Xamarin für Windows

    * Xamarin.Android

    * Xamarin.iOS

    * Xamarin für Visual Studio

2. Löschen Sie in Explorer alle verbleibenden Dateien aus den xamarin Visual Studio-Erweiterungs Ordnern (alle Versionen, einschließlich der **Programmdateien** und der **Programmdateien (x86)** ):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3. Überprüfen Sie auch das Verzeichnis "virtualstore", um festzustellen, ob eine "Overlay"-Kopie von einem der Erweiterungs Verzeichnisse vorhanden sein kann:

    `%LOCALAPPDATA%\VirtualStore`

4. Öffnen Sie den Registrierungs-Editor (regedit).

5. Suchen Sie nach diesem Schlüssel:

    _HKEY\_lokalen\_machine\software\wow6432node\microsoft\windows\currentversion\shareddlls_

6. Suchen und löschen Sie alle Einträge, die diesem Muster entsprechen:

    _C:\Programme\*\Microsoft Visual Studio 1\*. 0 \ Common7\IDE\Extensions\Xamarin_

7. Suchen Sie nach diesem Schlüssel:

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8. Löschen Sie alle Einträge, die zu Xamarin gehören könnten. Hier ist beispielsweise eine solche, die verwendet wird, um in älteren Versionen von xamarin Probleme zu verursachen:

    _Mono. VisualStudio. Shell, 1.0_

9. Starten Sie den Computer neu.

10. Installieren Sie die aktuelle stabile Version von xamarin von [VisualStudio.com](https://visualstudio.com/xamarin)neu.

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Mögliche Korrektur 4: Reparieren der Visual Studio-Installation

1. Öffnen Sie die Systemsteuerung " **Programme und Funktionen** " von Windows.

2. Klicken Sie mit der rechten Maustaste auf den Microsoft Visual Studio entsprechenden Eintrag, und wählen Sie **ändern**

3. Klicken Sie im Visual Studio-Dialogfeld auf die Schaltfläche **Reparieren** .
