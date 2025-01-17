---
title: Verweis auf DataPages-Steuerelemente
description: In diesem Artikel werden die Steuerelemente vorgestellt, die im xamarin. Forms DataPages-nuget-Paket verfügbar sind.
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e92669d9938b9fe48a1a589e0465acd03f129716
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759893"
---
# <a name="datapages-controls-reference"></a>Verweis auf DataPages-Steuerelemente

![](~/media/shared/preview.png "Diese API ist derzeit als Vorschauversion")

> [!IMPORTANT]
> Für DataPages ist ein xamarin. Forms-Design Verweis zum Rendering erforderlich. Dies umfasst das Installieren des [xamarin. Forms. Theme. Base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) -nuget-Pakets in Ihrem Projekt, gefolgt von den nuget-Paketen [xamarin. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) oder [xamarin. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Die xamarin. Forms DataPages nuget enthält eine Reihe von Steuerelementen, die von der Datenquellen Bindung profitieren können.

Um diese Steuerelemente in XAML zu verwenden, stellen Sie sicher, dass der Namespace enthalten ist `xmlns:pages` , z. b. in der folgenden Deklaration:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

In den folgenden Beispielen `DynamicResource` sind Verweise enthalten, die im Ressourcen Wörterbuch des Projekts vorhanden sein müssen, um funktionieren zu können. Außerdem gibt es ein Beispiel für das Erstellen eines [benutzerdefinierten Steuer](#custom) Elements.

## <a name="built-in-controls"></a>Integrierte Steuerelemente

* [HeroImage](#heroimage)
* [ListItem](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>Heroimage

Das `HeroImage` -Steuerelement verfügt über vier Eigenschaften:

* Text
* Detail
* ImageSource
* Eingehen

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "HeroImage-Steuerelement für Android") ![](controls-images/heroimage-dark-android.png "HeroImage-Steuerelement für Android")

**iOS**

![](controls-images/heroimage-light-ios.png "HeroImage-Steuerelement für iOS") ![](controls-images/heroimage-dark-ios.png "HeroImage-Steuerelement für iOS")

<a name="listitem" />

### <a name="listitem"></a>ListItem

Das `ListItem` Layout des Steuer Elements ähnelt der systemeigenen IOS-und Android-Liste oder Tabellenzeilen, kann jedoch auch als reguläre Ansicht verwendet werden. Im unten aufgeführten Beispielcode wird in einem `StackLayout`gehostet angezeigt, kann aber auch in Daten gebundenen scolte-Listen Steuerelementen verwendet werden.

Es gibt fünf Eigenschaften:

* Titel
* Detail
* ImageSource
* PlaceholdImageSource
* Eingehen

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

Diese Screenshots zeigen die `ListItem` auf IOS-und Android-Plattformen, die sowohl das helle als auch das dunkle Design verwenden:

**Android**

![](controls-images/listitem-light-android.png "ListItem-Steuerelement für Android") ![](controls-images/listitem-dark-android.png "ListItem-Steuerelement für Android")

**iOS**

![](controls-images/listitem-light-ios.png "ListItem-Steuerelement für iOS") ![](controls-images/listitem-dark-ios.png "ListItem-Steuerelement für iOS")

## <a name="custom-control-example"></a>Beispiel für einen benutzerdefinierten

Das Ziel dieses benutzerdefinierten `CardView` Steuer Elements ist, der nativen Android-Kartenansicht zu ähneln.

Sie enthält drei Eigenschaften:

* Text
* Detail
* ImageSource

Das Ziel ist ein benutzerdefiniertes Steuerelement, das wie der folgende Code aussieht (beachten `xmlns:local` Sie, dass ein benutzerdefiniertes erforderlich ist, das auf die aktuelle Assembly verweist):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Es sollte wie in den nachfolgenden Screenshots aussehen, indem Farben verwendet werden, die den integrierten hellen und dunklen Designs entsprechen:

**Android**

![](controls-images/cardview-light-android.png "CardView-benutzerdefinierte Steuerelement für Android") ![](controls-images/cardview-dark-android.png "CardView-benutzerdefinierte Steuerelement für Android")

**iOS**

![](controls-images/cardview-light-ios.png "CardView-benutzerdefiniertes Steuerelement unter iOS") ![](controls-images/cardview-dark-ios.png "CardView-benutzerdefiniertes Steuerelement unter iOS")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>Aufbauen der benutzerdefinierten CardView

1. [DataView-Unterklasse](#1)
2. [Schriftart, Layout und Ränder definieren](#2)
3. [Erstellen von Stilen für die untergeordneten Elemente des Steuer Elements](#3)
4. [Erstellen der Steuerelement Layout-Vorlage](#4)
5. [Hinzufügen der Design spezifischen Ressourcen](#5)
6. [Festlegen von "ControlTemplate" für die CardView-Klasse](#6)
7. [Hinzufügen des Steuer Elements zu einer Seite](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1. DataView-Unterklasse

Die C# -Unterklasse `DataView` von definiert die bindbaren Eigenschaften für das-Steuerelement.

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

<a name="2" />

#### <a name="2-define-font-layout-and-margins"></a>2. Schriftart, Layout und Ränder definieren

Der Steuerelement-Designer würde diese Werte als Teil des Benutzeroberflächen Entwurfs für das benutzerdefinierte Steuerelement herausfinden. Wenn plattformspezifische Spezifikationen erforderlich sind, wird `OnPlatform` das-Element verwendet.

Beachten Sie, dass einige Werte `StaticResource`auf s verweisen – diese werden in [Schritt 5](#5)definiert.

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

<a name="3" />

#### <a name="3-create-styles-for-the-controls-children"></a>3. Erstellen von Stilen für die untergeordneten Elemente des Steuer Elements

Verweisen auf alle Elemente, die für definiert sind, um die untergeordneten Elemente zu erstellen, die im benutzerdefinierten Steuerelement verwendet werden:

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

<a name="4" />

#### <a name="4-create-the-control-layout-template"></a>4. Erstellen der Steuerelement Layout-Vorlage

Das visuelle Design des benutzerdefinierten Steuer Elements wird mithilfe der oben definierten Ressourcen explizit in der Steuerelement Vorlage deklariert:

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />

    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

<a name="5" />

#### <a name="5-add-the-theme-specific-resources"></a>5. Hinzufügen der Design spezifischen Ressourcen

Da es sich um ein benutzerdefiniertes Steuerelement handelt, fügen Sie die Ressourcen hinzu, die dem verwendeten Design entsprechen:

##### <a name="light-theme-colors"></a>Helle Design Farben

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>Farben des dunklen Designs

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

<a name="6" />

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. Festlegen von "ControlTemplate" für die CardView-Klasse

Stellen Sie abschließend sicher C# , dass die in [Schritt 1](#1) erstellte-Klasse mithilfe eines `Style` `Setter` -Elements die in [Schritt 4](#4) definierte Steuerelement Vorlage verwendet.

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7. Hinzufügen des Steuer Elements zu einer Seite

Das `CardView` Steuerelement kann nun zu einer Seite hinzugefügt werden. Das folgende Beispiel zeigt, wie es in `StackLayout`einem gehostet wird:

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
