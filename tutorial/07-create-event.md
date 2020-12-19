<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt können Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzufügen.

## <a name="create-the-new-event-tab"></a>Erstellen der neuen Ereignisregisterkarte

1. Erstellen Sie eine neue Datei im **./Pages** -Verzeichnis mit dem Namen "New **Event. cshtml** ", und fügen Sie den folgenden Code hinzu.

    :::code language="razor" source="../demo/GraphTutorial/Pages/NewEvent.cshtml":::

    Dadurch wird ein einfaches Formular implementiert und JavaScript hinzugefügt, um die Formulardaten an die WebAPI zu senden.

## <a name="implement-the-web-api"></a>Implementieren der Webdienst-API

1. Erstellen Sie ein neues Verzeichnis mit dem Namen " **Models** " im Stamm des Projekts.

1. Erstellen Sie eine neue Datei im **./Models** -Verzeichnis mit dem Namen **NewEvent.cs** , und fügen Sie den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

1. Öffnen Sie **/Controllers/CalendarController.cs** , und fügen Sie die folgende `using` Anweisung am Anfang der Datei hinzu.

    ```csharp
    using GraphTutorial.Models;
    ```

1. Fügen Sie der **CalendarController** -Klasse die folgende Funktion hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="PostSnippet":::

    Dadurch kann ein HTTP-Beitrag an die WebAPI mit den Feldern des Formulars gesendet werden.

1. Speichern Sie alle Änderungen, und starten Sie die Anwendung neu. Aktualisieren Sie die app in Microsoft Teams, und wählen Sie die Registerkarte **Ereignis erstellen** aus. Füllen Sie das Formular aus, und wählen Sie **Erstellen** aus, um dem Kalender des Benutzers ein Ereignis hinzuzufügen.

    ![Screenshot der Registerkarte "Ereignis erstellen"](images/create-event.png)
