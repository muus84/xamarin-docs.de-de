---
title: Tabellen Ansichten in xamarin. Mac
description: In diesem Artikel wird das Arbeiten mit Tabellen Sichten in einer xamarin. Mac-Anwendung behandelt. Es beschreibt das Erstellen von Tabellen Sichten in Xcode und Interface Builder und die Interaktion mit Ihnen im Code.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 36bed05c1e60004125406c3ed2df66fcfe2be10b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008327"
---
# <a name="table-views-in-xamarinmac"></a>Tabellen Ansichten in xamarin. Mac

_In diesem Artikel wird das Arbeiten mit Tabellen Sichten in einer xamarin. Mac-Anwendung behandelt. Es beschreibt das Erstellen von Tabellen Sichten in Xcode und Interface Builder und die Interaktion mit Ihnen im Code._

Wenn Sie mit C# und .net in einer xamarin. Mac-Anwendung arbeiten, haben Sie Zugriff auf die gleichen Tabellen Sichten, die von einem Entwickler in *Ziel-C* und *Xcode* verwendet werden. Da xamarin. Mac direkt in Xcode integriert ist, können Sie die _Interface Builder_ von Xcode verwenden, um Tabellen Sichten zu erstellen und zu verwalten (oder Sie C# optional direkt im Code zu erstellen).

In einer Tabellen Sicht werden Daten in einem Tabellenformat angezeigt, das mindestens eine Spalte mit Informationen in mehreren Zeilen enthält. Basierend auf dem Typ der zu erstellenden Tabellenansicht kann der Benutzer nach Spalte sortieren, Spalten neu organisieren, Spalten hinzufügen, Spalten entfernen oder die in der Tabelle enthaltenen Daten bearbeiten.

[![](table-view-images/intro01.png "An example table")](table-view-images/intro01.png#lightbox)

In diesem Artikel werden die Grundlagen der Arbeit mit Tabellen Sichten in einer xamarin. Mac-Anwendung behandelt. Es wird dringend empfohlen, dass Sie zunächst den Artikel [Hello, Mac](~/mac/get-started/hello-mac.md) , insbesondere die [Einführung in Xcode und die Abschnitte zu Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) und Outlets und [Aktionen](~/mac/get-started/hello-mac.md#outlets-and-actions) , verwenden, da er wichtige Konzepte und Techniken behandelt, die wir in verwenden werden. Dieser Artikel.

Sie können sich auch den Abschnitt verfügbar machen von [ C# Klassen/Methoden zu "Ziel-c](~/mac/internals/how-it-works.md) " im Dokument " [xamarin. Mac](~/mac/internals/how-it-works.md) " ansehen. darin werden die`Register`und`Export`Befehle erläutert, die zum Verknüpfen der C# Klassen mit "Ziel-c" verwendet werden. Objekte und UI-Elemente.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Einführung in Tabellen Sichten

In einer Tabellen Sicht werden Daten in einem Tabellenformat angezeigt, das mindestens eine Spalte mit Informationen in mehreren Zeilen enthält. Tabellen Sichten werden in Bild Lauf Ansichten (`NSScrollView`) angezeigt, und ab macOS 10,7 können Sie beliebige `NSView` anstelle von Zellen (`NSCell`) verwenden, um sowohl Zeilen als auch Spalten anzuzeigen. Das heißt, Sie können zwar weiterhin `NSCell` verwenden, Sie werden jedoch in der Regel `NSTableCellView` Unterklassen und benutzerdefinierte Zeilen und Spalten erstellen.

In einer Tabellen Sicht werden Ihre eigenen Daten nicht gespeichert. stattdessen wird eine Datenquelle (`NSTableViewDataSource`) verwendet, um sowohl die erforderlichen Zeilen als auch die Spalten bereitzustellen.

Das Verhalten einer Tabellenansicht kann angepasst werden, indem eine Unterklasse des Tabellen Ansichts Delegaten (`NSTableViewDelegate`) bereitgestellt wird, um die Tabellen Spalten Verwaltung, den Typ für die Auswahl von Funktionen, die Zeilenauswahl und-Bearbeitung, die benutzerdefinierte Nachverfolgung und benutzerdefinierte Ansichten für einzelne Spalten

Beim Erstellen von Tabellen Sichten schlägt Apple folgendes vor:

- Ermöglicht dem Benutzer das Sortieren der Tabelle durch Klicken auf Spaltenüberschriften.
- Erstellen Sie Spaltenüberschriften, bei denen es sich um Nomen oder kurze Substantiv-Ausdrücke handelt, die die in dieser Spalte angezeigten Daten beschreiben.

Weitere Informationen finden Sie im Abschnitt " [Inhalts Ansichten](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) " in den [Richtlinien für die Benutzeroberfläche von Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Erstellen und warten von Tabellen Sichten in Xcode

Wenn Sie eine neue xamarin. Mac-Cocoa-Anwendung erstellen, erhalten Sie standardmäßig ein Standardmäßiges leeres Standardfenster. Dieses Fenster wird in einer `.storyboard` Datei definiert, die automatisch im Projekt enthalten ist. Um den Windows-Entwurf zu bearbeiten, doppelklicken Sie im **Projektmappen-Explorer**auf die `Main.storyboard` Datei:

[![](table-view-images/edit01.png "Selecting the main storyboard")](table-view-images/edit01.png#lightbox)

Dadurch wird das Fensterdesign in der Interface Builder von Xcode geöffnet:

[![](table-view-images/edit02.png "Editing the UI in Xcode")](table-view-images/edit02.png#lightbox)

Geben Sie `table` in das Suchfeld des **Bibliotheks Inspektors** ein, um die Suche nach den Tabellen Sicht-Steuerelementen zu vereinfachen:

[![](table-view-images/edit03.png "Selecting a Table View from the Library")](table-view-images/edit03.png#lightbox)

Ziehen Sie eine Tabellenansicht auf den Ansichts Controller im Schnittstellen-Editor, füllen Sie den Inhalts Bereich des Ansichts Controllers aus, und legen Sie ihn an der **Stelle**ab, an der er verkleinert wird und mit dem Fenster im Einschränkungs- **Editor**wächst:

[![](table-view-images/edit04.png "Editing constraints")](table-view-images/edit04.png#lightbox)

Wählen Sie die Tabellenansicht in der **Schnittstellen Hierarchie** aus, und die folgenden Eigenschaften sind im **Attribut Inspektor**verfügbar:

[![](table-view-images/edit05.png "The Attribute Inspector")](table-view-images/edit05.png#lightbox)

- **Inhalts Modus** : ermöglicht die Verwendung von Sichten (`NSView`) oder Zellen (`NSCell`), um die Daten in den Zeilen und Spalten anzuzeigen. Ab macOS 10,7 sollten Sie Ansichten verwenden.
- Gleit Komma **Gruppen Zeilen** : Wenn `true`, zeichnet die Tabellenansicht gruppierte Zellen so, als wären Sie unverankert.
- **Columns** : definiert die Anzahl der angezeigten Spalten.
- **Headers** : Wenn `true`, enthalten die Spaltenheader.
- **Neuanordnen** : Wenn `true`, kann der Benutzer die Reihenfolge der Spalten in der Tabelle verschieben.
- Größenänderung: Wenn `true`, kann der Benutzer **Spaltenüberschriften** ziehen, um die Größe der Spalten zu ändern.
- **Spaltengröße** : steuert die automatische Größenanpassung von Spalten durch die Tabelle.
- **Hervor** Hebung: steuert den Typ der Hervorhebung, die die Tabelle verwendet, wenn eine Zelle ausgewählt wird.
- **Alternative Zeilen** : Wenn `true`, hat eine beliebige andere Zeile eine andere Hintergrundfarbe.
- **Horizontales Raster** : wählt den Typ des Rahmens aus, der zwischen Zellen horizontal gezeichnet wird.
- **Vertikales Raster** : wählt den Typ des Rahmens aus, der zwischen Zellen vertikal gezeichnet wird.
- **Raster Farbe** : legt die Farbe für den Zell Rahmen fest.
- **Background** : legt die Hintergrundfarbe der Zelle fest.
- **Auswahl** : Hiermit können Sie steuern, wie der Benutzer Zellen in der Tabelle auswählen kann:
  - **Mehrfach** : Wenn `true`, kann der Benutzer mehrere Zeilen und Spalten auswählen.
  - **Spalte** : Wenn `true`, kann der Benutzer Spalten auswählen.
  - **Typ SELECT** -wenn `true`, kann der Benutzer ein Zeichen eingeben, um eine Zeile auszuwählen.
  - **Leer** : Wenn `true`, ist es nicht erforderlich, dass der Benutzer eine Zeile oder Spalte auswählt, da die Tabelle überhaupt keine Auswahl zulässt.
- **Autosave** : der Name, unter dem das Tabellenformat automatisch gespeichert wird.
- **Spalten Informationen** : Wenn `true`, werden die Reihenfolge und Breite der Spalten automatisch gespeichert.
- **Zeilenumbrüche** : Wählen Sie aus, wie die Zelle Zeilenumbrüche behandelt.
- Kürzt die **letzte sichtbare Zeile** : Wenn `true`, wird die Zelle abgeschnitten, wenn die Daten nicht innerhalb der Grenzen passen.

> [!IMPORTANT]
> Wenn Sie keine ältere xamarin. Mac-Anwendung verwalten, sollten Sie `NSView` basierten Tabellen Sichten über `NSCell` basierten Tabellen Sichten verwenden. `NSCell` gilt als Legacy und wird möglicherweise nicht weiter unterstützt.

Wählen Sie eine Tabellenspalte in der **Schnittstellen Hierarchie** aus, und die folgenden Eigenschaften sind im **Attribut Inspektor**verfügbar:

[![](table-view-images/edit06.png "The Attribute Inspector")](table-view-images/edit06.png#lightbox)

- **Title** : legt den Titel der Spalte fest.
- **Ausrichtung** : Legen Sie die Ausrichtung des Texts innerhalb der Zellen fest.
- **Titel Schriftart** : wählt die Schriftart für den Header Text der Zelle aus.
- **Sortierschlüssel** : der Schlüssel, der zum Sortieren der Daten in der Spalte verwendet wird. Lassen Sie das Feld leer, wenn der Benutzer diese Spalte nicht sortieren kann.
- **Selector** : ist die **Aktion** , die zum Ausführen der Sortierung verwendet wird. Lassen Sie das Feld leer, wenn der Benutzer diese Spalte nicht sortieren kann.
- **Order** : gibt die Sortierreihenfolge für die Spaltendaten an.
- **Ändern der Größe** : wählt den Typ der Größe der Größe für die Spalte aus.
- **Bearbeitbar** : Wenn `true`, kann der Benutzer Zellen in einer Zellen basierten Tabelle bearbeiten.
- **Hidden** : Wenn `true`, wird die Spalte ausgeblendet.

Sie können auch die Größe der Spalte ändern, indem Sie den Zieh Punkt (vertikal auf der rechten Seite der Spalte) nach links oder rechts ziehen.

Wählen Sie in der Tabellenansicht die einzelnen Spalten aus, und geben Sie der ersten Spalte einen **Titel** `Product` und der zweite `Details`.

Wählen Sie in der **Schnittstellen Hierarchie** eine Tabellenzellen Ansicht (`NSTableViewCell`) aus, und die folgenden Eigenschaften sind im **Attribut Inspektor**verfügbar:

[![](table-view-images/edit07.png "The Attribute Inspector")](table-view-images/edit07.png#lightbox)

Dabei handelt es sich um alle Eigenschaften einer Standardansicht. Sie haben auch die Möglichkeit, die Zeilen für diese Spalte hier zu ändern.

Wählen Sie eine Tabellen Ansichts Zelle (standardmäßig eine `NSTextField`) in der **Schnittstellen Hierarchie** aus, und die folgenden Eigenschaften sind im **Attribut Inspektor**verfügbar:

[![](table-view-images/edit08.png "The Attribute Inspector")](table-view-images/edit08.png#lightbox)

Sie verfügen über alle Eigenschaften eines Standard Textfelds, das hier festgelegt werden soll. Standardmäßig wird ein Standard Textfeld verwendet, um Daten für eine Zelle in einer Spalte anzuzeigen.

Wählen Sie in der **Schnittstellen Hierarchie** eine Tabellenzellen Ansicht (`NSTableFieldCell`) aus, und die folgenden Eigenschaften sind im **Attribut Inspektor**verfügbar:

[![](table-view-images/edit09.png "The Attribute Inspector")](table-view-images/edit09.png#lightbox)

Die wichtigsten Einstellungen finden Sie hier:

- **Layout** : Wählen Sie aus, wie Zellen in dieser Spalte angelegt werden.
- **Verwendet den Einzeilenmodus** -wenn `true`, ist die Zelle auf eine einzelne Zeile beschränkt.
- **Breite des ersten Lauf Zeit Layouts** : Wenn `true`, wird die Zelle für die Zelle (entweder manuell oder automatisch) bevorzugt, wenn Sie beim ersten Ausführen der Anwendung angezeigt wird.
- **Action** : steuert, wann die Bearbeitungs **Aktion** für die Zelle gesendet wird.
- **Behavior** : definiert, ob eine Zelle auswählbar ist oder bearbeitet werden kann.
- **Rich-Text** : Wenn `true`, kann die Zelle formatierten und formatierten Text anzeigen.
- **Rückgängig** : Wenn `true`, übernimmt die Zelle die Verantwortung für das rückgängig-Verhalten.

Wählen Sie die Tabellenzellen Ansicht (`NSTableFieldCell`) unten in einer Tabellenspalte in der **Schnittstellen Hierarchie**aus:

[![](table-view-images/edit10.png "Selecting the Table Cell View")](table-view-images/edit10.png#lightbox)

Dies ermöglicht es Ihnen, die Tabellenzellen Ansicht zu bearbeiten, die als Basis _Muster_ für alle Zellen verwendet wird, die für die jeweilige Spalte erstellt werden.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Hinzufügen von Aktionen und Outlets

Genau wie jedes andere Cocoa UI-Steuerelement müssen wir unsere Tabellenansicht und die Spalten und Zellen für C# Code mithilfe von **Aktionen** und **Outlets** (basierend auf der erforderlichen Funktionalität) verfügbar machen.

Der Prozess ist für jedes Tabellen Ansichts Element identisch, das wir verfügbar machen möchten:

1. Wechseln Sie zum **Assistenten-Editor** , und stellen Sie sicher, dass die Datei `ViewController.h` ausgewählt ist: 

    [![](table-view-images/edit11.png "The Assistant Editor")](table-view-images/edit11.png#lightbox)
2. Wählen Sie die Tabellenansicht in der **Schnittstellen Hierarchie**aus, und ziehen Sie Sie in die `ViewController.h` Datei.
3. Erstellen Sie ein **Outlet** für die Tabellenansicht mit dem Namen `ProductTable`: 

    [![](table-view-images/edit13.png "Configuring an Outlet")](table-view-images/edit13.png#lightbox)
4. Erstellen Sie **Outlets** für die Tabellen Spalten, die auch `ProductColumn` und `DetailsColumn`genannt werden: 

    [![](table-view-images/edit14.png "Configuring an Outlet")](table-view-images/edit14.png#lightbox)
5. Speichern Sie die Änderungen, und kehren Sie zu Visual Studio für Mac zurück, um mit Xcode zu synchronisieren.

Als nächstes schreiben wir den Code zum Anzeigen einiger Daten für die Tabelle, wenn die Anwendung ausgeführt wird.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Auffüllen der Tabellenansicht

Wenn unsere Tabellenansicht in Interface Builder entworfen und über ein **Outlet**verfügbar gemacht wird, müssen Sie als nächstes C# den Code erstellen, um ihn aufzufüllen.

Zunächst erstellen wir eine neue `Product`-Klasse, die die Informationen für die einzelnen Zeilen enthält. Klicken Sie im **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie > **neue Datei** **Hinzufügen** ... aus. Wählen Sie **Allgemein** > **leere Klasse**aus, geben Sie `Product` als **Namen** ein, und klicken Sie auf die Schaltfläche **neu** :

[![](table-view-images/populate01.png "Creating an empty class")](table-view-images/populate01.png#lightbox)

Erstellen Sie die `Product.cs` Datei wie folgt:

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
    public string Title { get; set;} = "";
    public string Description { get; set;} = "";
    #endregion

    #region Constructors
    public Product ()
    {
    }

    public Product (string title, string description)
    {
      this.Title = title;
      this.Description = description;
    }
    #endregion
  }
}

```

Als nächstes müssen wir eine Unterklasse von `NSTableDataSource` erstellen, um die Daten für die Tabelle anzugeben, wie Sie angefordert wird. Klicken Sie im **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie > **neue Datei** **Hinzufügen** ... aus. Wählen Sie **Allgemein** > **leere Klasse**aus, geben Sie `ProductTableDataSource` als **Namen** ein, und klicken Sie auf die Schaltfläche **neu** .

Bearbeiten Sie die Datei `ProductTableDataSource.cs`, und führen Sie Sie wie folgt aus:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDataSource : NSTableViewDataSource
  {
    #region Public Variables
    public List<Product> Products = new List<Product>();
    #endregion

    #region Constructors
    public ProductTableDataSource ()
    {
    }
    #endregion

    #region Override Methods
    public override nint GetRowCount (NSTableView tableView)
    {
      return Products.Count;
    }
    #endregion
  }
}

```

Diese Klasse verfügt über Speicher für die Elemente der Tabellen Sicht und überschreibt die `GetRowCount`, um die Anzahl der Zeilen in der Tabelle zurückzugeben.

Schließlich müssen wir eine Unterklasse von `NSTableDelegate` erstellen, um das Verhalten für unsere Tabelle bereitzustellen. Klicken Sie im **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie > **neue Datei** **Hinzufügen** ... aus. Wählen Sie **Allgemein** > **leere Klasse**aus, geben Sie `ProductTableDelegate` als **Namen** ein, und klicken Sie auf die Schaltfläche **neu** .

Bearbeiten Sie die Datei `ProductTableDelegate.cs`, und führen Sie Sie wie folgt aus:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDelegate: NSTableViewDelegate
  {
    #region Constants 
    private const string CellIdentifier = "ProdCell";
    #endregion

    #region Private Variables
    private ProductTableDataSource DataSource;
    #endregion

    #region Constructors
    public ProductTableDelegate (ProductTableDataSource datasource)
    {
      this.DataSource = datasource;
    }
    #endregion

    #region Override Methods
    public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
    {
      // This pattern allows you reuse existing views when they are no-longer in use.
      // If the returned view is null, you instance up a new view
      // If a non-null view is returned, you modify it enough to reflect the new data
      NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
      if (view == null) {
        view = new NSTextField ();
        view.Identifier = CellIdentifier;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = false;
      }

      // Setup view based on the column selected
      switch (tableColumn.Title) {
      case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
      case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
      }

      return view;
    }
    #endregion
  }
}
```

Wenn wir eine Instanz des `ProductTableDelegate`erstellen, übergeben wir auch eine Instanz des `ProductTableDataSource`, die die Daten für die Tabelle bereitstellt. Die `GetViewForItem`-Methode ist dafür verantwortlich, eine Ansicht (Daten) zurückzugeben, um die Zelle für eine Spalte und eine Zeile mit dem Wert anzuzeigen. Wenn möglich, wird eine vorhandene Ansicht wieder verwendet, um die Zelle anzuzeigen, wenn keine neue Ansicht erstellt werden muss.

Um die Tabelle aufzufüllen, bearbeiten Sie die Datei `ViewController.cs`, und legen Sie die `AwakeFromNib`-Methode wie folgt an:

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  // Create the Product Table Data Source and populate it
  var DataSource = new ProductTableDataSource ();
  DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

  // Populate the Product Table
  ProductTable.DataSource = DataSource;
  ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Wenn die Anwendung ausgeführt wird, wird Folgendes angezeigt:

[![](table-view-images/populate02.png "A sample app run")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Sortieren nach Spalte

Lassen Sie den Benutzer das Sortieren der Daten in der Tabelle zu, indem Sie auf eine Spaltenüberschrift klicken. Doppelklicken Sie zunächst auf die `Main.storyboard` Datei, um Sie für die Bearbeitung in Interface Builder zu öffnen. Wählen Sie die Spalte `Product` aus, geben Sie `Title` für den **Sortierschlüssel**ein `compare:` für die **Auswahl** , und wählen Sie `Ascending` für die **Reihenfolge**aus:

[![](table-view-images/sort01.png "Setting the sort key")](table-view-images/sort01.png#lightbox)

Wählen Sie die Spalte `Details` aus, geben Sie `Description` für den **Sortierschlüssel**ein `compare:` für die **Auswahl** , und wählen Sie `Ascending` für die **Reihenfolge**aus:

[![](table-view-images/sort02.png "Setting the sort key")](table-view-images/sort02.png#lightbox)

Speichern Sie die Änderungen, und kehren Sie zu Visual Studio für Mac zurück, um mit Xcode zu synchronisieren.

Nun bearbeiten wir die `ProductTableDataSource.cs` Datei und fügen die folgenden Methoden hinzu:

```csharp
public void Sort(string key, bool ascending) {

  // Take action based on key
  switch (key) {
  case "Title":
    if (ascending) {
      Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
    } else {
      Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
    }
    break;
  case "Description":
    if (ascending) {
      Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
    } else {
      Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
    }
    break;
  }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
  // Sort the data
  if (oldDescriptors.Length > 0) {
    // Update sort
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
  } else {
    // Grab current descriptors and update sort
    NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
    Sort (tbSort[0].Key, tbSort[0].Ascending); 
  }
      
  // Refresh table
  tableView.ReloadData ();
}
```

Mit der `Sort`-Methode können wir die Daten in der Datenquelle auf der Grundlage eines bestimmten `Product` Klassen Felds in aufsteigender oder absteigender Reihenfolge sortieren. Die überschriebene `SortDescriptorsChanged` Methode wird jedes Mal aufgerufen, wenn die Verwendung auf eine Spaltenüberschrift klickt. Der **Schlüssel** Wert, den wir in Interface Builder festgelegt haben, und die Sortierreihenfolge für diese Spalte werden an Sie übermittelt.

Wenn Sie die Anwendung ausführen und in die Spaltenüberschriften klicken, werden die Zeilen nach dieser Spalte sortiert:

[![](table-view-images/sort03.png "An example app run")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Zeilenauswahl

Wenn Sie dem Benutzer die Auswahl einer einzelnen Zeile gestatten möchten, doppelklicken Sie auf die `Main.storyboard` Datei, um Sie für die Bearbeitung in Interface Builder zu öffnen. Wählen Sie die Tabellenansicht in der **Schnittstellen Hierarchie** aus, und deaktivieren Sie das Kontrollkästchen **mehrfach** im **Attribut Inspektor**:

[![](table-view-images/select01.png "The Attribute Inspector")](table-view-images/select01.png#lightbox)

Speichern Sie die Änderungen, und kehren Sie zu Visual Studio für Mac zurück, um mit Xcode zu synchronisieren.

Bearbeiten Sie als nächstes die Datei `ProductTableDelegate.cs`, und fügen Sie die folgende Methode hinzu:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Dadurch kann der Benutzer eine beliebige einzelne Zeile in der Tabellenansicht auswählen. Geben Sie `false` für die `ShouldSelectRow` für jede Zeile zurück, die nicht für jede Zeile ausgewählt oder `false` werden soll, wenn der Benutzer nicht in der Lage sein soll, Zeilen auszuwählen.

Die Tabellenansicht (`NSTableView`) enthält die folgenden Methoden zum Arbeiten mit der Zeilenauswahl:

- deaktiviert die angegebene Zeile in der Tabelle `DeselectRow(nint)`.
- `SelectRow(nint,bool)`: wählt die angegebene Zeile aus. Übergeben Sie `false` für den zweiten Parameter, um jeweils nur eine Zeile auszuwählen.
- `SelectedRow`: gibt die in der Tabelle ausgewählte aktuelle Zeile zurück.
- `IsRowSelected(nint)`: gibt `true` zurück, wenn die angegebene Zeile ausgewählt ist.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Auswahl mehrerer Zeilen

Wenn Sie zulassen möchten, dass der Benutzer mehrere Zeilen auswählt, doppelklicken Sie auf die `Main.storyboard` Datei, um Sie für die Bearbeitung in Interface Builder zu öffnen. Wählen Sie die Tabellenansicht in der **Schnittstellen Hierarchie** aus, und aktivieren Sie das Kontrollkästchen **mehrfach** im **Attribut Inspektor**:

[![](table-view-images/select02.png "The Attribute Inspector")](table-view-images/select02.png#lightbox)

Speichern Sie die Änderungen, und kehren Sie zu Visual Studio für Mac zurück, um mit Xcode zu synchronisieren.

Bearbeiten Sie als nächstes die Datei `ProductTableDelegate.cs`, und fügen Sie die folgende Methode hinzu:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Dadurch kann der Benutzer eine beliebige einzelne Zeile in der Tabellenansicht auswählen. Geben Sie `false` für die `ShouldSelectRow` für jede Zeile zurück, die nicht für jede Zeile ausgewählt oder `false` werden soll, wenn der Benutzer nicht in der Lage sein soll, Zeilen auszuwählen.

Die Tabellenansicht (`NSTableView`) enthält die folgenden Methoden zum Arbeiten mit der Zeilenauswahl:

- `DeselectAll(NSObject)` alle Zeilen in der Tabelle deaktiviert. Verwenden Sie `this` für den ersten Parameter, um das Objekt zu senden, das die Auswahl durchgeführt hat. 
- deaktiviert die angegebene Zeile in der Tabelle `DeselectRow(nint)`.
- `SelectAll(NSobject)`: wählt alle Zeilen in der Tabelle aus. Verwenden Sie `this` für den ersten Parameter, um das Objekt zu senden, das die Auswahl durchgeführt hat.
- `SelectRow(nint,bool)`: wählt die angegebene Zeile aus. Übergeben Sie `false` für den zweiten Parameter `true`, um die Auswahl zu löschen, und wählen Sie nur eine einzige Zeile aus
- `SelectRows(NSIndexSet,bool)`: wählt den angegebenen Satz von Zeilen aus. Pass `false` für den zweiten Parameter löschen Sie die Auswahl, und wählen Sie nur eine dieser Zeilen aus, übergeben Sie `true`, um die Auswahl zu erweitern und diese Zeilen einzuschließen.
- `SelectedRow`: gibt die in der Tabelle ausgewählte aktuelle Zeile zurück.
- `SelectedRows`: gibt eine `NSIndexSet` zurück, die die Indizes der ausgewählten Zeilen enthält.
- `SelectedRowCount`: gibt die Anzahl der ausgewählten Zeilen zurück.
- `IsRowSelected(nint)`: gibt `true` zurück, wenn die angegebene Zeile ausgewählt ist.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Zum Auswählen der Zeile eingeben

Wenn Sie zulassen möchten, dass der Benutzer ein Zeichen mit ausgewählter Tabellenansicht eingibt, und dann die erste Zeile mit diesem Zeichen auswählen, doppelklicken Sie auf die `Main.storyboard` Datei, um Sie für die Bearbeitung in Interface Builder zu öffnen. Wählen Sie die Tabellenansicht in der **Schnittstellen Hierarchie** aus, und aktivieren Sie im **Attribut Inspektor**das Kontrollkästchen **Typ auswählen** :

[![](table-view-images/type01.png "Setting the selection type")](table-view-images/type01.png#lightbox)

Speichern Sie die Änderungen, und kehren Sie zu Visual Studio für Mac zurück, um mit Xcode zu synchronisieren.

Nun bearbeiten wir die `ProductTableDelegate.cs` Datei und fügen die folgende Methode hinzu:

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
  nint row = 0;
  foreach(Product product in DataSource.Products) {
    if (product.Title.Contains(searchString)) return row;

    // Increment row counter
    ++row;
  }

  // If not found select the first row
  return 0;
}
```

Die `GetNextTypeSelectMatch`-Methode nimmt die angegebene `searchString` an und gibt die Zeile des ersten `Product` zurück, bei dem diese Zeichenfolge in Ihr `Title`ist.

Wenn Sie die Anwendung ausführen und ein Zeichen eingeben, wird eine Zeile ausgewählt:

[![](table-view-images/type02.png "A sample app run")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Neuordnen von Spalten

Wenn Sie zulassen möchten, dass der Benutzer die Reihenfolge der Spalten in der Tabellenansicht zieht, doppelklicken Sie auf die Datei `Main.storyboard`, um Sie in Interface Builder zu bearbeiten. Wählen Sie die Tabellenansicht in der **Schnittstellen Hierarchie** aus, und aktivieren Sie das Kontrollkästchen **Neuanordnen** im **Attribut Inspektor**:

[![](table-view-images/reorder01.png "The Attribute Inspector")](table-view-images/reorder01.png#lightbox)

Wenn wir einen Wert für die Eigenschaft **Autosave** festlegen und das Feld **Spalten Informationen** aktivieren, werden alle Änderungen, die wir am Layout der Tabelle vornehmen, automatisch für uns gespeichert und beim nächsten Ausführen der Anwendung wieder hergestellt.

Speichern Sie die Änderungen, und kehren Sie zu Visual Studio für Mac zurück, um mit Xcode zu synchronisieren.

Nun bearbeiten wir die `ProductTableDelegate.cs` Datei und fügen die folgende Methode hinzu:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

Die `ShouldReorder`-Methode sollte `true` für jede Spalte zurückgeben, die in die `newColumnIndex`gezogen werden soll. andernfalls wird `false`zurückgegeben.

Wenn wir die Anwendung ausführen, können wir die Spaltenkopfzeilen verschieben, um die Spalten neu zu sortieren:

[![](table-view-images/reorder02.png "An example of the reordered columns")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Bearbeiten von Zellen

Wenn Sie zulassen möchten, dass der Benutzer die Werte für eine bestimmte Zelle bearbeitet, bearbeiten Sie die Datei `ProductTableDelegate.cs`, und ändern Sie die `GetViewForItem`-Methode wie folgt:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTextField ();
    view.Identifier = tableColumn.Title;
    view.BackgroundColor = NSColor.Clear;
    view.Bordered = false;
    view.Selectable = false;
    view.Editable = true;

    view.EditingEnded += (sender, e) => {
          
      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.Tag].Title = view.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.Tag].Description = view.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

Wenn nun die Anwendung ausgeführt wird, kann der Benutzer die Zellen in der Tabellenansicht bearbeiten:

[![](table-view-images/editing01.png "An example of editing a cell")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Verwenden von Bildern in Tabellen Sichten

Wenn Sie ein Bild als Teil der Zelle in einem `NSTableView` einschließen möchten, müssen Sie ändern, wie die Daten von der `NSTableViewDelegate's` `GetViewForItem`-Methode der Tabellen Sicht zurückgegeben werden, um anstelle der typischen `NSTextField` eine `NSTableCellView` zu verwenden. Beispiel:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();
    if (tableColumn.Title == "Product") {
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
    } else {
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
    }
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);
    view.Identifier = tableColumn.Title;
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    view.TextField.EditingEnded += (sender, e) => {

      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.TextField.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tags.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

Weitere Informationen finden Sie im Abschnitt [Verwenden von Images mit Tabellen Sichten](~/mac/app-fundamentals/image.md) in unserer Dokumentation zum [Arbeiten mit](~/mac/app-fundamentals/image.md) Images.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Hinzufügen einer Lösch Schaltfläche zu einer Zeile

Abhängig von den Anforderungen Ihrer APP kann es vorkommen, dass Sie eine Aktions Schaltfläche für jede Zeile in der Tabelle angeben müssen. Als Beispiel hierfür erweitern wir das oben erstellte Tabellen Ansichts Beispiel, um eine **Lösch** Schaltfläche für jede Zeile einzuschließen.

Bearbeiten Sie zunächst die `Main.storyboard` in der Interface Builder von Xcode, wählen Sie die Tabellenansicht aus, und erhöhen Sie die Anzahl der Spalten auf drei (3). Ändern Sie als nächstes den **Titel** der neuen Spalte in `Action`:

[![](table-view-images/delete01.png "Editing the column name")](table-view-images/delete01.png#lightbox)

Speichern Sie die Änderungen am Storyboard, und kehren Sie zu Visual Studio für Mac zurück, um die Änderungen zu synchronisieren.

Bearbeiten Sie als nächstes die Datei `ViewController.cs`, und fügen Sie die folgende öffentliche Methode hinzu:

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

Ändern Sie in derselben Datei die Erstellung des neuen Tabellen Ansichts Delegaten in der `ViewDidLoad`-Methode wie folgt:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Bearbeiten Sie nun die `ProductTableDelegate.cs` Datei so, dass Sie eine private Verbindung mit dem Ansichts Controller einschließt, und verwenden Sie den Controller als Parameter, wenn Sie eine neue Instanz des Delegaten erstellen:

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
  this.Controller = controller;
  this.DataSource = datasource;
}
#endregion
```

Fügen Sie dann der-Klasse die folgende neue private-Methode hinzu:

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
  // Add to view
  view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
  view.AddSubview (view.TextField);

  // Configure
  view.TextField.BackgroundColor = NSColor.Clear;
  view.TextField.Bordered = false;
  view.TextField.Selectable = false;
  view.TextField.Editable = true;

  // Wireup events
  view.TextField.EditingEnded += (sender, e) => {

    // Take action based on type
    switch (view.Identifier) {
    case "Product":
      DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
      break;
    case "Details":
      DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
      break;
    }
  };

  // Tag view
  view.TextField.Tag = row;
}
```

Dadurch werden alle Text Ansichts Konfigurationen verwendet, die zuvor in der `GetViewForItem`-Methode ausgeführt wurden, und Sie werden an einem einzelnen Aufruf baren Speicherort platziert (da die letzte Spalte der Tabelle keine Text Ansicht, sondern eine Schaltfläche enthält).

Bearbeiten Sie abschließend die `GetViewForItem`-Methode, und führen Sie Sie wie folgt aus:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();

    // Configure the view
    view.Identifier = tableColumn.Title;

    // Take action based on title
    switch (tableColumn.Title) {
    case "Product":
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Details":
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Action":
      // Create new button
      var button = new NSButton (new CGRect (0, 0, 81, 16));
      button.SetButtonType (NSButtonType.MomentaryPushIn);
      button.Title = "Delete";
      button.Tag = row;

      // Wireup events
      button.Activated += (sender, e) => {
        // Get button and product
        var btn = sender as NSButton;
        var product = DataSource.Products [(int)btn.Tag];

        // Configure alert
        var alert = new NSAlert () {
          AlertStyle = NSAlertStyle.Informational,
          InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
          MessageText = $"Delete {product.Title}?",
        };
        alert.AddButton ("Cancel");
        alert.AddButton ("Delete");
        alert.BeginSheetForResponse (Controller.View.Window, (result) => {
          // Should we delete the requested row?
          if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
          }
        });
      };

      // Add to view
      view.AddSubview (button);
      break;
    }

  }

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
  case "Action":
    foreach (NSView subview in view.Subviews) {
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

Betrachten wir nun mehrere Abschnitte dieses Codes ausführlicher. Zuerst wird eine Aktion basierend auf dem Namen der Spalte erstellt, wenn eine neue `NSTableViewCell` erstellt wird. Für die ersten beiden Spalten (**Produkt** und **Details**) wird die neue `ConfigureTextField`-Methode aufgerufen.

In der Spalte **Aktion** wird ein neuer `NSButton` erstellt und der Zelle als untergeordnete Ansicht hinzugefügt:

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

Die `Tag`-Eigenschaft der Schaltfläche wird verwendet, um die Anzahl der zurzeit verarbeiteten Zeilen zu speichern. Diese Zahl wird später verwendet, wenn der Benutzer anfordert, eine Zeile im `Activated` Ereignis der Schaltfläche zu löschen:

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
  var product = DataSource.Products [(int)btn.Tag];

  // Configure alert
  var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
    MessageText = $"Delete {product.Title}?",
  };
  alert.AddButton ("Cancel");
  alert.AddButton ("Delete");
  alert.BeginSheetForResponse (Controller.View.Window, (result) => {
    // Should we delete the requested row?
    if (result == 1001) {
      // Remove the given row from the dataset
      DataSource.Products.RemoveAt((int)btn.Tag);
      Controller.ReloadTable ();
    }
  });
};
```

Am Anfang des Ereignis Handlers werden die Schaltfläche und das Produkt für die angegebene Tabellenzeile angezeigt. Daraufhin wird dem Benutzer eine Warnung angezeigt, die das Löschen der Zeile bestätigt. Wenn der Benutzer das Löschen der Zeile auswählt, wird die angegebene Zeile aus der Datenquelle entfernt, und die Tabelle wird erneut geladen:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Wenn die Tabellen Ansichts Zelle zum Schluss wieder verwendet wird, anstatt neu erstellt zu werden, konfiguriert der folgende Code Sie basierend auf der verarbeiteten Spalte:

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
  view.ImageView.Image = NSImage.ImageNamed ("tag.png");
  view.TextField.StringValue = DataSource.Products [(int)row].Title;
  view.TextField.Tag = row;
  break;
case "Details":
  view.TextField.StringValue = DataSource.Products [(int)row].Description;
  view.TextField.Tag = row;
  break;
case "Action":
  foreach (NSView subview in view.Subviews) {
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

In der Spalte **Aktion** werden alle untergeordneten Sichten überprüft, bis die `NSButton` gefunden werden. Anschließend wird `Tag` Eigenschaft aktualisiert, um auf die aktuelle Zeile zu verweisen.

Wenn diese Änderungen vorgenommen wurden, hat jede Zeile, wenn die app ausgeführt wird, eine **Lösch** Schaltfläche:

[![](table-view-images/delete02.png "The table view with deletion buttons")](table-view-images/delete02.png#lightbox)

Wenn der Benutzer auf eine **Lösch** Schaltfläche klickt, wird eine Warnung angezeigt, in der Sie aufgefordert werden, die angegebene Zeile zu löschen:

[![](table-view-images/delete03.png "A delete row alert")](table-view-images/delete03.png#lightbox)

Wenn der Benutzer löschen auswählt, wird die Zeile entfernt, und die Tabelle wird neu gezeichnet:

[![](table-view-images/delete04.png "The table after the row is deleted")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Daten Bindungs Tabellen-Sichten

Durch die Verwendung von Schlüssel-Wert-Codierungs-und Daten Bindungs Techniken in ihrer xamarin. Mac-Anwendung können Sie die Menge des Codes, den Sie schreiben und verwalten müssen, erheblich verringern, um Benutzeroberflächen Elemente aufzufüllen und mit Ihnen zu arbeiten. Außerdem profitieren Sie von der weiteren Entkopplung ihrer Sicherungsdaten (_Datenmodell_) von der Front-End-Benutzeroberfläche (_Model-View-Controller_). Dies führt zu einer einfacheren Wartung und einem flexibleren Anwendungs Entwurf.

Key-Value Coding (KVC) ist ein Mechanismus für den indirekten Zugriff auf die Eigenschaften eines Objekts, indem Schlüssel (speziell formatierte Zeichen folgen) verwendet werden, um Eigenschaften zu identifizieren, anstatt über Instanzvariablen oder Accessormethoden (`get/set`) auf Sie zuzugreifen. Durch die Implementierung von Schlüssel-Wert-Codierungs kompatiblen Accessoren in ihrer xamarin. Mac-Anwendung erhalten Sie Zugriff auf andere macOS-Features, wie z. b. Key-Value-Beobachtungen (KVO), Datenbindung, Kerndaten, Cocoa-Bindungen und scriptbarkeit.

Weitere Informationen finden Sie im Abschnitt " [Datenbindung für Tabellenansicht](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) " in unserer Datenbindung und in der Dokumentation zu [Schlüssel-Wert-Codierungen](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde die Arbeit mit Tabellen Sichten in einer xamarin. Mac-Anwendung ausführlich erläutert. Wir haben die verschiedenen Typen und Verwendungsmöglichkeiten von Tabellen Sichten gesehen, das Erstellen und Verwalten von Tabellen Sichten in der Interface Builder von Xcode und das Arbeiten mit Tabellen C# Ansichten im Code.

## <a name="related-links"></a>Verwandte Links

- [Mactables (Beispiel)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages (Beispiel)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac (Hallo, Mac)](~/mac/get-started/hello-mac.md)
- [Gliederungsansichten](~/mac/user-interface/outline-view.md)
- [Quelllisten](~/mac/user-interface/source-list.md)
- [Datenbindung und Schlüssel/Wert-Codierung](~/mac/app-fundamentals/databinding.md)
- [Eingaberichtlinien für OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [Nstableviewdelegat](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [Nstableviewdatasource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
