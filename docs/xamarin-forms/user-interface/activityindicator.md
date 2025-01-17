---
title: Aktivitätsindikator in xamarin. Forms
description: Das Steuerelement activityindicator gibt Benutzern an, dass die Anwendung mit einer langwierigen Aktivität beschäftigt ist, ohne dass der Fortschritt angegeben wird. In diesem Artikel wird erläutert, wie ein activityindicator in XAML und Code verwendet wird.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: 0694439f5e363399e0442c9883426c0f0bf5d989
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887429"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms-activityindicator
[![Beispiel herunterladen](~/media/shared/download.png) Herunterladen des Beispiels](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Das xamarin. Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) -Steuerelement zeigt eine Animation an, die anzeigt, dass die Anwendung mit einer langwierigen Aktivität beschäftigt ist. Anders als [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)der gibt `ActivityIndicator` den Fortschritt an. Der `ActivityIndicator` erbt von [`View`](xref:Xamarin.Forms.View).

Die folgenden Screenshots zeigen ein `ActivityIndicator` Steuerelement unter IOS und Android:

![Screenshot von activityindicator unter IOS und Android](activityindicator-images/activityindicators-default.png "Screenshot von activityindicator unter IOS und Android")

Das `ActivityIndicator` -Steuerelement definiert die folgenden Eigenschaften:

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)ein `Color` -Wert, der die Anzeige Farbe `ActivityIndicator`von definiert.
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)ein `bool` Wert, der `ActivityIndicator` angibt, ob sichtbar und animiert werden soll. Wenn der Wert ist `false` , `ActivityIndicator` ist nicht sichtbar.

Diese Eigenschaften werden durch [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) -Objekte gestützt. Dies bedeutet, dass das `ActivityIndicator` formatiert werden kann und das Ziel von Daten Bindungen ist.

## <a name="create-an-activityindicator"></a>Erstellen eines activityindicator

Die `ActivityIndicator` Klasse kann in XAML instanziiert werden. Die `IsRunning` -Eigenschaft bestimmt, ob das Steuerelement sichtbar und animiert ist. Die `IsRunning` -Eigenschaft hat `false`den Standardwert. Im folgenden Beispiel wird gezeigt, wie ein `ActivityIndicator` in XAML mit dem optionalen `IsRunning` Eigenschaften Satz instanziiert wird:

```xaml
<ActivityIndicator IsRunning="true" />
```

Ein `ActivityIndicator` kann auch im Code erstellt werden:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Aktivitätsindikator-Darstellungs Eigenschaften

Die `Color` -Eigenschaft definiert `ActivityIndicator` die Farbe. Im folgenden Beispiel wird gezeigt, wie ein `ActivityIndicator` in XAML mit dem `Color` -Eigenschaften Satz instanziiert wird:

```xaml
<ActivityIndicator Color="Orange" />
```

Die `Color` -Eigenschaft kann auch festgelegt werden, `ActivityIndicator` wenn ein-Objekt im Code erstellt wird:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

Die folgenden Screenshots zeigen den `ActivityIndicator` , bei `Color` dem die- `Color.Orange` Eigenschaft unter IOS und Android auf festgelegt ist:

![Screenshot des formatierten activityindicator unter IOS und Android](activityindicator-images/activityindicators-styled.png "Screenshot des formatierten activityindicator unter IOS und Android")

## <a name="related-links"></a>Verwandte Links

* [Activityindicator-Demos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
