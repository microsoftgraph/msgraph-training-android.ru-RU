<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="3b59a-101">В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3b59a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="3b59a-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="3b59a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="3b59a-103">Для этого в приложение будет интегрирована библиотека проверки подлинности [Майкрософт (MSAL) для Android.](https://github.com/AzureAD/microsoft-authentication-library-for-android)</span><span class="sxs-lookup"><span data-stu-id="3b59a-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="3b59a-104">Щелкните правой кнопкой **мыши папку res** и выберите **"Новый"** и "Каталог ресурсов **Android".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="3b59a-105">Измените **тип ресурса на "ОК"** и выберите `raw` **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="3b59a-106">Щелкните правой кнопкой мыши **новую необработанные** папку и выберите **"Новый"** и **"Файл".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="3b59a-107">Наберем имя `msal_config.json` файла и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="3b59a-108">Добавьте в файлmsal_config.js **следующее.**</span><span class="sxs-lookup"><span data-stu-id="3b59a-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="3b59a-109">Замените его на ИД приложения из регистрации приложения и на имя `YOUR_APP_ID_HERE` `com.example.graphtutorial` пакета проекта.</span><span class="sxs-lookup"><span data-stu-id="3b59a-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="3b59a-110">Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления исходным кодом, чтобы не допустить случайной утечки вашего `msal_config.json` ИД приложения.</span><span class="sxs-lookup"><span data-stu-id="3b59a-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="3b59a-111">Реализация входов</span><span class="sxs-lookup"><span data-stu-id="3b59a-111">Implement sign-in</span></span>

<span data-ttu-id="3b59a-112">В этом разделе вы обновите манифест, чтобы разрешить MSAL использовать браузер для проверки подлинности пользователя, зарегистрировать URI перенаправления как обрабатываемого приложением, создать дополнительный класс проверки подлинности и обновить приложение, чтобы войти и выйти.</span><span class="sxs-lookup"><span data-stu-id="3b59a-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="3b59a-113">**Разкрой папку** приложения или манифеста и откройте **AndroidManifest.xml.**</span><span class="sxs-lookup"><span data-stu-id="3b59a-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="3b59a-114">Добавьте следующие элементы над `application` элементом.</span><span class="sxs-lookup"><span data-stu-id="3b59a-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="3b59a-115">Эти разрешения необходимы для проверки подлинности пользователя в библиотеке MSAL.</span><span class="sxs-lookup"><span data-stu-id="3b59a-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="3b59a-116">Добавьте в элемент следующий элемент, заменив `application` `YOUR_PACKAGE_NAME_HERE` строку именем пакета.</span><span class="sxs-lookup"><span data-stu-id="3b59a-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="3b59a-117">Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="3b59a-118">Измените **тип на** **"Интерфейс".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="3b59a-119">Назовем интерфейс `IAuthenticationHelperCreatedListener` и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="3b59a-120">Откройте новый файл и замените его содержимое следующим:</span><span class="sxs-lookup"><span data-stu-id="3b59a-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="3b59a-121">Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="3b59a-122">Назовем класс `AuthenticationHelper` и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="3b59a-123">Откройте новый файл и замените его содержимое следующим:</span><span class="sxs-lookup"><span data-stu-id="3b59a-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="3b59a-124">Откройте **MainActivity** и добавьте следующие `import` утверждения.</span><span class="sxs-lookup"><span data-stu-id="3b59a-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="3b59a-125">Добавьте в класс следующие свойства `MainActivity` членов.</span><span class="sxs-lookup"><span data-stu-id="3b59a-125">Add the following member properties to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. <span data-ttu-id="3b59a-126">Добавьте ниже указанный код в конец функции `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="3b59a-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="3b59a-127">Добавьте в класс следующие `MainActivity` функции.</span><span class="sxs-lookup"><span data-stu-id="3b59a-127">Add the following functions to the `MainActivity` class.</span></span>

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

1. <span data-ttu-id="3b59a-128">Замените существующие `signIn` `signOut` функции на следующие.</span><span class="sxs-lookup"><span data-stu-id="3b59a-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="3b59a-129">Обратите внимание, что метод делает вход в систему в тихом `signIn` режиме (с `doSilentSignIn` помощью).</span><span class="sxs-lookup"><span data-stu-id="3b59a-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="3b59a-130">При сбойе функции тихого вызова для этого метода будет вызовите интерактивный вход.</span><span class="sxs-lookup"><span data-stu-id="3b59a-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="3b59a-131">Это позволяет избежать запроса пользователя при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="3b59a-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="3b59a-132">Сохраните изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="3b59a-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="3b59a-133">При **нажатии** элемента меню "Вход" откроется страница входа в Azure AD в браузере.</span><span class="sxs-lookup"><span data-stu-id="3b59a-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="3b59a-134">Выполните вход с помощью своей учетной записи.</span><span class="sxs-lookup"><span data-stu-id="3b59a-134">Sign in with your account.</span></span>

<span data-ttu-id="3b59a-135">После возобновления работы приложения в журнале отлаки в Android Studio должен отпечататься маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="3b59a-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Снимок экрана: окно Logcat в Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="3b59a-137">Получить сведения о пользователе</span><span class="sxs-lookup"><span data-stu-id="3b59a-137">Get user details</span></span>

<span data-ttu-id="3b59a-138">В этом разделе вы создадим дополнительный класс для удержания всех вызовов Microsoft Graph и обновим класс, чтобы использовать этот новый класс для получения во входе `MainActivity` пользователя.</span><span class="sxs-lookup"><span data-stu-id="3b59a-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="3b59a-139">Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="3b59a-140">Назовем класс `GraphHelper` и выберите **"ОК".**</span><span class="sxs-lookup"><span data-stu-id="3b59a-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="3b59a-141">Откройте новый файл и замените его содержимое следующим:</span><span class="sxs-lookup"><span data-stu-id="3b59a-141">Open the new file and replace its contents with the following.</span></span>

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
    > <span data-ttu-id="3b59a-142">Подумайте, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="3b59a-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="3b59a-143">Он реализует интерфейс для вставки маркера доступа в загоне в `IAuthenticationProvider` `Authorization` исходях HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="3b59a-143">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="3b59a-144">Он предоставляет функцию для получения сведений во входе пользователя из `getUser` `/me` конечной точки Graph.</span><span class="sxs-lookup"><span data-stu-id="3b59a-144">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="3b59a-145">Добавьте следующие утверждения в верхнюю часть файла `import` **MainActivity.**</span><span class="sxs-lookup"><span data-stu-id="3b59a-145">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="3b59a-146">Добавьте в класс следующую `MainActivity` функцию, чтобы создать объект `ICallback` для вызова Graph.</span><span class="sxs-lookup"><span data-stu-id="3b59a-146">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. <span data-ttu-id="3b59a-147">Удалите следующие строки, задав имя пользователя и электронную почту:</span><span class="sxs-lookup"><span data-stu-id="3b59a-147">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="3b59a-148">Замените `onSuccess` переопределения в `AuthenticationCallback` следующем.</span><span class="sxs-lookup"><span data-stu-id="3b59a-148">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. <span data-ttu-id="3b59a-149">Сохраните изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="3b59a-149">Save your changes and run the app.</span></span> <span data-ttu-id="3b59a-150">После того как пользовательский интерфейс будет обновлен с помощью отображаемого имени пользователя и адреса электронной почты.</span><span class="sxs-lookup"><span data-stu-id="3b59a-150">After sign-in the UI is updated with the user's display name and email address.</span></span>
