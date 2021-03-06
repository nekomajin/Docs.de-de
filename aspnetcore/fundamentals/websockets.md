---
title: WebSockets-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/15/2018
uid: fundamentals/websockets
ms.openlocfilehash: ee529f1aaadb6b6062bed56003c51f161eae7e72
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36273796"
---
# <a name="websockets-support-in-aspnet-core"></a>WebSockets-Unterstützung in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra) und [Andrew Stanton-Nurse](https://github.com/anurse)

In diesem Artikel erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen. Bei [WebSockets](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) handelt es sich um ein Protokoll, das bidirektionale persistente Kommunikationskanäle über TCP-Verbindungen ermöglicht. Es wird in Apps verwendet, die von schneller Kommunikation in Echtzeit profitieren, z.B. Chats, Dashboards und Spiele-Apps.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample)). Weitere Informationen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## <a name="prerequisites"></a>Erforderliche Komponenten

* ASP.NET Core 1.1 oder höher
* Jedes Betriebssystem, das ASP.NET Core unterstützt:
  
  * Windows 7/Windows Server 2008 und höher
  * Linux
  * macOS
  
* Wenn die App unter Windows mit IIS ausgeführt wird:

  * Windows 8/Windows Server 2012 und höher
  * IIS 8/IIS 8 Express
  * WebSockets müssen in IIS aktiviert sein (weitere Informationen finden Sie im Abschnitt [Unterstützung für IIS und IIS Express](#iisiis-express-support)).
  
* Wenn die App unter [HTTP.sys](xref:fundamentals/servers/httpsys) ausgeführt wird:

  * Windows 8/Windows Server 2012 und höher

* Weitere Informationen zu unterstützten Browsern finden Sie unter https://caniuse.com/#feat=websockets.

## <a name="when-to-use-websockets"></a>Verwendungszweck von WebSockets

Verwenden Sie WebSockets, um direkt mit einer Socketverbindung zu arbeiten. Verwenden Sie WebSockets beispielsweise für die bestmögliche Leistung bei einem Echtzeitspiel.

[ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) stellt ein erweitertes App-Modell für Echtzeitfunktionen bereit, aber es kann nur in ASP.NET 4.x und nicht in ASP.NET Core ausgeführt werden. Eine ASP.NET Core-Version von SignalR ist für das Release von ASP.NET Core 2.1 geplant. Weitere Informationen finden Sie unter [ASP.NET Core 2.1 high-level planning (Grundlegende Planung von ASP.NET Core 2.1)](https://github.com/aspnet/Announcements/issues/288).

Vor dem Release von SignalR Core können WebSockets verwendet werden. Die Features von SignalR müssen jedoch vom Entwickler bereitgestellt und unterstützt werden. Zum Beispiel:

* Unterstützung für eine größere Zahl an unterschiedlichen Browserversionen, indem Sie ein automatisches Fallback auf alternative Transportmethoden einsetzen
* Der automatische Aufbau einer neuen Verbindung, wenn die Verbindung unterbrochen wurde
* Unterstützung für das Aufrufen von Methoden auf dem Server durch Clients bzw. andersherum
* Unterstützung für die Skalierung auf mehrere Server

## <a name="how-to-use-it"></a>Verwendungsweise

* Installieren Sie das Paket [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).
* Konfigurieren Sie die Middleware.
* Akzeptieren Sie Anforderungen von WebSocket.
* Senden Sie Nachrichten, und empfangen Sie diese.

### <a name="configure-the-middleware"></a>Konfigurieren der Middleware

Fügen Sie die WebSockets-Middleware zur `Configure`-Methode der `Startup`-Klasse hinzu.

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSockets)]

Sie können die folgenden Einstellungen konfigurieren:

* `KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten
* `ReceiveBufferSize`: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen.

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSocketsOptions)]

### <a name="accept-websocket-requests"></a>Akzeptieren der Anforderungen von WebSocket

Prüfen Sie zu einem späteren Zeitpunkt im Lebenszyklus einer Anforderung (z.B. später in der `Configure`-Methode oder in einer MVC-Aktion), ob es sich um eine WebSocket-Anforderung handelt, und akzeptieren Sie diese.

Das folgende Beispiel ist ein späterer Auszug der `Configure`-Methode.

[!code-csharp[](websockets/sample/Startup.cs?name=AcceptWebSocket&highlight=7)]

WebSocket-Anforderungen können bei jeder URL eingehen. Dieser Beispielcode akzeptiert jedoch nur Anforderungen für `/ws`.

### <a name="send-and-receive-messages"></a>Senden und Empfangen von Nachrichten

Die `AcceptWebSocketAsync`-Methode ändert die TCP-Verbindung in eine WebSocket-Verbindung und stellt ein [WebSocket](/dotnet/core/api/system.net.websockets.websocket)-Objekt bereit. Verwenden Sie das `WebSocket`-Objekt, um Nachrichten zu senden und zu empfangen.

Der weiter oben gezeigte Code, der WebSocket-Anforderungen akzeptiert, übergibt das `WebSocket`-Objekt an eine `Echo`-Methode. Der Code empfängt eine Nachricht und sendet diese umgehend wieder zurück. Nachrichten werden in einer Schleife gesendet und empfangen, bis der Client die Verbindung schließt:

[!code-csharp[](websockets/sample/Startup.cs?name=Echo)]

Wenn Sie die WebSocket-Verbindung vor Beginn der Schleife akzeptieren, endet die Middlewarepipeline. Wenn Sie das Socket schließen, wird die Pipeline entladen. Das bedeutet, dass sich die Anforderung in der Pipeline nicht mehr weiter bewegt, wenn der WebSocket akzeptiert wird. Wenn die Schleife beendet und der Socket geschlossen ist, wird die Anforderung in der Pipeline weiter verarbeitet.

## <a name="iisiis-express-support"></a>Unterstützung für IIS und IIS Express

In Windows Server 2012 oder höher und in Windows 8 oder höher mit IIS 8 oder IIS Express 8 oder höher ist die Unterstützung für das WebSocket-Protokoll enthalten.

So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows Server 2012 oder höher:

1. Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**.
1. Klicken Sie auf **Rollenbasierte oder featurebasierte Installation**. Klicken Sie auf **Weiter**.
1. Wählen Sie den entsprechenden Server aus (standardmäßig ist der lokale Server ausgewählt). Klicken Sie auf **Weiter**.
1. Erweitern Sie **Webserver (IIS)** in der Struktur **Rollen**, und erweitern Sie **Webserver** und anschließend **Anwendungsentwicklung**.
1. Wählen Sie **WebSocket-Protokoll** aus. Klicken Sie auf **Weiter**.
1. Wenn keine zusätzlichen Features erforderlich sind, klicken Sie auf **Weiter**.
1. Klicken Sie auf **Installieren**.
1. Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**, um den Assistenten zu beenden.

So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows 8 oder höher:

1. Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).
1. Öffnen Sie folgende Knoten: **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.
1. Wählen Sie das Feature **WebSocket-Protokoll** aus. Klicken Sie auf **OK**.

**Deaktivieren von WebSocket bei Verwendung von „socket.io“ in „Node.js“**

Wenn Sie die WebSocket-Unterstützung in [socket.io](https://socket.io/) in [Node.js](https://nodejs.org/) verwenden, deaktivieren Sie das IIS-WebSocket-Standardmodul mithilfe des `webSocket`-Elements in *web.config* oder *applicationHost.config*. Wenn dieser Schritt nicht durchgeführt wird, versucht das IIS-WebSocket-Modul, die WebSocket-Kommunikation statt Node.js und der App zu verarbeiten.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a>Nächste Schritte

Die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample), die in diesem Artikel verwendet wird, ist eine Echo-App. Sie verfügt über eine Webseite, die WebSocket-Verbindungen herstellt. Der Server schickt alle empfangenen Nachrichten zurück an den Client. Führen Sie die App über eine Eingabeaufforderung aus (sie ist nicht darauf ausgelegt, von Visual Studio mit IIS Express ausgeführt zu werden), und navigieren Sie zu http://localhost:5000. Die Webseite zeigt den Verbindungsstatus in der oberen linken Ecke an:

![Erster Zustand der Webseite](websockets/_static/start.png)

Klicken Sie auf **Connect** (Verbinden), um eine WebSocket-Anforderung an die gezeigte URL zu senden. Geben Sie einen Testtext ein, und klicken Sie auf **Send** (Senden). Wenn dies abgeschlossen ist, klicken Sie auf **Close Socket** (Socket schließen). Der Abschnitt **Kommunikationsprotokoll** meldet jede open-, send- und close-Aktion, wenn diese durchgeführt wird.

![Erster Zustand der Webseite](websockets/_static/end.png)
