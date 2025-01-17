---
title: Binden einer Java-Bibliothek
description: Die Android-Community verfügt über viele Java-Bibliotheken, die Sie möglicherweise in Ihrer APP verwenden möchten. in diesem Handbuch wird erläutert, wie Sie Java-Bibliotheken in Ihre xamarin. Android-Anwendung integrieren, indem Sie eine Bindungs Bibliothek erstellen.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027604"
---
# <a name="binding-a-java-library"></a>Binden einer Java-Bibliothek

_Die Android-Community verfügt über viele Java-Bibliotheken, die Sie möglicherweise in Ihrer APP verwenden möchten. in diesem Handbuch wird erläutert, wie Sie Java-Bibliotheken in Ihre xamarin. Android-Anwendung integrieren, indem Sie eine Bindungs Bibliothek erstellen._

## <a name="overview"></a>Übersicht

Das Drittanbieter-Bibliotheks Ökosystem für Android ist enorm. Aus diesem Grund ist es häufig sinnvoll, eine vorhandene Android-Bibliothek zu verwenden, als eine neue zu erstellen. Xamarin. Android bietet zwei Möglichkeiten, diese Bibliotheken zu verwenden:

- Erstellen Sie eine *Bindungs Bibliothek* , die die Bibliothek automatisch C# mit Wrappern umschließt, damit Sie C# Java-Code über Aufrufe aufrufen können.

- Verwenden Sie die *Java Native Interface* (*jni*), um Aufrufe im Java-Bibliotheks Code direkt aufzurufen. Jni ist ein Programmier Framework, mit dem Java-Code aufrufen und von nativen Anwendungen oder Bibliotheken aufgerufen werden kann.

In diesem Handbuch wird die erste Option erläutert: Erstellen einer *Bindungs Bibliothek* , die eine oder mehrere vorhandene Java-Bibliotheken in eine Assembly umschließt, die Sie in Ihrer Anwendung verknüpfen können. Weitere Informationen zur Verwendung von jni finden Sie unter [Arbeiten mit jni](~/android/platform/java-integration/working-with-jni.md).

Xamarin. Android implementiert Bindungen mit *verwalteten Callable Wrapper* (*MCW*). MCW ist eine jni-Bridge, die verwendet wird, wenn verwalteter Code Java-Code aufrufen muss. Verwaltete Aufruf Bare Wrapper bieten außerdem Unterstützung für die Unterklassen von Java-Typen und zum Überschreiben virtueller Methoden in Java-Typen. Ebenso, wenn Android Runtime (Art)-Code verwalteten Code aufrufen möchte, erfolgt dies über eine andere jni-Bridge, die als Android Callable Wrapper (ACW) bezeichnet wird. Diese [Architektur](~/android/internals/architecture.md) wird im folgenden Diagramm veranschaulicht:

[![Android jni Bridge-Architektur](images/architecture.png)](images/architecture.png#lightbox)

Eine Bindungs Bibliothek ist eine Assembly, die verwaltete Aufruf Bare Wrapper für Java-Typen enthält. Hier sehen Sie beispielsweise einen Java-Typ, `MyClass`, den Sie in einer Bindungs Bibliothek einschließen möchten:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Nachdem wir eine Bindungs Bibliothek für die **jar** -Datei erstellt haben, die `MyClass`enthält, können wir diese instanziieren und Methoden für C#Sie von folgenden Methoden abrufen:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Um diese Bindungs Bibliothek zu erstellen, verwenden Sie die Vorlage xamarin. Android *Java-Bindungs Bibliothek* . Das resultierende Bindungs Projekt erstellt eine .NET-Assembly mit den MCW-Klassen, **jar** -Dateien und Ressourcen für Android-Bibliotheks Projekte, die darin eingebettet sind. Sie können auch Bindungs Bibliotheken für Android Archive erstellen (. Aar) Dateien und Eclipse-Android-Bibliotheks Projekte. Wenn Sie auf die resultierende Bindungs Bibliotheks-dll-Assembly verweisen, können Sie eine vorhandene Java-Bibliothek in Ihrem xamarin. Android-Projekt wieder verwenden.

Wenn Sie auf Typen in der Bindungs Bibliothek verweisen, müssen Sie den Namespace der Bindungs Bibliothek verwenden. In der Regel fügen Sie am Anfang Ihrer C# Quelldateien eine `using`-Direktive hinzu, die die .NET-Namespace Version des Java-Paket namens ist. Wenn z **. b.** der Java-Paketname für die gebundene jar-Datenbankname lautet:

```csharp
com.company.package
```

Dann fügen Sie die folgende `using`-Anweisung am Anfang der C# Quelldateien ein, um auf Typen in der gebundenen **jar** -Datei zuzugreifen:

```csharp
using Com.Company.Package;
```

Beim Binden einer vorhandenen Android-Bibliothek müssen folgende Punkte berücksichtigt werden:

- **Gibt es externe Abhängigkeiten für die Bibliothek?** &ndash; Java-Abhängigkeiten, die für die Android-Bibliothek erforderlich sind, müssen im xamarin. Android-Projekt als **referencejar** oder als **embeddedreferencejar**enthalten sein. Alle systemeigenen Assemblys müssen dem Bindungs Projekt als **embeddednativelibrary**hinzugefügt werden.  

- **Welche Version der Android-API führt das Ziel der Android-Bibliothek aus?** &ndash; das "herabstufen" der Android-API-Ebene ist nicht möglich. Stellen Sie sicher, dass das xamarin. Android-Bindungs Projekt auf dieselbe API-Ebene (oder höher) wie die Android-Bibliothek abzielt.

- **Welche Version des JDK wurde verwendet, um die Bibliothek zu kompilieren?** &ndash; Bindungs Fehler können auftreten, wenn die Android-Bibliothek mit einer anderen Version von JDK erstellt wurde, als von xamarin. Android verwendet wird. Wenn möglich, kompilieren Sie die Android-Bibliothek mit derselben Version des JDK neu, das von Ihrer Installation von xamarin. Android verwendet wird.

## <a name="build-actions"></a>Buildvorgänge

Wenn Sie eine Bindungs Bibliothek erstellen, legen Sie *Buildaktionen* für die **jar** -Datei oder fest. AAR-Dateien, die Sie in Ihr Bindungs Bibliotheksprojekt integrieren &ndash; jede Buildaktion bestimmt, wie die **jar** -Datei oder. Die Aar-Datei wird in Ihre Bindungs Bibliothek eingebettet (oder von ihr referenziert). In der folgenden Liste sind diese Buildaktionen zusammengefasst:

- `EmbeddedJar` &ndash; bettet die **jar** -Bibliothek in die resultierende Bindungs Bibliotheks-DLL als eingebettete Ressource ein. Dies ist die einfachste und am häufigsten verwendete Buildaktion. Verwenden Sie diese Option, wenn Sie möchten, dass die **jar** -Datei automatisch in Bytecode kompiliert und in die Bindungs Bibliothek gepackt wird.

- in `InputJar` &ndash; wird die **jar** -Bibliothek nicht in die resultierende Bindungs Bibliothek eingebettet. DLL. Ihre Bindungs Bibliothek. Die dll weist zur Laufzeit eine Abhängigkeit von dieser **jar** -Datei auf. Verwenden Sie diese Option, wenn Sie die **jar** -Bibliothek nicht in die Bindungs Bibliothek einschließen möchten (z. b. aus Lizenzierungs Gründen). Wenn Sie diese Option verwenden, müssen Sie sicherstellen, dass die Eingabe **. jar** auf dem Gerät verfügbar ist, das Ihre APP ausführt.

- `LibraryProjectZip` &ndash; bettet ein ein. AAR-Datei in die resultierende Bindungs Bibliothek. DLL. Dies ist vergleichbar mit embeddedjar, mit dem Unterschied, dass Sie auf Ressourcen (und Code) in der gebundenen zugreifen können. AAR-Datei. Verwenden Sie diese Option, wenn Sie einen einbetten möchten. Aar in Ihre Bindungs Bibliothek.

- `ReferenceJar` &ndash; gibt eine "Reference. **jar**"-Angabe an: "Reference **. jar** " ist eine **. jar** - **-oder-Bindung.** AAR-Dateien sind abhängig von. This Reference **. jar** wird nur zum erfüllen der Abhängigkeiten der Kompilierzeit verwendet. Wenn Sie diese Buildaktion verwenden C# , werden keine Bindungen für "Reference **. jar** " erstellt und nicht in die resultierende Bindungs Bibliothek eingebettet. DLL. Verwenden Sie diese Option, wenn Sie eine Bindungs Bibliothek für den Verweis **. jar** erstellen, dies jedoch noch nicht getan haben. Diese Buildaktion ist hilfreich beim Verpacken mehrerer **jar**-s (und/oder). AARS) in mehrere abhängige Bindungs Bibliotheken.

- `EmbeddedReferenceJar` &ndash; ein Reference **. jar** in die resultierende Bindungs Bibliothek einbettet. DLL. Verwenden Sie diese Buildaktion, wenn Sie C# Bindungen für die Eingabe **. jar** -Datei (oder) erstellen möchten. Aar) und alle zugehörigen Reference **. jar**-(e) in der Bindungs Bibliothek.

- `EmbeddedNativeLibrary` &ndash; bettet eine native **. so** in die Bindung ein. Diese Buildaktion wird für **.** -Dateien verwendet, die von der **jar** -Datei benötigt werden, die gebunden wird. Es kann erforderlich sein, die **. so** -Bibliothek vor dem Ausführen von Code aus der Java-Bibliothek manuell zu laden. Dies wird nachfolgend beschrieben.

Diese Buildaktionen werden in den folgenden Leitfäden ausführlicher erläutert.

Außerdem werden die folgenden Buildaktionen verwendet, um die Java-API-Dokumentation zu importieren C# und in die XML-Dokumentation zu konvertieren:

- `JavaDocJar` wird verwendet, um auf Javadoc Archive JAR für eine Java-Bibliothek zu verweisen, die einem Maven-Paketstil (in der Regel `FOOBAR-javadoc**.jar**`) entspricht.
- `JavaDocIndex` wird verwendet, um in der API-Referenz Dokumentation HTML auf `index.html` Datei zu verweisen.
- `JavaSourceJar` wird zum ergänzen `JavaDocJar`verwendet, um zunächst Javadoc aus Quellen zu generieren und die Ergebnisse dann als `JavaDocIndex`zu behandeln, für eine Java-Bibliothek, die einem Maven-Paketstil entspricht (normalerweise `FOOBAR-sources**.jar**`).

Die API-Dokumentation sollte das standardmäßige Doclet aus Java8, Java7 oder java6 SDK (alle unterschiedlichen Format) oder den droiddoc-Stil sein.

## <a name="including-a-native-library-in-a-binding"></a>Einschließen einer nativen Bibliothek in eine Bindung

Es kann erforderlich sein, eine **. so** -Bibliothek in ein xamarin. Android-Bindungs Projekt als Teil der Bindung einer Java-Bibliothek einzuschließen. Wenn der umschließende Java-Code ausgeführt wird, kann xamarin. Android den jni-Befehl nicht ausführen, und die Fehlermeldung _java. lang. unbefriefiedlinkerror: Native Methode wurde nicht gefunden:_ wird in der logcat out für die Anwendung angezeigt.

Die Behebung hierfür besteht darin, die **. so** -Bibliothek mit einem `Java.Lang.JavaSystem.LoadLibrary`-Aufrufvorgang manuell zu laden. Angenommen, ein xamarin. Android-Projekt hat eine freigegebene Bibliothek libpocketsphinx_jni, die im Bindungs Projekt mit einer Buildaktion von **embeddednativelibrary**enthalten ist, den folgenden Code Ausschnitt (vor der Verwendung der freigegebenen Bibliothek ausgeführt) **.** lädt die **. so** -Bibliothek:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Anpassen von Java-APIs an C-&eparsl;

Der xamarin. Android-Bindungs Generator ändert einige Java-Ausdrücke und-Muster entsprechend den .net-Mustern. In der folgenden Liste wird beschrieben, C#wie Java/.NET zugeordnet wird:

- _Setter/Getter-Methoden_ in Java sind _Eigenschaften_ in .net.

- _Felder_ in Java sind _Eigenschaften_ in .net.

- _Listener/Listenerschnittstellen_ in Java sind _Ereignisse_ in .net. Die Parameter der Methoden in den Rückruf Schnittstellen werden durch eine `EventArgs` Unterklasse dargestellt.

- Eine statische, in Java _erbt Klasse_ ist eine in .net _erbt_ .

- Eine _innere Klasse_ in Java ist eine geschachtelte _Klasse_ mit einem Instanzkonstruktor in C#.

## <a name="binding-scenarios"></a>Bindungs Szenarien

Die folgenden Bindungs szenariohandbücher können Ihnen helfen, eine Java-Bibliothek (oder Bibliotheken) für die Einbindung in Ihre APP zu binden:

- [Binden einer. JAR](~/android/platform/binding-java-library/binding-a-jar.md) ist eine exemplarische Vorgehensweise zum Erstellen von Bindungs Bibliotheken für **jar** -Dateien.

- [Binden einer. AAR](~/android/platform/binding-java-library/binding-an-aar.md) ist eine exemplarische Vorgehensweise zum Erstellen von Bindungs Bibliotheken für. AAR-Dateien. Lesen Sie diese exemplarische Vorgehensweise, um zu erfahren, wie Android Studio Bibliotheken gebunden werden.

- [Die Bindung eines Eclipse-Bibliotheks Projekts](~/android/platform/binding-java-library/binding-a-library-project.md) ist eine exemplarische Vorgehensweise zum Erstellen von Bindungs Bibliotheken aus Android-Bibliotheks Projekten. Lesen Sie diese exemplarische Vorgehensweise, um zu erfahren, wie Sie Eclipse-Android-Bibliotheks Projekte

- [Anpassen von Bindungen](~/android/platform/binding-java-library/customizing-bindings/index.md) erläutert, wie manuelle Änderungen an der Bindung vorgenommen werden, um Buildfehler aufzulösen und die resultierende API so zu strukturieren,C#dass Sie eher "-like" ist.

- [Problem](~/android/platform/binding-java-library/troubleshooting-bindings.md) Behandlung bei Bindungen werden häufige Bindungs Fehler Szenarios aufgelistet, mögliche Ursachen erläutert und Vorschläge zum Beheben dieser Fehler angezeigt.

## <a name="related-links"></a>Verwandte Links

- [Arbeiten mit jni](~/android/platform/java-integration/working-with-jni.md)
- [Gapi-Metadaten](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Verwenden nativer Bibliotheken](~/android/platform/native-libraries.md)
