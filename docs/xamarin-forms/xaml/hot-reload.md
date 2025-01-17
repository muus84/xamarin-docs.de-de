---
title: XAML-Hot-Upload für xamarin. Forms
description: Erneutes Laden von Änderungen an ihrer XAML-Datei in der laufenden Anwendung, sodass Sie das xamarin. Forms-Projekt nicht nach jeder XAML-Änderung erstellen müssen.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 020b8208dfbfd7594d152a7b6e32f7e41f974c2f
ms.sourcegitcommit: 43423d4018cc0d4b0b8c98a4b3da0704495eb0cf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303257"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>XAML-Hot-Upload für xamarin. Forms (Vorschau)

![Feature der Vorschauversion](~/media/shared/preview.png)

XAML Hot Upload wird in Ihren vorhandenen Workflow integriert, um Ihre Produktivität zu steigern und Zeit zu sparen. Ohne das erneute Laden von XAML müssen Sie die APP jedes Mal erstellen und bereitstellen, wenn Sie eine XAML-Änderung sehen möchten. Wenn Sie beim Laden der XAML-Datei die XAML-Datei speichern, werden die Änderungen in der laufenden app Live übernommen. Außerdem werden der Navigations Zustand und die Daten beibehalten, sodass Sie Ihre Benutzeroberfläche schnell durchlaufen können, ohne Ihre Position in der APP zu verlieren. Mit XAML Hot Neuladen verbringen Sie daher weniger Zeit mit der Neuerstellung und Bereitstellung Ihrer Apps, um Änderungen an der Benutzeroberfläche zu überprüfen.

> [!NOTE]
> Wenn Sie eine WPF-oder UWP-app schreiben, finden Sie weitere Informationen unter [XAML-Hot-Upload für UWP und WPF](/visualstudio/debugger/xaml-hot-reload).

## <a name="system-requirements"></a>Systemanforderungen

| IDE/Framework | Erforderliche Version |
|------|------------------|
|Visual Studio 2019 | 16,3 oder höher
Visual Studio 2019 für Mac | 8,3 oder höher
Xamarin.Forms | 4,1 oder höher

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Verwenden von XAML-Hot-Neuladen für xamarin. Forms

Für die Verwendung von XAML Hot Neuladen ist keine zusätzliche Installation oder Einrichtung erforderlich. Es ist in Visual Studio integriert und kann in den IDE-Einstellungen aktiviert werden. Nach der Aktivierung können Sie mit der Verwendung von XAML Hot Neuladen beginnen, indem Sie Ihre APP auf einem Emulator, Simulator oder einem physischen Gerät Debuggen. Derzeit funktioniert das Laden von XAML-Hot nur beim Debuggen unter IOS oder Android.

Unter Windows kann das aktive XAML-Neuladen durch Aktivieren des Kontrollkästchens " **xamarin Hot Neuladen aktivieren** " unter **Extras > ** **Optionen** > **xamarin** > **Hot Neuladen**aktiviert werden.

Auf einem Mac kann das aktive XAML-Neuladen durch Aktivieren des Kontrollkästchens " **xamarin Hot Neuladen aktivieren** " in **Visual Studio** > **Einstellungen** > **Projekten** > **xamarin Hot Neuladen**aktiviert werden.

## <a name="resilient-reloading"></a>Resilientes erneutes Laden

Wenn Sie eine Änderung vornehmen, dass das Laden von XAML-Hot nicht erneut geladen werden kann, wird eine Fehlermeldung mit IntelliSense angezeigt. Diese Änderungen, die als grobe Bearbeitungen bezeichnet werden, beinhalten das falsche Schreibweise Ihrer XAML oder das Verknüpfen eines Steuer Elements mit einem Ereignishandler, der nicht vorhanden ist. Auch bei einer unhöflichen Bearbeitung können Sie den erneuten Ladevorgang fortsetzen, ohne die APP neu zu starten. nehmen Sie an anderer Stelle in der XAML-Datei eine andere Änderung vor, und Die ungrobe Bearbeitung wird nicht erneut geladen, aber Ihre anderen Änderungen werden weiterhin angewendet.

## <a name="known-limitations"></a>Bekannte Einschränkungen

- Sie können während einer XAML-Sitzung zum aktiven erneuten Laden keine Dateien oder nuget-Pakete hinzufügen, entfernen oder umbenennen. Wenn Sie eine Datei oder ein nuget-Paket hinzufügen oder entfernen, erstellen Sie Ihre APP erneut, und stellen Sie Sie erneut bereit.
- Legen Sie den Linker auf **keine Verknüpfung** fest, um eine optimale Leistung zu erzielen. Die Einstellung **Link SDK only** funktioniert meistens, kann jedoch in bestimmten Fällen fehlschlagen.
- Das Debuggen auf einem physischen iPhone erfordert, dass der Interpreter XAML-Hot-Neuladen verwendet. Fügen Sie **--Interpreter** zum Feld **zusätzliche mberührungs-Argumente** in ihren IOS-Buildeinstellungen hinzu, um XAML Hot Neuladen zu verwenden.
- Alle Verweise, `x:Name` die durch Zuweisen eines Steuer Elements zu einem anderen Feld oder einer Eigenschaft erstellt werden
- Das Aktualisieren der visuellen Hierarchie Ihrer Shell-Anwendung in **appshell. XAML** kann Probleme beim Verwalten des Zustands der Anwendung verursachen. Erstellen Sie die APP neu, damit Sie erneut geladen wird.
- Beim erneuten Laden von XAML C# kann Code nicht neu geladen werden, einschließlich Ereignis Handlern, benutzerdefinierte Steuerelemente, Seitencode Behind und zusätzliche Klassen.

## <a name="migrate-from-the-private-preview"></a>Migrieren von der privaten Vorschau

Wenn Sie Teil der privaten Vorschau sind, wird die XAML-Erweiterung zum erneuten Laden automatisch aktualisiert, wenn Visual Studio aktualisiert wird. Wenn Sie Visual Studio nicht aktualisieren möchten, können Sie weiterhin die aktuelle Version von XAML Hot Neuladen verwenden. Sie erhalten jedoch keine weiteren Updates über den Feed der privaten Vorschau Erweiterung.

## <a name="troubleshooting"></a>Problembehandlung

- Wenn XAML-Hot-Neuladen nicht initialisiert werden kann:
  - Aktualisieren Sie die xamarin. Forms-Version.
  - Stellen Sie sicher, dass Sie über die neueste Version der IDE verfügen.
  - Legen Sie die Einstellungen für den Android-oder IOS-Linker auf **keine Verknüpfung** in den Buildeinstellungen des Projekts fest.
- Wenn beim Speichern der XAML-Datei nichts passiert, stellen Sie sicher, dass das heiße laden in der IDE aktiviert ist.
- Wenn Sie auf einem physischen iPhone Debuggen und Ihre APP nicht mehr reagiert, überprüfen Sie, ob der Interpreter aktiviert ist. Um dies zu aktivieren, fügen Sie **--Interpreter** in den IOS-Buildeinstellungen zum Feld **zusätzliches mberührungs-Argument** hinzu.

Um einen Fehler zu melden, verwenden Sie das Feedback Tool in der **Hilfe** > Senden von Feedback > **melden Sie ein Problem** Menü unter Windows, **und > ** melden **Sie** **ein problemmenü** auf einem Mac.
