<!-- markdownlint-disable MD002 MD041 -->

Das App-Manifest beschreibt, wie die app in Microsoft Teams integriert wird und für die Installation von apps erforderlich ist. In diesem Abschnitt verwenden Sie App Studio im Microsoft Teams-Client, um ein Manifest zu generieren.

1. Wenn Sie das App-Studio nicht bereits in Microsoft Teams installiert haben, [Installieren Sie es jetzt](/microsoftteams/platform/concepts/build-and-test/app-studio-overview).

1. Starten Sie App Studio in Microsoft Teams, und wählen Sie den **Manifest-Editor** aus.

1. Wählen Sie **neue APP erstellen** aus.

    ![Ein Screenshot des Manifest-Editors in App Studio in Microsoft Teams](images/app-studio-01.png)

1. Füllen Sie auf der Seite mit den **App-Details** die erforderlichen Felder aus.

    > [!NOTE]
    > Sie können die Standardsymbole im Abschnitt **Branding** verwenden oder Ihre eigenen hochladen.

1. Klicken Sie im linken Menü auf **Registerkarten** unter **Funktionen**.

1. Wählen Sie unter **persönliche Registerkarte hinzufügen die** Option **hinzu** fügen aus.

    ![Screenshot der Registerkartenseite in App Studio](images/app-studio-02.png)

1. Füllen Sie die Felder wie folgt aus, wobei `YOUR_NGROK_URL` es sich um die Weiterleitungs-URL handelt, die Sie im vorherigen Abschnitt kopiert haben. Wählen Sie **Speichern** aus, wenn Sie fertig sind.

    - **Name:**`Create event`
    - **Entitäts-ID:**`createEventTab`
    - **Inhalts-URL:**`YOUR_NGROK_URL/newevent`

1. Wählen Sie unter **persönliche Registerkarte hinzufügen die** Option **hinzu** fügen aus.

1. Füllen Sie die Felder wie folgt aus, wobei `YOUR_NGROK_URL` es sich um die Weiterleitungs-URL handelt, die Sie im vorherigen Abschnitt kopiert haben. Wählen Sie **Speichern** aus, wenn Sie fertig sind.

    - **Name:**`Graph calendar`
    - **Entitäts-ID:**`calendarTab`
    - **Inhalts-URL:**`YOUR_NGROK_URL`

1. Wählen Sie im Menü links die Option **Domains und Berechtigungen** unter **Fertig stellen** aus.

1. Legen Sie die **Aad-APP-ID** von Ihrer APP-Registrierung auf die Anwendungs-ID fest.

1. Legen Sie das Feld für **einmaliges Anmelden** aus Ihrer APP-Registrierung auf den Anwendungs-ID-URI fest.

1. Klicken Sie im linken Menü auf **Testen und verteilen** unter **Fertig stellen**. Klicken Sie auf **herunterladen**.

1. Erstellen Sie ein neues Verzeichnis im Stamm des Projekts mit dem Namen **Manifest**. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei in dieses Verzeichnis.
