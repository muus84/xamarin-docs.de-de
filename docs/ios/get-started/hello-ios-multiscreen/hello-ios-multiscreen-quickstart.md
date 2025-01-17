---
title: 'Schnellstart: Hallo, iOS Multiscreen'
description: In diesem Artikel wird demonstriert, wie Sie die Phoneword-Beispielanwendung zum Hinzufügen eines zweiten Bildschirms erweitern. Außerdem werden der Model-View-Controller, die iOS-Navigation und andere Kernkonzepte der iOS-Entwicklung beschrieben.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: c89a3616bfa239ba919ae9750082bcef48c9f890
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023372"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Schnellstart: Hallo, iOS Multiscreen

In diesem Teil des Tutorials wird der Phoneword-Anwendung eine zweite Anzeige hinzugefügt, auf der die Liste der Telefonnummern aufgeführt wird, die mit der App angerufen wurden. Die endgültige Anwendung verfügt dann über einen zweiten Bildschirm, auf dem, wie im folgenden Screenshot veranschaulicht, die Anrufliste angezeigt wird:

[![](hello-ios-multiscreen-quickstart-images/00.png "The final application will have a second screen that displays the call history, as illustrated by this screenshot")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

In den [entsprechenden ausführlichen Erläuterungen](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md) wird die erstellte Anwendung überprüft sowie die Architektur, Navigation und weitere neue iOS-Konzepte erläutert, denen Sie noch begegnen werden.

## <a name="requirements"></a>Requirements (Anforderungen)

Dieser Leitfaden macht an der Stelle weiter, an der das Dokument „Hallo, iOS“ aufgehört hat. Zuvor müssen Sie den [Schnellstart: Hallo, iOS](~/ios/get-started/hello-ios/index.md) abgeschlossen haben. Die vollständige Version der Phoneword-App kann auf der Seite [Beispiel: Hallo, iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) heruntergeladen werden.

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>Exemplarische Vorgehensweise unter macOS

In dieser exemplarischen Vorgehensweise wird Ihrer **Phoneword**-Anwendung ein Bildschirm für die Anrufliste hinzugefügt.

1. Öffnen Sie in Visual Studio für Mac die **Phoneword**-Anwendung. Bei Bedarf können Sie [hier](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) die vollständige Phoneword-Anwendung aus dem Leitfaden [Exemplarische Vorgehensweise: Hallo, iOS](~/ios/get-started/hello-ios/index.md) herunterladen.

2. Öffnen Sie im **Projektmappenpad** die Datei **Main.storyboard**:

    ![](hello-ios-multiscreen-quickstart-images/02new.png "The Main.storyboard in the iOS Designer")

3. Ziehen Sie aus der **Toolbox** einen **Navigationscontroller** in die Entwurfsoberfläche (damit alle Elemente auf die Entwurfsoberfläche passen, müssen Sie die Ansicht möglicherweise verkleinern):

    ![](hello-ios-multiscreen-quickstart-images/03new.png "Drag a navigation controller from the Toolbox onto the design surface")

4. Ziehen Sie zum Ändern des Startpunkts der Anwendung den **Sourceless Segue** (den grauen Pfeil links neben dem Ansichtscontroller) zum **Navigationscontroller**:

    ![](hello-ios-multiscreen-quickstart-images/04new.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. Klicken Sie auf die untere Leiste, um den vorhandenen **Stammansichtscontroller** auszuwählen. Drücken Sie **ENTFERNEN**, um ihn aus der Entwurfsoberfläche zu entfernen.
Verschieben Sie anschließend die **Phoneword**-Szene neben den **Navigationscontroller**:

    ![](hello-ios-multiscreen-quickstart-images/05new.png "Move the Phoneword scene next to the navigation controller")

6. Legen Sie den **ViewController** als **Stammansichtscontroller** des Navigationscontrollers fest. Drücken Sie die **STRG**-Taste, und klicken Sie in den **Navigationscontroller**. Eine blaue Linie wird nun angezeigt. Ziehen Sie anschließend mit gedrückter **STRG**-Taste vom **Navigationscontroller** zur **Phoneword**-Szene, und lassen Sie los. Diesen Vorgang nennt man _Ziehen mit gedrückter STRG-Taste_:

    ![](hello-ios-multiscreen-quickstart-images/06.png "Drag from the navigation controller to the Phoneword scene and release")

7. Legen Sie im Popover die Beziehung auf **Root** fest:

    ![](hello-ios-multiscreen-quickstart-images/07new.png "Setting the relationship to Root")

    Der **ViewController** ist jetzt als **Stammansichtscontroller des Navigationscontrollers** festgelegt:

    ![](hello-ios-multiscreen-quickstart-images/08.png "The ViewController is now the navigation controllers Root view controller")

8. Doppelklicken Sie auf die **Titel**-Leiste des **Phoneword**-Bildschirms, und ändern Sie den **Titel** in **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/09.png "Change the Title to 'Phoneword'")

9. Ziehen Sie aus der **Toolbox** eine **Schaltfläche**, und platzieren Sie sie unterhalb der **Anruf-Schaltfläche**. Ziehen Sie die Ziehpunkte der neuen **Schaltfläche** auf die gleiche Breite wie die **Anruf-Schaltfläche**:

    ![](hello-ios-multiscreen-quickstart-images/10new.png "Make the new Button the same width as the Call Button")

10. Ändern Sie im **Eigenschaftenpad** den **Namen** der Schaltfläche in **SchaltflächeAnrufliste**, und ändern Sie den **Titel** in **Anrufliste**:

    ![](hello-ios-multiscreen-quickstart-images/11new.png "Change the Name of the Button to CallHistoryButton and change the Title to Call History")

11. Erstellen Sie den Bildschirm **Anrufliste**. Ziehen Sie aus der **Toolbox** einen **Tabellenansichtscontroller** in die Entwurfsoberfläche:

    ![](hello-ios-multiscreen-quickstart-images/12new.png "Drag a table view controller onto the design surface")

12. Klicken Sie anschließend auf die schwarze Leiste am unteren Rand der Szene, um den **Tabellenansichtscontroller** auszuwählen. Ändern Sie im **Eigenschaftenpad** die Klasse des **Tabellenansichtscontrollers** in `CallHistoryController`, und drücken Sie die **EINGABETASTE**:

    ![](hello-ios-multiscreen-quickstart-images/13new.png "Change the table view controllers class to CallHistoryController")

    Der iOS-Designer generiert eine benutzerdefinierte Sicherungsklasse mit dem Namen `CallHistoryController`, um die Hierarchie der Inhaltsansicht auf diesem Bildschirm zu verwalten. Im **Projektmappenpad** wird die Datei **CallHistoryController.cs** angezeigt:

    ![](hello-ios-multiscreen-quickstart-images/14new.png "The CallHistoryController.cs file in the Solution Pad")

13. Doppelklicken Sie auf die Datei **CallHistoryController.cs**, um sie öffnen. Ersetzen Sie den Inhalt durch den folgenden Code:
    
    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Speichern Sie die Anwendung (**⌘+S**), und erstellen Sie sie (**⌘+B**), um sicherzustellen, dass keine Fehler auftreten.

14. Erstellen Sie einen _Segue_ (einen Übergang) zwischen der **Phoneword**-Szene und der **Anrufliste**-Szene.
  Wählen Sie in der **Phoneword-Szene** die **Anrufliste-Schaltfläche** aus, und ziehen Sie mit gedrückter STRG-Taste von der **Schaltfläche** auf die **Anrufliste**-Szene:

    ![](hello-ios-multiscreen-quickstart-images/15.png "Ctrl-drag from the Button to the Call History scene")

    Wählen Sie im Popover **Aktion Segue** die Option **Anzeigen** aus.

    Der iOS-Designer fügt zwischen den beiden Szenen einen Segue hinzu:

    ![](hello-ios-multiscreen-quickstart-images/17new.png "The Segue between the two scenes")

15. Wählen Sie zum Hinzufügen eines **Titels** zum **Tabellenansichtscontroller** die schwarze Leiste am unteren Rand der Szene aus. Ändern Sie im **Eigenschaftenpad** den **Titel des Ansichtscontrollers** in **Anrufliste**:

    ![](hello-ios-multiscreen-quickstart-images/18new.png "Change the view controller title to Call History in the Properties Pad")

16. Beim Ausführen der Anwendung wird mit der **Anrufliste-Schaltfläche** der Bildschirm **Anrufliste** geöffnet. Die Tabellenansicht bleibt jedoch leer, da kein Code vorhanden ist, der nachverfolgt werden kann und der die Telefonnummern anzeigt.

    Diese App speichert die Telefonnummern als Liste von Zeichenfolgen.

    Fügen Sie eine `using`-Anweisung für `System.Collections.Generic` am Anfang von **ViewController** hinzu:

    ```csharp
    using System.Collections.Generic;
    ```

17. Ändern Sie die `ViewController`-Klasse mit dem folgenden Code:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Es gibt hierbei einige Punkte zu beachten:

    - Die Variable `translatedNumber` der `ViewDidLoad`-Methode wurde verschoben und ist nun eine _Variable auf Klassenebene_.
    - Der **CallButton**-Code wurde verändert, um gewählte Rufnummern zur Liste der Telefonnummern hinzuzufügen, indem `PhoneNumbers.Add(translatedNumber)` aufgerufen wird.
    - Die `PrepareForSegue`-Methode wurde hinzugefügt.

    Speichern und kompilieren Sie die Anwendung, um sicherzustellen, dass keine Fehler auftreten.

18. Klicken Sie zum Starten der Anwendung im **iOS-Simulator** auf die Schaltfläche **Starten**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "Press the Start button to launch the application inside the iOS Simulator")

Herzlichen Glückwunsch, Sie haben die Xamarin.iOS-Multiscreen-Anwendung fertiggestellt!

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Exemplarische Vorgehensweise für Windows

In dieser exemplarischen Vorgehensweise wird Ihrer **Phoneword**-Anwendung ein Bildschirm für die Anrufliste hinzugefügt.

1. Öffnen Sie in Visual Studio die **Phoneword**-Anwendung. Bei Bedarf können Sie hier die [vollständige Phoneword-Anwendung](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) aus dem Leitfaden [Exemplarische Vorgehensweise: Hallo, iOS](~/ios/get-started/hello-ios/index.md) herunterladen. Beachten Sie, dass für die Verwendung des iOS-Designers und des iOS-Simulators eine Verbindung mit einem [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) erforderlich ist.

2. Beginnen Sie mit der Bearbeitung der Benutzeroberfläche. Öffnen Sie im **Projektmappen-Explorer** die Datei **Main.storyboard**, und stellen Sie sicher, dass **Anzeigen als** auf _iPhone 6_ festgelegt ist:

    ![](hello-ios-multiscreen-quickstart-images/image1.png "The Main.storyboard in the iOS Designer")

3. Ziehen Sie einen **Navigationscontroller** aus der **Toolbox** auf die Entwurfsoberfläche:

    ![](hello-ios-multiscreen-quickstart-images/image2.png "Drag a navigation controller from the Toolbox onto the design surface")

4. Ziehen Sie zum Ändern des Startpunkts der Anwendung den **Sourceless Segue** (den grauen Pfeil links neben der **Phoneword**-Szene) von der **Phoneword**-Szene zum **Navigationscontroller**:

    ![](hello-ios-multiscreen-quickstart-images/image3.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. Klicken Sie auf die schwarze Leiste, um den **Stammansichtscontroller** auszuwählen. Drücken Sie **ENTFERNEN**, um ihn aus der Entwurfsoberfläche zu entfernen.
  Verschieben Sie anschließend die **Phoneword**-Szene neben den **Navigationscontroller**:

    ![](hello-ios-multiscreen-quickstart-images/image4.png "Move the Phoneword scene next to the navigation controller")

6. Legen Sie den **ViewController** als Stammansichtscontroller des Navigationscontrollers fest. Drücken Sie die **STRG**-Taste, und klicken Sie in den **Navigationscontroller**. Eine blaue Linie wird nun angezeigt. Ziehen Sie anschließend mit gedrückter **STRG**-Taste vom **Navigationscontroller** zur **Phoneword**-Szene, und lassen Sie los. Diesen Vorgang nennt man _Ziehen mit gedrückter STRG-Taste_:

    ![](hello-ios-multiscreen-quickstart-images/image5.png "Drag from the navigation controller to the Phoneword scene and release")

7. Legen Sie im Popover die Beziehung auf **Root** fest:

    ![](hello-ios-multiscreen-quickstart-images/image6.png "Set the relationship to Root")

    Der **ViewController** ist jetzt als **Stammansichtscontroller des Navigationscontrollers** festgelegt:

8. Doppelklicken Sie auf die **Titel**-Leiste des **Phoneword**-Bildschirms, und ändern Sie den **Titel** in **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/image7.png "Change the Title to Phoneword")

9. Ziehen Sie aus der **Toolbox** eine **Schaltfläche**, und platzieren Sie sie unterhalb der **Anruf-Schaltfläche**. Ziehen Sie die Ziehpunkte der neuen **Schaltfläche** auf die gleiche Breite wie die **Anruf-Schaltfläche**:

    ![](hello-ios-multiscreen-quickstart-images/image8.png "Make the new Button the same width as the Call Button")

10. Ändern Sie im **Eigenschaften-Explorer** den **Namen** der **Schaltfläche** in `CallHistoryButton`, und ändern Sie den **Titel** in **Anrufliste**:

    ![](hello-ios-multiscreen-quickstart-images/image9.png "Change the Name of the Button to 'CallHistoryButton' and the Title to 'Call History'")

11. Erstellen Sie den Bildschirm **Anrufliste**. Ziehen Sie aus der **Toolbox** einen **Tabellenansichtscontroller** in die Entwurfsoberfläche:

    ![](hello-ios-multiscreen-quickstart-images/image10.png "Drag a table view controller onto the design surface")

12. Klicken Sie auf die schwarze Leiste am unteren Rand der Szene, um den **Tabellenansichtscontroller** auszuwählen. Ändern Sie im **Eigenschaften-Explorer** die Klasse des **Tabellenansichtscontrollers** in `CallHistoryController`, und drücken Sie die **EINGABETASTE**:

    ![](hello-ios-multiscreen-quickstart-images/image11.png "Change the table view controllers class to CallHistoryController")

    Der iOS-Designer generiert eine benutzerdefinierte Sicherungsklasse mit dem Namen `CallHistoryController`, um die Hierarchie der Inhaltsansicht auf diesem Bildschirm zu verwalten. Im **Projektmappen-Explorer** wird die Datei **CallHistoryController.cs** angezeigt:

    ![](hello-ios-multiscreen-quickstart-images/image12.png "The CallHistoryController.cs file in the Solution Explorer")

13. Doppelklicken Sie auf die Datei **CallHistoryController.cs**, um sie öffnen. Ersetzen Sie den Inhalt durch den folgenden Code:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Speichern und erstellen Sie die Anwendung, um sicherzustellen, dass keine Fehler auftreten. Dabei können Sie eventuelle Buildwarnungen vorerst ignorieren.

14. Erstellen Sie einen _Segue_ (einen Übergang) zwischen der **Phoneword**-Szene und der **Anrufliste**-Szene.
  Wählen Sie in der **Phoneword-Szene** die **Anrufliste-Schaltfläche** aus, und ziehen Sie **mit gedrückter STRG-Taste** von der **Schaltfläche** auf die **Anrufliste**-Szene:

    ![](hello-ios-multiscreen-quickstart-images/image13.png "Ctrl-drag from the Button to the Call History scene")

    Wählen Sie im Popover **Aktion Segue** die Option **Anzeigen** aus:

    ![](hello-ios-multiscreen-quickstart-images/image14.png "Select Show as the segue type")

    Der iOS-Designer fügt zwischen den beiden Szenen einen Segue hinzu:

    ![](hello-ios-multiscreen-quickstart-images/image15.png "The Segue between the two scenes")

15. Wählen Sie zum Hinzufügen eines **Titels** zum **Tabellenansichtscontroller** die schwarze Leiste am unteren Rand der Szene aus. Ändern Sie im **Eigenschaften-Explorer** die Option **Ansichtscontroller > Titel** in **Anrufliste**:

    ![](hello-ios-multiscreen-quickstart-images/image16.png "Change the view controller Title to Call History")

16. Beim Ausführen der Anwendung wird mit der **Anrufliste-Schaltfläche** der Bildschirm **Anrufliste** geöffnet. Die Tabellenansicht bleibt jedoch leer, da kein Code vorhanden ist, der nachverfolgt werden kann und der die Telefonnummern anzeigt.

    Diese App speichert die Telefonnummern als Liste von Zeichenfolgen.

    Fügen Sie eine `using`-Anweisung für `System.Collections.Generic` am Anfang von **ViewController** hinzu:

    ```csharp
    using System.Collections.Generic;
    ```

17. Ändern Sie die `ViewController`-Klasse mit dem folgenden Code:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Es gibt hierbei einige Punkte zu beachten.
    - Die Variable `translatedNumber` von der `ViewDidLoad`-Methode wurde zu einer _Variable auf Klassenebene_ verschoben.
    - Der **CallButton**-Code wurde verändert, um gewählte Rufnummern zur Liste der Telefonnummern hinzuzufügen, indem `PhoneNumbers.Add(translatedNumber)` aufgerufen wird.
    - Die `PrepareForSegue`-Methode wurde hinzugefügt.

    Speichern und kompilieren Sie die Anwendung, um sicherzustellen, dass keine Fehler auftreten.

    Speichern und kompilieren Sie die Anwendung, um sicherzustellen, dass keine Fehler auftreten.

18. Klicken Sie zum Starten der Anwendung im **iOS-Simulator** auf die Schaltfläche **Starten**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "The first screen of the sample app")

Herzlichen Glückwunsch, Sie haben die Xamarin.iOS-Multiscreen-Anwendung fertiggestellt!

::: zone-end

Die App kann die Navigation jetzt sowohl mithilfe von Storyboard-Segues als auch im Code behandeln. Erfahren Sie mehr über die Tools und Fähigkeiten, die Sie in diesem Leitfaden kennengelernt haben, in den [Ausführlichen Erläuterungen zu Hallo, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).

## <a name="related-links"></a>Verwandte Links

- [Hallo, iOS (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [iOS-Eingaberichtlinien](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS-Bereitstellungsportal](https://developer.apple.com/ios/manage/overview/index.action)
