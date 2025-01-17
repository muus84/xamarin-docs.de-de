---
title: watchos-Menü Steuerelement (Force Touch) in xamarin
description: In diesem Dokument wird beschrieben, wie Sie die watchos Force-touchbewegung in xamarin verwenden. Es wird erläutert, wie auf eine Force-Fingereingabe reagiert wird, wie ein Menü hinzugefügt und die Menü Elemente geändert werden.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: f848433a5a668e247142aa4f47374c2c6531b55d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032695"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>watchos-Menü Steuerelement (Force Touch) in xamarin

Das Watch-Kit bietet eine Force Touch Geste, mit der ein Menü ausgelöst wird, wenn es auf einem Bildschirm für die Überwachung

![](menu-images/menu.png "Apple Watch showing a menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Reagieren auf Force Touch

Wenn ein `Menu` für einen Schnittstellen Controller implementiert wurde, wird das Menü angezeigt, wenn ein Benutzer eine Force Touch ausführt. Wenn kein Menü implementiert wurde, wird der Bildschirm kurz animiert, sodass keine weitere Aktion durchgeführt wird.

Force-Berührungen werden keinem bestimmten Element auf dem Bildschirm zugeordnet. an einen Schnittstellen Controller kann nur ein Menü angefügt werden, und es wird unabhängig davon angezeigt, wo der Force Touch auf dem Bildschirm angezeigt wird.

Zwischen einer und vier Menü Optionen können angezeigt werden.

## <a name="adding-a-menu"></a>Hinzufügen eines Menüs

Einer `InterfaceController` auf dem Storyboard muss zur Entwurfszeit ein `Menu` hinzugefügt werden. Wenn ein Menü Steuerelement auf einen Schnittstellen Controller gezogen wird, gibt es keinen visuellen Hinweis auf die Storyboard-Vorschau. das **Menü** wird jedoch im **Dokument** Gliederungs Fenster angezeigt:

![](menu-images/menu-action.png "Editing a menu at design time")

Dem Menü Steuerelement können bis zu vier Menü Elemente hinzugefügt werden. Sie können im **eigenschaftenpad** konfiguriert werden. Die folgenden Attribute können festgelegt werden:

- Titel und
- Benutzerdefiniertes Image oder
- Ein System Abbild: Accept, Add, Block, ablehnen, Info, Maybe, more, stumm, Pause, Play, Repeat, Resume, Share, shuffle, Speaker, Papierkorb.

Erstellen Sie einen `Action`, indem Sie im **eigenschaftenpad** den Abschnitt **Ereignisse** auswählen und den Namen für die Aktionsmethode eingeben. Im Code wird eine partielle Methode erstellt, die wie folgt in der Schnittstellen Controller Klasse implementiert werden kann:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Benutzerdefinierte Images

Ähnlich wie Registerkarten Bilder in ios erfordern Menü Element Bilder ein undurchsichtiges Muster mit einem Alphakanal, mit dem der Hintergrund durch angezeigt werden kann.

Für eine optimale Leistung sollten Sie die für das Menü verwendeten Images dem App-Projekt überwachen (nicht das App-Erweiterungsprojekt) hinzufügen.

## <a name="changing-the-menu-items"></a>Ändern der Menü Elemente

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Hinzufügen zur Laufzeit

Sie können nicht bewirken, dass ein `Menu` einem Schnittstellen Controller zur Laufzeit hinzugefügt wird, obwohl die Auflistung von `MenuItem`s Programm gesteuert geändert werden *kann* .
Verwenden Sie die `AddMenuItem`-Methode wie im folgenden gezeigt:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Die xamarin. IOS Watch-Kit-API erfordert zurzeit eine `selector` für die `AdMenuItem`-Methode, die wie folgt deklariert werden sollte:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Entfernen zur Laufzeit

Die `ClearAllMenuItems`-Methode kann aufgerufen werden, um alle *Programm gesteuert hinzugefügten* Menü Elemente zu entfernen.

Im Storyboard konfigurierte Menü Elemente können nicht gelöscht werden.

## <a name="related-links"></a>Verwandte Links

- [Watchkitcatalog (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Menü Dokumentation von Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
