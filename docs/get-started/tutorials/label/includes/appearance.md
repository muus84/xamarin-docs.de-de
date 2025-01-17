---
ms.openlocfilehash: 4fc6c50b5aa2ce502b4157ca2b15f0d33a68ecd1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896685"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Bearbeiten Sie in **MainPage.xaml** die [`Label`](xref:Xamarin.Forms.Label)-Deklaration, um die visuelle Darstellung zu ändern:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Dieser Code legt Eigenschaften fest, die die visuelle Darstellung von [`Label`](xref:Xamarin.Forms.Label) ändern. Die [`TextColor`](xref:Xamarin.Forms.Label.TextColor)-Eigenschaft legt die Farbe des `Button`-Textes fest. Die [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes)-Eigenschaft setzt die Schriftart für die Bezeichnung auf „Kursiv“, und die [`FontSize`](xref:Xamarin.Forms.Label.FontSize)-Eigenschaft legt den Schriftgrad fest. Zudem wird das `Label`-Element unterstrichen, indem dessen [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations)-Eigenschaft festgelegt wird, und es wird horizontal zentriert, indem die [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)-Eigenschaft auf [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) festgelegt wird.

1. Klicken Sie in der Symbolleiste von Visual Studio auf die Schaltfläche zum **Starten** (die dreieckige Schaltfläche, die einer Wiedergabetaste ähnelt), um die Anwendung im ausgewählten iOS-Remotesimulator oder Android-Emulator zu starten: Beachten Sie, wie sich die Darstellung von [`Label`](xref:Xamarin.Forms.Label) geändert hat:

    [![Screenshot: Bezeichnung mit geänderter visueller Darstellung unter iOS und Android](../images/change-label-appearance.png "Bezeichnung mit geänderter Darstellung")](../images/change-label-appearance-large.png#lightbox "Bezeichnung mit geänderter Darstellung")

    Weitere Informationen zur Einstellung der [`Label`](xref:Xamarin.Forms.Label)-Darstellung finden Sie im Leitfaden [Xamarin.Forms-Bezeichnung](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio für Mac](#tab/vsmac)

1. Bearbeiten Sie in **MainPage.xaml** die [`Label`](xref:Xamarin.Forms.Label)-Deklaration, um die visuelle Darstellung zu ändern:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Dieser Code legt Eigenschaften fest, die die visuelle Darstellung von [`Label`](xref:Xamarin.Forms.Label) ändern. Die [`TextColor`](xref:Xamarin.Forms.Label.TextColor)-Eigenschaft legt die Farbe des `Button`-Textes fest. Die [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes)-Eigenschaft setzt die Schriftart für die Bezeichnung auf „Kursiv“, und die [`FontSize`](xref:Xamarin.Forms.Label.FontSize)-Eigenschaft legt den Schriftgrad fest. Zudem wird das `Label`-Element unterstrichen, indem dessen [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations)-Eigenschaft festgelegt wird, und es wird horizontal zentriert, indem die [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)-Eigenschaft auf [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) festgelegt wird.

1. Klicken Sie in der Symbolleiste von Visual Studio für Mac auf die Schaltfläche zum **Starten** (die dreieckige Schaltfläche, die einer Wiedergabetaste ähnelt), um die Anwendung im ausgewählten iOS-Simulator oder Android-Emulator zu starten. Beachten Sie, wie sich die Darstellung von [`Label`](xref:Xamarin.Forms.Label) geändert hat:

    [![Screenshot: Bezeichnung mit geänderter visueller Darstellung unter iOS und Android](../images/change-label-appearance.png "Bezeichnung mit geänderter Darstellung")](../images/change-label-appearance-large.png#lightbox "Bezeichnung mit geänderter Darstellung")

    Weitere Informationen zur Einstellung der [`Label`](xref:Xamarin.Forms.Label)-Darstellung finden Sie im Leitfaden [Xamarin.Forms-Bezeichnung](~/xamarin-forms/user-interface/text/label.md).
