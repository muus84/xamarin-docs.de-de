---
title: Automatische Größenanpassung der Zeilenhöhe in xamarin. IOS
description: In diesem Dokument wird beschrieben, wie Sie xamarin. IOS-apps Tabellen Ansichts Zeilen hinzufügen, deren Höhen sich je nach Inhalt unterscheiden. Er erläutert das Zellen Layout im IOS-Designer und ermöglicht die automatische Größenänderung.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4129370ecb465340a893e0a7f16703a08cc1db72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021933"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Automatische Größenanpassung der Zeilenhöhe in xamarin. IOS

Ab IOS 8 hat Apple die Möglichkeit hinzugefügt, eine Tabellenansicht (`UITableView`) zu erstellen, mit der die Höhe einer bestimmten Zeile basierend auf der Größe des Inhalts automatisch vergrößert und verkleinert werden kann, indem Automatisches Layout, Größenklassen und Einschränkungen verwendet werden.

IOS 11 hat die Möglichkeit zur automatischen Erweiterung von Zeilen hinzugefügt. Kopfzeilen, Fußzeilen und Zellen können nun basierend auf Ihrem Inhalt automatisch verkleinert werden. Wenn die Tabelle jedoch im IOS-Designer erstellt wird, Interface Builder, oder wenn Sie eine fixierte Zeilenhöhe aufweist, müssen Sie die Zellen mit selbst Größenänderung manuell aktivieren, wie in diesem Handbuch beschrieben.

## <a name="cell-layout-in-the-ios-designer"></a>Zellen Layout im IOS-Designer

Öffnen Sie das Storyboard für die Tabellen Sicht, für die die automatische Größenänderung der Zeile im IOS-Designer festgelegt werden soll, wählen Sie den *Prototyp* der Zelle aus, und entwerfen Sie das Layout der Zelle. Beispiel:

[![](autosizing-row-height-images/table01.png "The Cell's Prototype design")](autosizing-row-height-images/table01.png#lightbox)

Fügen Sie für jedes Element im Prototyp Einschränkungen hinzu, um die Elemente an der richtigen Position zu halten, da die Größe der Tabellenansicht für die Drehung oder für verschiedene Bildschirmgrößen von IOS-Geräten geändert wird. Wenn Sie z. b. die `Title` an den oberen, linken und rechten Rand der *Inhaltsansicht*der Zelle anhepten:

[![](autosizing-row-height-images/table02.png "Pinning the Title to the top, left and right of the Cells Content View")](autosizing-row-height-images/table02.png#lightbox)

Im Fall der Beispiel Tabelle ist die kleine `Label` (unter dem `Title`) das Feld, das verkleinert und vergrößert werden kann, um die Zeilenhöhe zu vergrößern oder zu verkleinern. Fügen Sie zum Erreichen dieses Effekts die folgenden Einschränkungen hinzu, um den linken, rechten, oberen und unteren Rand der Bezeichnung anzuheften:

[![](autosizing-row-height-images/table03.png "These constraints to pin the left, right, top and bottom of the label")](autosizing-row-height-images/table03.png#lightbox)

Nachdem wir nun die Elemente in der Zelle vollständig eingeschränkt haben, müssen wir verdeutlichen, welches Element gestreckt werden soll. Legen Sie zu diesem Zweck im **Layoutbereich** des Eigenschaftenpad die Priorität der **Inhalts-hugging-Priorität** und der **Inhalts Komprimierungs Priorität** nach Bedarf fest:

[![](autosizing-row-height-images/table03a.png "The Layout section of the Properties Pad")](autosizing-row-height-images/table03a.png#lightbox)

Legen Sie das Element, das Sie erweitern möchten, auf einen **niedrigeren** Wert für die hugging-Priorität und einen **niedrigeren** Prioritätswert für die Komprimierung fest.

Als nächstes müssen wir den Zellen Prototyp auswählen und ihm einen eindeutigen **Bezeichner**zuordnen:

[![](autosizing-row-height-images/table04.png "Giving the Cell Prototype a unique Identifier")](autosizing-row-height-images/table04.png#lightbox)

Im vorliegenden Beispiel `GrowCell`. Dieser Wert wird später verwendet, wenn wir die Tabelle auffüllen.

> [!IMPORTANT]
> Wenn die Tabelle mehr als einen Zellentyp (**Prototype**) enthält, müssen Sie sicherstellen, dass jeder Typ über einen eigenen eindeutigen `Identifier` verfügt, damit die automatische Zeilen Änderung funktioniert.

Weisen Sie für jedes Element des Zellen Prototyps einen **Namen** zu, um ihn C# für den Code verfügbar zu machen. Beispiel:

[![](autosizing-row-height-images/table05.png "Assign a Name to expose it to C# code")](autosizing-row-height-images/table05.png#lightbox)

Fügen Sie als nächstes eine benutzerdefinierte Klasse für die `UITableViewController`, die `UITableView` und die `UITableCell` (Prototyp) hinzu. Beispiel: 

[![](autosizing-row-height-images/table06.png "Adding a custom class for the UITableViewController, the UITableView and the UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Um schließlich sicherzustellen, dass alle erwarteten Inhalte in unserer Bezeichnung angezeigt werden, legen Sie die Eigenschaft **Lines** auf `0`fest:

[![](autosizing-row-height-images/table06.png "The Lines property set to 0")](autosizing-row-height-images/table06a.png#lightbox)

Wenn Sie die Benutzeroberfläche definiert haben, fügen Sie den Code hinzu, um die Größenänderung der automatischen Zeilenhöhe zu aktivieren.

## <a name="enabling-auto-resizing-height"></a>Aktivieren der Höhe der automatischen Größenänderung

In der Datenquelle (`UITableViewDatasource`) oder Quelle (`UITableViewSource`) der Tabellenansicht müssen wir die im Designer definierte `Identifier` verwenden, wenn wir eine Zelle aus der Warteschlange entfernen. Beispiel:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

Standardmäßig wird die Tabellenansicht für die automatische Größenänderung der Zeilenhöhe festgelegt. Um dies zu gewährleisten, sollte die `RowHeight`-Eigenschaft auf `UITableView.AutomaticDimension`festgelegt werden. Wir müssen auch die `EstimatedRowHeight`-Eigenschaft in unserer `UITableViewController`festlegen. Beispiel:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

Diese Schätzung muss nicht exakt sein, sondern nur eine grobe Schätzung der durchschnittlichen Höhe der einzelnen Zeilen in der Tabellenansicht.

Wenn dieser Code vorhanden ist, wird beim Ausführen der APP jede Zeile verkleinert und basierend auf der Höhe der letzten Bezeichnung im Zellen Prototyp vergrößert. Beispiel:

[![](autosizing-row-height-images/table07.png "A sample table run")](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>Verwandte Links

- [Growrowtable (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
