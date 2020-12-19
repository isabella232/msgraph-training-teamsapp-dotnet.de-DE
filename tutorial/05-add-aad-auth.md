<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit einmaligem Anmelden mit Azure AD zu unterstützen. Dies ist notwendig, um das erforderliche OAuth-Zugriffstoken zum Aufruf der Microsoft Graph-API abzurufen. In diesem Schritt wird die [Microsoft. Identity.](https://www.nuget.org/packages/Microsoft.Identity.Web/) webbibliothek konfiguriert.

> [!IMPORTANT]
> Um zu vermeiden, dass die Anwendungs-ID und der geheime Schlüssel in der Quelle gespeichert werden, verwenden Sie den [.net Secret Manager](/aspnet/core/security/app-secrets) zum Speichern dieser Werte. Der geheime Manager dient nur Entwicklungszwecken, für Produktions-apps sollte ein vertrauenswürdiger geheimer geheim Manager zum Speichern von Geheimnissen verwendet werden.

1. Öffnen Sie **./appsettings.jsauf** , und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="json" source="../demo/GraphTutorial/appsettings.example.json" highlight="2-8":::

1. Öffnen Sie die CLI in dem Verzeichnis, in dem sich **GraphTutorial. csproj** befindet, und führen Sie die folgenden Befehle aus, indem `YOUR_APP_ID` Sie Ihre Anwendungs-ID aus dem Azure-Portal und den `YOUR_APP_SECRET` geheimen Anwendungsschlüssel ersetzen.

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Implementieren Sie zunächst einmaliges Anmelden im JavaScript-Code der app. Sie verwenden das [Microsoft Teams JavaScript SDK](/javascript/api/overview/msteams-client) , um ein Zugriffstoken zu erhalten, mit dem der JavaScript-Code im Teams-Client AJAX-Aufrufe an die WebAPI ausführt, die Sie später implementieren können.

1. Öffnen Sie **/pages/index.cshtml** , und fügen Sie den folgenden Code innerhalb des- `<script>` Tags hinzu.

    ```javascript
    (function () {
      if (microsoftTeams) {
        microsoftTeams.initialize();

        microsoftTeams.authentication.getAuthToken({
          successCallback: (token) => {
            // TEMPORARY: Display the access token for debugging
            $('#tab-container').empty();

            $('<code/>', {
              text: token,
              style: 'word-break: break-all;'
            }).appendTo('#tab-container');
          },
          failureCallback: (error) => {
            renderError(error);
          }
        });
      }
    })();

    function renderError(error) {
      $('#tab-container').empty();

      $('<h1/>', {
        text: 'Error'
      }).appendTo('#tab-container');

      $('<code/>', {
        text: JSON.stringify(error, Object.getOwnPropertyNames(error)),
        style: 'word-break: break-all;'
      }).appendTo('#tab-container');
    }
    ```

    Dadurch wird die `microsoftTeams.authentication.getAuthToken` automatische Authentifizierung als der Benutzer aufgerufen, der bei Microsoft Teams angemeldet ist. Normalerweise sind keine Benutzeroberflächen Ansagen beteiligt, es sei denn, der Benutzer muss zustimmen. Der Code zeigt dann das Token auf der Registerkarte an.

1. Speichern Sie Ihre Änderungen, und starten Sie die Anwendung, indem Sie den folgenden Befehl in ihrer CLI ausführen.

    ```Shell
    dotnet run
    ```

    > [!IMPORTANT]
    > Wenn Sie ngrok neu gestartet haben und die ngrok-URL geändert wurde, müssen Sie den ngrok-Wert **vor** dem Testen an der folgenden Stelle aktualisieren.
    >
    > - Der Umleitungs-URI in Ihrer APP-Registrierung
    > - Der Anwendungs-ID-URI in Ihrer APP-Registrierung
    > - `contentUrl` in manifest.jsauf
    > - `validDomains` in manifest.jsauf
    > - `resource` in manifest.jsauf

1. Erstellen Sie eine ZIP-Datei mit **manifest.json**, **color.png** und **outline.png**.

1. Wählen Sie in Microsoft Teams in der linken Leiste **apps** aus, wählen Sie **benutzerdefinierte App hochladen** aus, und wählen Sie dann **für mich oder meine Teams hochladen** aus.

    ![Screenshot des Links zum Hochladen eines benutzerdefinierten app in Microsoft Teams](images/upload-custom-app.png)

1. Wechseln Sie zu der zuvor erstellten ZIP-Datei, und wählen Sie **Öffnen** aus.

1. Überprüfen Sie die Anwendungsinformationen, und wählen Sie **Hinzufügen** aus.

1. Die Anwendung wird in Microsoft Teams geöffnet und zeigt ein Zugriffstoken an.

Wenn Sie das Token kopieren, können Sie es in [JWT.ms](https://jwt.ms)einfügen. Stellen Sie sicher, dass die Benutzergruppe (der `aud` Anspruch) Ihre Anwendungs-ID ist und der einzige Bereich (der `scp` Anspruch) der von `access_as_user` Ihnen erstellte API-Bereich ist. Das bedeutet, dass dieses Token keinen direkten Zugriff auf Microsoft Graph gewährt! Stattdessen muss die WebAPI, die Sie in Kürze implementieren, dieses Token mithilfe des [im-Auftrag-des-Flusses](/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) austauschen, um ein Token zu erhalten, das mit Microsoft Graph-Aufrufen verwendet werden kann.

## <a name="configure-authentication-in-the-aspnet-core-app"></a>Konfigurieren der Authentifizierung in der ASP.net-Kern-App

Beginnen Sie mit dem Hinzufügen der Microsoft Identity Platform-Dienste zur Anwendung.

1. Öffnen Sie die Datei **./Startup.cs** , und fügen Sie die folgende `using` Anweisung am Anfang der Datei hinzu.

    ```csharp
    using Microsoft.Identity.Web;
    ```

1. Fügen Sie die folgende gerade vor der `app.UseAuthorization();` `Configure` -Funktion hinzu.

    ```csharp
    app.UseAuthentication();
    ```

1. Fügen Sie die folgende gerade hinter der `endpoints.MapRazorPages();` in der `Configure` -Funktion aufgeführten.

    ```csharp
    endpoints.MapControllers();
    ```

1. Ersetzen Sie die vorhandene `ConfigureServices`-Funktion durch Folgendes.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="ConfigureServicesSnippet":::

    Dieser Code konfiguriert die Anwendung so, dass Aufrufe von webapis basierend auf dem JWT-Träger Token in der Kopfzeile authentifiziert werden können `Authorization` . Außerdem werden die Token-Akquisitions Dienste hinzugefügt, die dieses Token über den im-Auftrag-von-Fluss austauschen können.

## <a name="create-the-web-api-controller"></a>Erstellen des WebAPI-Controllers

1. Erstellen Sie ein neues Verzeichnis im Stamm des Projekts mit dem Namen **Controller**.

1. Erstellen Sie eine neue Datei im **./Controllers** -Verzeichnis mit dem Namen **CalendarController.cs** , und fügen Sie den folgenden Code hinzu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.Resource;
    using Microsoft.Graph;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        [ApiController]
        [Route("[controller]")]
        [Authorize]
        public class CalendarController : ControllerBase
        {
            private static readonly string[] apiScopes = new[] { "access_as_user" };

            private readonly GraphServiceClient _graphClient;
            private readonly ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<CalendarController> _logger;

            public CalendarController(ITokenAcquisition tokenAcquisition, GraphServiceClient graphClient, ILogger<CalendarController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _graphClient = graphClient;
                _logger = logger;
            }

            [HttpGet]
            public async Task<string> Get()
            {
                // This verifies that the access_as_user scope is
                // present in the bearer token, throws if not
                HttpContext.VerifyUserHasAnyAcceptedScope(apiScopes);

                // To verify that the identity libraries have authenticated
                // based on the token, log the user's name
                _logger.LogInformation($"Authenticated user: {User.GetDisplayName()}");

                try
                {
                    // TEMPORARY
                    // Get a Graph token via OBO flow
                    var token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(new[]{
                            "User.Read",
                            "MailboxSettings.Read",
                            "Calendars.ReadWrite" });

                    // Log the token
                    _logger.LogInformation($"Access token for Graph: {token}");
                    return "{ \"status\": \"OK\" }";
                }
                catch (MicrosoftIdentityWebChallengeUserException ex)
                {
                    _logger.LogError(ex, "Consent required");
                    // This exception indicates consent is required.
                    // Return a 403 with "consent_required" in the body
                    // to signal to the tab it needs to prompt for consent
                    HttpContext.Response.ContentType = "text/plain";
                    HttpContext.Response.StatusCode = (int)HttpStatusCode.Forbidden;
                    await HttpContext.Response.WriteAsync("consent_required");
                    return null;
                }
                catch (Exception ex)
                {
                    _logger.LogError(ex, "Error occurred");
                    return null;
                }
            }
        }
    }
    ```

    Dies implementiert eine WebAPI ( `GET /calendar` ), die von der Registerkarte Teams aufgerufen werden kann. Für jetzt versucht es einfach, das Träger Token für ein Diagramm Token auszutauschen. Wenn ein Benutzer die Registerkarte zum ersten Mal lädt, tritt ein Fehler auf, da er noch nicht zugestimmt hat, den App-Zugriff auf Microsoft Graph in seinem Namen zuzulassen.

1. Öffnen Sie **./pages/index.cshtml** , und ersetzen Sie die `successCallback` Funktion durch Folgendes.

    ```javascript
    successCallback: (token) => {
      // TEMPORARY: Call the Web API
      fetch('/calendar', {
        headers: {
          'Authorization': `Bearer ${token}`
        }
      }).then(response => {
        response.text()
          .then(body => {
            $('#tab-container').empty();
            $('<code/>', {
              text: body
            }).appendTo('#tab-container');
          });
      }).catch(error => {
        console.error(error);
        renderError(error);
      });
    }
    ```

    Dadurch wird die WebAPI aufgerufen und die Antwort angezeigt.

1. Speichern Sie die Änderungen, und starten Sie die App neu. Aktualisieren Sie die Registerkarte in Microsoft Teams. Die Seite sollte angezeigt werden `consent_required` .

1. Überprüfen Sie die Protokollausgabe in ihrer CLI. Beachten Sie zwei Dinge.

    - Ein Eintrag wie `Authenticated user: MeganB@contoso.com` . Die WebAPI hat den Benutzer basierend auf dem Token authentifiziert, das mit der API-Anforderung gesendet wurde.
    - Ein Eintrag wie `AADSTS65001: The user or administrator has not consented to use the application with ID...` . Dies wird erwartet, da der Benutzer noch nicht zur Zustimmung für die angeforderten Microsoft Graph-Berechtigungs Bereiche aufgefordert wurde.

## <a name="implement-consent-prompt"></a>Implementieren der Zustimmungsaufforderung

Da die WebAPI den Benutzer nicht auffordern kann, muss die Registerkarte Microsoft Teams eine Eingabeaufforderung implementieren. Dies muss nur einmal für jeden Benutzer erfolgen. Sobald ein Benutzer zustimmt, müssen Sie nicht erneut zustimmen, es sei denn, Sie widerrufen den Zugriff auf Ihre Anwendung ausdrücklich.

1. Erstellen Sie eine neue Datei im **./Pages** -Verzeichnis mit dem Namen **Authenticate.cshtml.cs** , und fügen Sie den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Pages/Authenticate.cshtml.cs" id="AuthenticateModelSnippet":::

1. Erstellen Sie eine neue Datei im **./Pages** -Verzeichnis mit dem Namen **Authenticate. cshtml** , und fügen Sie den folgenden Code hinzu.

    :::code language="razor" source="../demo/GraphTutorial/Pages/Authenticate.cshtml":::

1. Erstellen Sie eine neue Datei im Verzeichnis **./Pages** mit dem Namen **AuthComplete. cshtml** , und fügen Sie den folgenden Code hinzu.

    :::code language="razor" source="../demo/GraphTutorial/Pages/AuthComplete.cshtml":::

1. Öffnen Sie **/pages/index.cshtml** , und fügen Sie die folgenden Funktionen innerhalb des- `<script>` Tags hinzu.

    :::code language="javascript" source="../demo/GraphTutorial/Pages/Index.cshtml" id="LoadUserCalendarSnippet":::

1. Fügen Sie die folgende Funktion innerhalb des `<script>` -Tags hinzu, um ein erfolgreiches Ergebnis aus der WebAPI anzuzeigen.

    ```javascript
    function renderCalendar(events) {
      $('#tab-container').empty();

      $('<pre/>').append($('<code/>', {
        text: JSON.stringify(events, null, 2),
        style: 'word-break: break-all;'
      })).appendTo('#tab-container');
    }
    ```

1. Ersetzen Sie den vorhandenen `successCallback` durch den folgenden Code.

    ```javascript
    successCallback: (token) => {
      loadUserCalendar(token, (events) => {
        renderCalendar(events);
      });
    }
    ```

1. Speichern Sie die Änderungen, und starten Sie die App neu. Aktualisieren Sie die Registerkarte in Microsoft Teams. Die Registerkarte sollte angezeigt werden `{ "status": "OK" }` .

1. Überprüfen Sie die Protokollausgabe. Der Eintrag sollte angezeigt werden `Access token for Graph` . Wenn Sie dieses Token analysieren, werden Sie feststellen, dass es die Microsoft Graph-Bereiche enthält, die in **appsettings.jsauf** konfiguriert sind.

## <a name="storing-and-refreshing-tokens"></a>Speichern und Aktualisieren von Token

Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird. Dies ist das Token, durch das die App im Namen des Benutzers auf Microsoft Graph zugreifen kann.

Dieses Token ist jedoch nur kurzzeitig verfügbar. Das Token läuft eine Stunde nach seiner Ausgabe ab. An dieser Stelle kommt das Aktualisierungstoken ins Spiel. Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.

Da die APP die Microsoft. Identity. webbibliothek verwendet, müssen Sie keine Token-Speicher-oder Aktualisierungslogik implementieren.

Die APP verwendet den in-Memory-Token-Cache, der für apps ausreicht, die beim Neustart der APP keine Token beibehälten müssen. In Produktions-apps werden stattdessen möglicherweise die Optionen für den [verteilten Cache](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in der Microsoft. Identity. webbibliothek verwendet.

Die `GetAccessTokenForUserAsync` -Methode behandelt Token-Ablauf und-Aktualisierung für Sie. Zunächst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben. Wenn er abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues zu erhalten.

Die **GraphServiceClient** , die von Controllern über Dependency Injection abgerufen werden, sind mit einem Authentifizierungsanbieter vorkonfiguriert, der `GetAccessTokenForUserAsync` für Sie verwendet wird.
