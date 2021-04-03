<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширит приложение от предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. Для этого в приложение будет интегрирована библиотека проверки подлинности [Microsoft (MSAL) для Android.](https://github.com/AzureAD/microsoft-authentication-library-for-android)

1. Щелкните правой кнопкой мыши **папку res** и выберите **New**, а затем **Каталог ресурсов Android**.

1. Измените **тип ресурса и** выберите `raw` **ОК.**

1. Щелкните правой кнопкой мыши новую **сырую** папку и выберите **New**, а затем **Файл**.

1. Назови файл `msal_config.json` и выберите **ОК.**

1. Добавьте следующее в **msal_config.jsфайл.**

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. Замените `YOUR_APP_ID_HERE` iD приложения из регистрации приложения и `com.example.graphtutorial` замените имя пакета проекта.

    > [!IMPORTANT]
    > Если вы используете источник управления, например git, сейчас самое время исключить файл из источника управления, чтобы избежать случайной утечки вашего `msal_config.json` ID приложения.

## <a name="implement-sign-in"></a>Реализация входа в систему

В этом разделе вы обновите манифест, чтобы разрешить MSAL использовать браузер для проверки подлинности пользователя, зарегистрировать URI перенаправления как обрабатываемого приложением, создать класс помощника проверки подлинности и обновить приложение для регистрации и регистрации.

1. **Расширь папку app/manifests** и **откройтеAndroidManifest.xml.** Добавьте следующие элементы над `application` элементом.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Эти разрешения необходимы для проверки подлинности пользователя библиотекой MSAL.

1. Добавьте следующий элемент внутри `application` элемента, заменив `YOUR_PACKAGE_NAME_HERE` строку именем пакета.

    ```xml
    <!--Intent filter to capture authorization code response from the default browser on the
        device calling back to the app after interactive sign in -->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:scheme="msauth"
                android:host="YOUR_PACKAGE_NAME_HERE"
                android:path="/callback" />
        </intent-filter>
    </activity>
    ```

1. Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**. Изменение **вида** на **интерфейс**. Назови интерфейс `IAuthenticationHelperCreatedListener` и выберите **ОК.**

1. Откройте новый файл и замените его содержимое следующим.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**. Назови класс `AuthenticationHelper` и выберите **ОК.**

1. Откройте новый файл и замените его содержимое следующим.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. Откройте **MainActivity** и добавьте следующие `import` утверждения.

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Добавьте в класс следующее `MainActivity` свойство участника.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. Добавьте ниже указанный код в конец функции `onCreate`.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. Добавьте в класс следующие `MainActivity` функции.

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAuthHelper.acquireTokenSilently()
            .thenAccept(authenticationResult -> {
                handleSignInSuccess(authenticationResult);
            })
            .exceptionally(exception -> {
                // Check the type of exception and handle appropriately
                Throwable cause = exception.getCause();
                if (cause instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    if (shouldAttemptInteractive) doInteractiveSignIn();
                } else if (cause instanceof MsalClientException) {
                    MsalClientException clientException = (MsalClientException)cause;
                    if (clientException.getErrorCode() == "no_current_account" ||
                        clientException.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (shouldAttemptInteractive) doInteractiveSignIn();
                    }
                } else {
                    handleSignInFailure(cause);
                }
                hideProgressBar();
                return null;
            });
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this)
            .thenAccept(authenticationResult -> {
                handleSignInSuccess(authenticationResult);
            })
            .exceptionally(exception -> {
                handleSignInFailure(exception);
                hideProgressBar();
                return null;
            });
    }

    // Handles the authentication result
    private void handleSignInSuccess(IAuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        hideProgressBar();

        setSignedInState(true);
        openHomeFragment(mUserName);
    }

    private void handleSignInFailure(Throwable exception) {
        if (exception instanceof MsalServiceException) {
            // Exception when communicating with the auth server, likely config issue
            Log.e("AUTH", "Service error authenticating", exception);
        } else if (exception instanceof MsalClientException) {
            // Exception inside MSAL, more info inside MsalError.java
            Log.e("AUTH", "Client error authenticating", exception);
        } else {
            Log.e("AUTH", "Unhandled exception authenticating", exception);
        }
    }
    ```

1. Замените `signIn` существующие `signOut` и функции следующими.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > Обратите `signIn` внимание, что метод делает молчаливый вход (через). `doSilentSignIn` В случае сбой бесшумного метода при вызове этого метода будет работать интерактивный вход. Это позволяет избежать необходимости подсказок пользователю при каждом запуске приложения.

1. Сохраните изменения и запустите приложение.

1. При нажатии элемента **Вход** в меню браузер открывается на страницу входа Azure AD. Выполните вход с помощью своей учетной записи.

Как только приложение возобновится, вы увидите маркер доступа, напечатанный в журнале отлаговок в Android Studio.

![Снимок экрана окна Logcat в Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Получение сведений о пользователе

В этом разделе вы создадим класс помощника для удержания всех вызовов в Microsoft Graph и обновим класс, чтобы использовать этот новый класс для входа в `MainActivity` систему пользователя.

1. Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**. Назови класс `GraphHelper` и выберите **ОК.**

1. Откройте новый файл и замените его содержимое следующим.

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.GraphServiceClient;

    import java.util.concurrent.CompletableFuture;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private GraphServiceClient mClient = null;

        private GraphHelper() {
            AuthenticationHelper authProvider = AuthenticationHelper.getInstance();

            mClient = GraphServiceClient.builder()
                .authenticationProvider(authProvider).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        public CompletableFuture<User> getUser() {
            // GET /me (logged in user)
            return mClient.me().buildRequest()
                .select("displayName,mail,mailboxSettings,userPrincipalName")
                .getAsync();
        }
    }
    ```

    > [!NOTE]
    > Рассмотрим, что делает этот код.
    >
    > - Она предоставляет функцию для получения сведений пользователя в журнале из `getUser` конечной `/me` точки Graph.
    >   - Он использует для запроса только свойств `.select` пользователя, которое требуется приложению.

1. Удалите следующие строки, задав имя пользователя и электронную почту:

    ```java
    // For testing
    mUserName = "Lynne Robbins";
    mUserEmail = "lynner@contoso.com";
    mUserTimeZone = "Pacific Standard Time";
    ```

1. Замените имеющуюся функцию `handleSignInSuccess` указанным ниже кодом.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="HandleSignInSuccessSnippet":::

1. Сохраните изменения и запустите приложение. После регистрации пользовательский интерфейс обновляется с именем и адресом электронной почты пользователя.
