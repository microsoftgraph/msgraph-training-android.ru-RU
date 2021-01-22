<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. Для этого в приложение будет интегрирована библиотека проверки подлинности [Майкрософт (MSAL) для Android.](https://github.com/AzureAD/microsoft-authentication-library-for-android)

1. Щелкните правой кнопкой **мыши папку res** и выберите **"Новый"** и "Каталог ресурсов **Android".**

1. Измените **тип ресурса на "ОК"** и выберите `raw` **"ОК".**

1. Щелкните правой кнопкой мыши **новую необработанные** папку и выберите **"Новый"** и **"Файл".**

1. Наберем имя `msal_config.json` файла и выберите **"ОК".**

1. Добавьте в файлmsal_config.js **следующее.**

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. Замените его на ИД приложения из регистрации приложения и на имя `YOUR_APP_ID_HERE` `com.example.graphtutorial` пакета проекта.

    > [!IMPORTANT]
    > Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления исходным кодом, чтобы не допустить случайной утечки вашего `msal_config.json` ИД приложения.

## <a name="implement-sign-in"></a>Реализация входов

В этом разделе вы обновите манифест, чтобы разрешить MSAL использовать браузер для проверки подлинности пользователя, зарегистрировать URI перенаправления как обрабатываемого приложением, создать дополнительный класс проверки подлинности и обновить приложение, чтобы войти и выйти.

1. **Разкрой папку** приложения или манифеста и откройте **AndroidManifest.xml.** Добавьте следующие элементы над `application` элементом.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Эти разрешения необходимы для проверки подлинности пользователя в библиотеке MSAL.

1. Добавьте в элемент следующий элемент, заменив `application` `YOUR_PACKAGE_NAME_HERE` строку именем пакета.

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

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".** Измените **тип на** **"Интерфейс".** Назовем интерфейс `IAuthenticationHelperCreatedListener` и выберите **"ОК".**

1. Откройте новый файл и замените его содержимое следующим:

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".** Назовем класс `AuthenticationHelper` и выберите **"ОК".**

1. Откройте новый файл и замените его содержимое следующим:

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. Откройте **MainActivity** и добавьте следующие `import` утверждения.

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Добавьте в класс следующие свойства `MainActivity` членов.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. Добавьте ниже указанный код в конец функции `onCreate`.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. Добавьте в класс следующие `MainActivity` функции.

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAttemptInteractiveSignIn = shouldAttemptInteractive;
        mAuthHelper.acquireTokenSilently(getAuthCallback());
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this, getAuthCallback());
    }

    // Handles the authentication result
    public AuthenticationCallback getAuthCallback() {
        return new AuthenticationCallback() {

            @Override
            public void onSuccess(IAuthenticationResult authenticationResult) {
                // Log the token for debug purposes
                String accessToken = authenticationResult.getAccessToken();
                Log.d("AUTH", String.format("Access token: %s", accessToken));

                hideProgressBar();

                setSignedInState(true);
                openHomeFragment(mUserName);
            }

            @Override
            public void onError(MsalException exception) {
                // Check the type of exception and handle appropriately
                if (exception instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    if (mAttemptInteractiveSignIn) {
                        doInteractiveSignIn();
                    }
                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account" ||
                        exception.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (mAttemptInteractiveSignIn) {
                            doInteractiveSignIn();
                        }
                    } else {
                        // Exception inside MSAL, more info inside MsalError.java
                        Log.e("AUTH", "Client error authenticating", exception);
                    }
                } else if (exception instanceof MsalServiceException) {
                    // Exception when communicating with the auth server, likely config issue
                    Log.e("AUTH", "Service error authenticating", exception);
                }
                hideProgressBar();
            }

            @Override
            public void onCancel() {
                // User canceled the authentication
                Log.d("AUTH", "Authentication canceled");
                hideProgressBar();
            }
        };
    }
    ```

1. Замените существующие `signIn` `signOut` функции на следующие.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > Обратите внимание, что метод делает вход в систему в тихом `signIn` режиме (с `doSilentSignIn` помощью). При сбойе функции тихого вызова для этого метода будет вызовите интерактивный вход. Это позволяет избежать запроса пользователя при каждом запуске приложения.

1. Сохраните изменения и запустите приложение.

1. При **нажатии** элемента меню "Вход" откроется страница входа в Azure AD в браузере. Выполните вход с помощью своей учетной записи.

После возобновления работы приложения в журнале отлаки в Android Studio должен отпечататься маркер доступа.

![Снимок экрана: окно Logcat в Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Получить сведения о пользователе

В этом разделе вы создадим дополнительный класс для удержания всех вызовов Microsoft Graph и обновим класс, чтобы использовать этот новый класс для получения во входе `MainActivity` пользователя.

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".** Назовем класс `GraphHelper` и выберите **"ОК".**

1. Откройте новый файл и замените его содержимое следующим:

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.authentication.IAuthenticationProvider;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.http.IHttpRequest;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.extensions.GraphServiceClient;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private IGraphServiceClient mClient = null;
        private String mAccessToken = null;

        private GraphHelper() {
            mClient = GraphServiceClient.builder()
                    .authenticationProvider(this).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        // Part of the Graph IAuthenticationProvider interface
        // This method is called before sending the HTTP request
        @Override
        public void authenticateRequest(IHttpRequest request) {
            // Add the access token in the Authorization header
            request.addHeader("Authorization", "Bearer " + mAccessToken);
        }

        public void getUser(String accessToken, ICallback<User> callback) {
            mAccessToken = accessToken;

            // GET /me (logged in user)
            mClient.me().buildRequest()
                    .select("displayName,mail,mailboxSettings,userPrincipalName")
                    .get(callback);
        }
    }
    ```

    > [!NOTE]
    > Подумайте, что делает этот код.
    >
    > - Он реализует интерфейс для вставки маркера доступа в загоне в `IAuthenticationProvider` `Authorization` исходях HTTP-запросах.
    > - Он предоставляет функцию для получения сведений во входе пользователя из `getUser` `/me` конечной точки Graph.

1. Добавьте следующие утверждения в верхнюю часть файла `import` **MainActivity.**

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. Добавьте в класс следующую `MainActivity` функцию, чтобы создать объект `ICallback` для вызова Graph.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. Удалите следующие строки, задав имя пользователя и электронную почту:

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. Замените `onSuccess` переопределения в `AuthenticationCallback` следующем.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. Сохраните изменения и запустите приложение. После того как пользовательский интерфейс будет обновлен с помощью отображаемого имени пользователя и адреса электронной почты.
