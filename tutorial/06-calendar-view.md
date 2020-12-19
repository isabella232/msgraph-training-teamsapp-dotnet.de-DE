<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt werden Sie Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.

# <a name="get-a-calendar-view"></a>Abrufen einer Kalenderansicht

Eine Kalenderansicht ist eine Gruppe von Ereignissen aus dem Kalender des Benutzers, die zwischen zwei Zeitpunkten auftreten. Verwenden Sie diese, um die Ereignisse des Benutzers für die aktuelle Woche abzurufen.

1. Öffnen Sie **/Controllers/CalendarController.cs** , und fügen Sie die folgende Funktion zur **CalendarController** -Klasse hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetStartOfWeekSnippet":::

1. Fügen Sie die folgende Funktion hinzu, um von Microsoft Graph-anrufen zurückgegebene Ausnahmen zu behandeln.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="HandleGraphExceptionSnippet":::

1. Ersetzen Sie die vorhandene `Get`-Funktion durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetSnippet" highlight="2,14-57":::

    Überprüfen Sie die Änderungen. Diese neue Version der Funktion:

    - Gibt `IEnumerable<Event>` anstelle von zurück `string` .
    - Ruft die Postfacheinstellungen des Benutzers mithilfe von Microsoft Graph ab.
    - Verwendet die Zeitzone des Benutzers, um den Anfang und das Ende der aktuellen Woche zu berechnen.
    - Ruft eine Kalenderansicht ab.
        - Verwendet die `.Header()` -Funktion, um eine Kopfzeile einzuschließen `Prefer: outlook.timezone` , wodurch die Anfangs-und Endzeiten der zurückgegebenen Ereignisse in die Zeitzone des Benutzers konvertiert werden.
        - Verwendet die `.Top()` -Funktion, um höchstens 50 Ereignisse anzufordern.
        - Verwendet die `.Select()` -Funktion, um nur die von der APP verwendeten Felder anzufordern.
        - Verwendet die `OrderBy()` -Funktion, um die Ergebnisse nach dem Startzeitpunkt zu sortieren.

1. Speichern Sie die Änderungen, und starten Sie die App neu. Aktualisieren Sie die Registerkarte in Microsoft Teams. Die APP zeigt eine JSON-Auflistung der Ereignisse an.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie die Liste der Ereignisse auf eine benutzerfreundlichere Weise anzeigen.

1. Öffnen Sie **/pages/index.cshtml** , und fügen Sie die folgenden Funktionen innerhalb des- `<script>` Tags hinzu.

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderHelpersSnippet":::

1. Ersetzen Sie die vorhandene `renderCalendar`-Funktion durch Folgendes.

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="RenderCalendarSnippet":::

1. Speichern Sie die Änderungen, und starten Sie die App neu. Aktualisieren Sie die Registerkarte in Microsoft Teams. Die APP zeigt Ereignisse im Kalender des Benutzers an.

    ![Ein Screenshot der APP, die den Kalender des Benutzers anzeigt](images/calendar-view.png)
