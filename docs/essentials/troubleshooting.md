---
title: 'Xamarin.Essentials: Problembehandlung'
description: In diesem Artikel wird beschrieben, wie Sie auftretende Fehler beim Entwickeln mit der Xamarin.Essentials-Bibliothek beheben.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: a9546923c9a17aade58d830ea2504406ad09d7d7
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870078"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Problembehandlung

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Fehler: Bei Xamarin.Android.Support.Compat wurde ein Versionskonflikt festgestellt.

Der folgende Fehler kann beim Aktualisieren von NuGet-Paketen (oder beim Hinzufügen eines neuen Pakets) mit einem Xamarin.Forms-Projekt auftreten, das Xamarin.Essentials verwendet:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 1.1.0 -> Xamarin.Android.Support.CustomTabs 28.0.0.1 -> Xamarin.Android.Support.Compat (= 28.0.0.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Das Problem liegt bei den nicht übereinstimmenden Abhängigkeiten der zwei NuGet-Pakete. Durch das Hinzufügen einer bestimmten Version der Abhängigkeit (in diesem Fall **Xamarin.Android.Support.Compat**), die beides unterstützt, kann das Problem behoben werden.

Fügen Sie dazu das NuGet-Paket, welches die Quelle des Konflikts darstellt, manuell hinzu, und verwenden Sie die **Versionsliste**, um eine bestimmte Version auszuwählen. Aktuell behebt die Version 28.0.0.1 der NuGet-Pakete Xamarin.Android.Support.Compat und Xamarin.Android.Support.Core.Util diesen Fehler.

Weitere Informationen finden Sie [in diesem Blogbeitrag](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) und in einem Video zum Lösen des Problems.

Sollten bei Ihnen Probleme oder Fehler auftreten, melden Sie dies im [GitHub-Repository zu Xamarin.Essentials](https://github.com/xamarin/Essentials).
