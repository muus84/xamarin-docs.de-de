---
title: Dynamische Stile in Xamarin.Forms
description: In diesem Artikel wird erläutert, wie eine Xamarin.Forms-Anwendung auf Änderungen an Formatvorlagen dynamisch zur Laufzeit reagieren kann, mithilfe von dynamischen Ressourcen.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228191"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Dynamische Stile in Xamarin.Forms

[![Beispiel herunterladen](~/media/shared/download.png) Das Beispiel herunterladen](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Stile reagieren nicht auf Eigenschafts Änderungen und bleiben für die Dauer einer Anwendung unverändert. Wenn Sie z. b. einem visuellen Element einen Stil zuweisen und eine der Setter-Instanzen geändert, entfernt oder eine neue Setter-Instanz hinzugefügt wurde, werden die Änderungen nicht auf das visuelle Element angewendet. Anwendungen können jedoch zur Laufzeit dynamisch auf Formatänderungen reagieren, indem Sie dynamische Ressourcen verwenden._

Die `DynamicResource` Markuperweiterung ähnelt der `StaticResource` -Markuperweiterung in, die beide einen Dictionary-Schlüssel verwenden, zum Abrufen der eines Werts aus einer [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Allerdings zwar die `StaticResource` führt ein einziges wörterbruch-Lookup der `DynamicResource` verwaltet eine Verbindung zu der Wörterbuchschlüssel. Daher wird dem Schlüssel zugeordnete Wörterbucheintrags ersetzt wird, die Änderung auf das visuelle Element angewendet. Dadurch können Änderungen zur Laufzeit Stil in einer Anwendung vorgenommen werden.

Im folgenden Codebeispiel wird veranschaulicht, *dynamische* Stilen in einer XAML-Seite:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Die [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Instanzen verwenden den `DynamicResource` Markuperweiterung auf eine [ `Style` ](xref:Xamarin.Forms.Style) mit dem Namen `searchBarStyle`, die nicht in der XAML definiert. Allerdings da die [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) Eigenschaften der `SearchBar` Instanzen festgelegt werden, werden eine `DynamicResource`, fehlende Wörterbuchschlüssel eine Ausnahme ausgelöst wird, führt nicht.

Stattdessen in der CodeBehind-Datei der Konstruktor erstellt ein [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) Eintrag mit dem Schlüssel `searchBarStyle`, wie im folgenden Codebeispiel gezeigt:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Wenn die `OnButtonClicked` -Ereignishandler ausgeführt wird, `searchBarStyle` wechselt zwischen `blueSearchBarStyle` und `greenSearchBarStyle`. Dies ergibt die in den folgenden Screenshots gezeigte Darstellung:

[![Blue Dynamic Style-Beispiel](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![Beispiel für ein dynamisches dynamisches](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox) Format

Im folgenden Codebeispiel wird veranschaulicht, die entsprechende Seite in C# geschrieben wird:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

In c# die [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Instanzen verwenden den [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) Methode zum Verweisen auf `searchBarStyle`. Die `OnButtonClicked` Ereignishandlercode ist identisch, mit dem XAML-Beispiel, und bei der Ausführung `searchBarStyle` wechselt zwischen `blueSearchBarStyle` und `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Vererbung dynamischer Stile

Ableiten eines Stils in einer dynamischen Stil nicht erreicht werden, mithilfe der [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) Eigenschaft. Stattdessen die [ `Style` ](xref:Xamarin.Forms.Style) Klasse enthält die [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) -Eigenschaft, die auf ein Dictionary-Schlüssel, dessen Wert festgelegt werden kann, kann dynamisch ändern.

Im folgenden Codebeispiel wird veranschaulicht, *dynamische* formatieren Sie Vererbung in einer XAML-Seite:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Die [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Instanzen verwenden den `StaticResource` Markuperweiterung auf eine [ `Style` ](xref:Xamarin.Forms.Style) mit dem Namen `tealSearchBarStyle`. Dies `Style` einige zusätzlichen Eigenschaften festgelegt und verwendet die [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) -Eigenschaft zum Verweisen auf `searchBarStyle`. Die `DynamicResource` Markuperweiterung ist nicht erforderlich, da `tealSearchBarStyle` wird nicht geändert werden, mit Ausnahme der `Style` abgeleitet ist. Aus diesem Grund `tealSearchBarStyle` verwaltet eine Verbindung zu `searchBarStyle` und geändert wird, wenn es sich bei der grundlegende Stil ändert.

In der CodeBehind-Datei, die der Konstruktor erstellt ein [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) Eintrag mit dem Schlüssel `searchBarStyle`gemäß dem vorherigen Beispiel, das dynamische Formate veranschaulicht. Wenn die `OnButtonClicked` -Ereignishandler ausgeführt wird, `searchBarStyle` wechselt zwischen `blueSearchBarStyle` und `greenSearchBarStyle`. Dies ergibt die in den folgenden Screenshots gezeigte Darstellung:

[Beispiel für![blaue Vererbung mit dynamischem](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox) Format
[![Beispiel für Vererbung im dynamischen Stil](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

Im folgenden Codebeispiel wird veranschaulicht, die entsprechende Seite in C# geschrieben wird:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

Die `tealSearchBarStyle` direkt zugewiesen ist die [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) Eigenschaft der [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Instanzen. Dies `Style` einige zusätzlichen Eigenschaften festgelegt, und verwendet die [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) -Eigenschaft zum Verweisen auf `searchBarStyle`. Die [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) Methode ist hier erforderlich, da `tealSearchBarStyle` wird nicht geändert werden, mit Ausnahme der `Style` abgeleitet ist. Aus diesem Grund `tealSearchBarStyle` verwaltet eine Verbindung zu `searchBarStyle` und geändert wird, wenn es sich bei der grundlegende Stil ändert.

## <a name="related-links"></a>Verwandte Links

- [XAML-Markuperweiterungen](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Dynamische Stile (Beispiel)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Arbeiten mit Stilen (Beispiel)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stil](xref:Xamarin.Forms.Style)
- [Set-Methode](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Zugehörige Videos

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
