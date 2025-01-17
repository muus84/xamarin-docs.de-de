---
title: Design einer xamarin. Forms-Anwendung
description: Design kann in xamarin. Forms-Anwendungen implementiert werden, indem für jedes Design ein ResourceDictionary erstellt und anschließend die Ressourcen mit der DynamicResource-Markup Erweiterung geladen werden.
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
ms.openlocfilehash: 3e0f508a9c980c02681f1be581846f9f2f25e2d0
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "69529278"
---
# <a name="theming-a-xamarinforms-application"></a>Design einer xamarin. Forms-Anwendung

[![Beispiel herunterladen](~/media/shared/download.png) Das Beispiel herunterladen](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Xamarin. Forms-Anwendungen können mit der `DynamicResource` Markup Erweiterung dynamisch auf Formatänderungen reagieren, die zur Laufzeit dynamisch verwendet werden. Diese Markup Erweiterung ähnelt der `StaticResource` Markup Erweiterung, da beide eine Wörterbuch Taste verwenden, um einen Wert aus einer [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)abzurufen. Obwohl die `StaticResource` Markup Erweiterung eine einzelne Wörterbuchsuche ausführt, behält die `DynamicResource` Markup Erweiterung einen Link zum Wörterbuch Schlüssel bei. Wenn also der Wert, der dem Schlüssel zugeordnet ist, ersetzt wird, wird die Änderung auf die [`VisualElement`](xref:Xamarin.Forms.VisualElement)angewendet. Dies ermöglicht die Implementierung von Lauf Zeit Designs in xamarin. Forms-Anwendungen.

Der Prozess der Implementierung von Lauf Zeit Designs in einer xamarin. Forms-Anwendung lautet wie folgt:

1. Definieren Sie die Ressourcen für jedes Design in einem [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).
1. Verwenden Sie Design Ressourcen in der Anwendung, indem Sie die `DynamicResource` Markup Erweiterung verwenden.
1. Legen Sie ein Standarddesign in der **app. XAML** -Datei der Anwendung fest.
1. Fügen Sie Code hinzu, um ein Design zur Laufzeit zu laden.

Die folgenden Screenshots zeigen Design Seiten, bei denen die IOS-Anwendung ein helles Design und die Android-Anwendung mit einem dunklen Design verwendet:

[![Screenshot der Hauptseite einer Designs-app unter IOS und Android](theming-images/main-page-both-themes.png "Hauptseite der App Thema")](theming-images/main-page-both-themes-large.png#lightbox "Hauptseite der App Thema")
[ ![Screenshot der Detailseite einer Designs-app unter IOS und Android](theming-images/detail-page-both-themes.png "Detail Seite der APP mit Design")](theming-images/detail-page-both-themes-large.png#lightbox "Detail Seite der APP mit Design")

## <a name="define-themes"></a>Definieren von Designs

Ein Design wird als Auflistung von Ressourcen Objekten definiert, die in einem [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)gespeichert werden.

Das folgende Beispiel zeigt die `LightTheme` aus der Beispielanwendung:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.LightTheme">
    <Color x:Key="PageBackgroundColor">White</Color>
    <Color x:Key="NavigationBarColor">WhiteSmoke</Color>
    <Color x:Key="PrimaryColor">WhiteSmoke</Color>
    <Color x:Key="SecondaryColor">Black</Color>
    <Color x:Key="PrimaryTextColor">Black</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">Gray</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

Das folgende Beispiel zeigt die `DarkTheme` aus der Beispielanwendung:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.DarkTheme">
    <Color x:Key="PageBackgroundColor">Black</Color>
    <Color x:Key="NavigationBarColor">Teal</Color>
    <Color x:Key="PrimaryColor">Teal</Color>
    <Color x:Key="SecondaryColor">White</Color>
    <Color x:Key="PrimaryTextColor">White</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">WhiteSmoke</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

Jede [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) enthält [`Color`](xref:Xamarin.Forms.Color) Ressourcen, die ihre jeweiligen Designs definieren, wobei jede `ResourceDictionary` identische Schlüsselwerte verwendet. Weitere Informationen zu Ressourcenwörterbüchern, finden Sie unter [Ressourcenverzeichnisse](~/xamarin-forms/xaml/resource-dictionaries.md).

> [!IMPORTANT]
> Für jede `ResourceDictionary`, die die `InitializeComponent`-Methode aufruft, ist eine Code Behind-Datei erforderlich. Dies ist erforderlich, damit ein CLR-Objekt, das das ausgewählte Design darstellt, zur Laufzeit erstellt werden kann.

## <a name="set-a-default-theme"></a>Festlegen eines Standarddesigns

Eine Anwendung erfordert ein Standarddesign, sodass Steuerelemente Werte für die Ressourcen aufweisen, die Sie nutzen. Ein Standarddesign kann festgelegt werden, indem die [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) des Designs in den `ResourceDictionary` auf Anwendungsebene zusammengeführt wird, der in " **app. XAML**" definiert ist:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

Weitere Informationen zum Zusammenführen von Ressourcen Wörterbüchern finden Sie unter [zusammengeführte Ressourcen](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries)Verzeichnisse.

## <a name="consume-theme-resources"></a>Verbrauchen von Design Ressourcen

Wenn eine Anwendung eine Ressource nutzen möchte, die in einer [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) gespeichert ist, die ein Design darstellt, sollte Sie dies mit der `DynamicResource` Markup Erweiterung tun. Dadurch wird sichergestellt, dass die Werte aus dem neuen Design angewendet werden, wenn zur Laufzeit ein anderes Design ausgewählt wird.

Das folgende Beispiel zeigt drei Stile aus der Beispielanwendung, die auf [`Label`](xref:Xamarin.Forms.Label) -Objekte angewendet werden können:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>

        <Style x:Key="LargeLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource SecondaryTextColor}" />
            <Setter Property="FontSize"
                    Value="30" />
        </Style>

        <Style x:Key="MediumLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource PrimaryTextColor}" />
            <Setter Property="FontSize"
                    Value="25" />
        </Style>

        <Style x:Key="SmallLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource TertiaryTextColor}" />
            <Setter Property="FontSize"
                    Value="15" />
        </Style>

    </Application.Resources>
</Application>
```

Diese Stile werden im Ressourcen Wörterbuch auf Anwendungsebene definiert, sodass Sie von mehreren Seiten genutzt werden können. Jeder Stil nutzt Design Ressourcen mit der `DynamicResource` Markup Erweiterung.

Diese Stile werden dann von Seiten genutzt:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ThemingDemo"
             x:Class="ThemingDemo.UserSummaryPage"
             Title="User Summary"
             BackgroundColor="{DynamicResource PageBackgroundColor}">
    ...
    <ScrollView>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="200" />
                <RowDefinition Height="120" />
                <RowDefinition Height="70" />
            </Grid.RowDefinitions>
            <Grid BackgroundColor="{DynamicResource PrimaryColor}">
                <Label Text="Face-Palm Monkey"
                       VerticalOptions="Center"
                       Margin="15"
                       Style="{StaticResource MediumLabelStyle}" />
                ...
            </Grid>
            <StackLayout Grid.Row="1"
                         Margin="10">
                <Label Text="This monkey reacts appropriately to ridiculous assertions and actions."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Cynical but not unfriendly."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Seven varieties of grimaces."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Doesn't laugh at your jokes."
                       Style="{StaticResource SmallLabelStyle}" />
            </StackLayout>
            ...
        </Grid>
    </ScrollView>
</ContentPage>
```

Wenn eine Design Ressource direkt genutzt wird, sollte Sie mit der `DynamicResource` Markup Erweiterung genutzt werden. Wenn jedoch ein Stil verwendet wird, der die `DynamicResource` Markup Erweiterung verwendet, sollte er mit der `StaticResource` Markup Erweiterung verwendet werden.

Weitere Informationen zum Formatieren finden Sie unter Formatieren von [xamarin. Forms-Apps mithilfe von XAML-Stilen](~/xamarin-forms/user-interface/styles/xaml/index.md). Weitere Informationen zur `DynamicResource` Markup Erweiterung finden Sie unter [dynamische Stile in xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md).

## <a name="load-a-theme-at-runtime"></a>Ein Design zur Laufzeit laden

Wenn ein Design zur Laufzeit ausgewählt wird, sollte die Anwendung Folgendes ausführen:

1. Entfernen Sie das aktuelle Design aus der Anwendung. Dies wird erreicht, indem die [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) -Eigenschaft der [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)auf Anwendungsebene gelöscht wird.
2. Ausgewähltes Designladen. Dies wird erreicht, indem der `MergedDictionaries`-Eigenschaft der `ResourceDictionary`auf Anwendungsebene eine Instanz des ausgewählten Designs hinzugefügt wird.

Alle [`VisualElement`](xref:Xamarin.Forms.VisualElement) Objekte, die Eigenschaften mit der `DynamicResource` Markup Erweiterung festlegen, wenden dann die neuen Designwerte an. Dies tritt auf, weil die `DynamicResource` Markup Erweiterung einen Link zu Wörterbuch Schlüsseln beibehält. Wenn also die den Schlüsseln zugeordneten Werte ersetzt werden, werden die Änderungen auf die `VisualElement` Objekte angewendet.

In der Beispielanwendung wird ein Design über eine modale Seite ausgewählt, die eine [`Picker`](xref:Xamarin.Forms.Picker)enthält. Der folgende Code zeigt die `OnPickerSelectionChanged`-Methode, die ausgeführt wird, wenn sich das ausgewählte Design ändert:

```csharp
void OnPickerSelectionChanged(object sender, EventArgs e)
{
    Picker picker = sender as Picker;
    Theme theme = (Theme)picker.SelectedItem;

    ICollection<ResourceDictionary> mergedDictionaries = Application.Current.Resources.MergedDictionaries;
    if (mergedDictionaries != null)
    {
        mergedDictionaries.Clear();

        switch (theme)
        {
            case Theme.Dark:
                mergedDictionaries.Add(new DarkTheme());
                break;
            case Theme.Light:
            default:
                mergedDictionaries.Add(new LightTheme());
                break;
        }
    }
}
```

## <a name="related-links"></a>Verwandte Links

- [Design (Beispiel)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Ressourcenverzeichnisse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Dynamische Stile in xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Formatieren von Xamarin.Forms-Apps mithilfe von XAML-Formatvorlagen](~/xamarin-forms/user-interface/styles/xaml/index.md)
