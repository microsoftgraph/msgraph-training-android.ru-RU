<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c8c4e-101">В этом упражнении вы расширит приложение от предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="c8c4e-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="c8c4e-103">Для этого в приложение будет интегрирована библиотека проверки подлинности [Microsoft (MSAL) для Android.](https://github.com/AzureAD/microsoft-authentication-library-for-android)</span><span class="sxs-lookup"><span data-stu-id="c8c4e-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="c8c4e-104">Щелкните правой кнопкой мыши **папку res** и выберите **New**, а затем **Каталог ресурсов Android**.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="c8c4e-105">Измените **тип ресурса и** выберите `raw` **ОК.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="c8c4e-106">Щелкните правой кнопкой мыши новую **сырую** папку и выберите **New**, а затем **Файл**.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="c8c4e-107">Назови файл `msal_config.json` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="c8c4e-108">Добавьте следующее в **msal_config.jsфайл.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="c8c4e-109">Замените `YOUR_APP_ID_HERE` iD приложения из регистрации приложения и `com.example.graphtutorial` замените имя пакета проекта.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="c8c4e-110">Если вы используете источник управления, например git, сейчас самое время исключить файл из источника управления, чтобы избежать случайной утечки вашего `msal_config.json` ID приложения.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="c8c4e-111">Реализация входа в систему</span><span class="sxs-lookup"><span data-stu-id="c8c4e-111">Implement sign-in</span></span>

<span data-ttu-id="c8c4e-112">В этом разделе вы обновите манифест, чтобы разрешить MSAL использовать браузер для проверки подлинности пользователя, зарегистрировать URI перенаправления как обрабатываемого приложением, создать класс помощника проверки подлинности и обновить приложение для регистрации и регистрации.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="c8c4e-113">**Расширь папку app/manifests** и **откройтеAndroidManifest.xml.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="c8c4e-114">Добавьте следующие элементы над `application` элементом.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="c8c4e-115">Эти разрешения необходимы для проверки подлинности пользователя библиотекой MSAL.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="c8c4e-116">Добавьте следующий элемент внутри `application` элемента, заменив `YOUR_PACKAGE_NAME_HERE` строку именем пакета.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="c8c4e-117">Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c8c4e-118">Изменение **вида** на **интерфейс**.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="c8c4e-119">Назови интерфейс `IAuthenticationHelperCreatedListener` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="c8c4e-120">Откройте новый файл и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="c8c4e-121">Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c8c4e-122">Назови класс `AuthenticationHelper` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="c8c4e-123">Откройте новый файл и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="c8c4e-124">Откройте **MainActivity** и добавьте следующие `import` утверждения.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="c8c4e-125">Добавьте в класс следующее `MainActivity` свойство участника.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-125">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="c8c4e-126">Добавьте ниже указанный код в конец функции `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="c8c4e-127">Добавьте в класс следующие `MainActivity` функции.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-127">Add the following functions to the `MainActivity` class.</span></span>

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

1. <span data-ttu-id="c8c4e-128">Замените `signIn` существующие `signOut` и функции следующими.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="c8c4e-129">Обратите `signIn` внимание, что метод делает молчаливый вход (через). `doSilentSignIn`</span><span class="sxs-lookup"><span data-stu-id="c8c4e-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="c8c4e-130">В случае сбой бесшумного метода при вызове этого метода будет работать интерактивный вход.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="c8c4e-131">Это позволяет избежать необходимости подсказок пользователю при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="c8c4e-132">Сохраните изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="c8c4e-133">При нажатии элемента **Вход** в меню браузер открывается на страницу входа Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="c8c4e-134">Выполните вход с помощью своей учетной записи.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-134">Sign in with your account.</span></span>

<span data-ttu-id="c8c4e-135">Как только приложение возобновится, вы увидите маркер доступа, напечатанный в журнале отлаговок в Android Studio.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Снимок экрана окна Logcat в Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="c8c4e-137">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="c8c4e-137">Get user details</span></span>

<span data-ttu-id="c8c4e-138">В этом разделе вы создадим класс помощника для удержания всех вызовов в Microsoft Graph и обновим класс, чтобы использовать этот новый класс для входа в `MainActivity` систему пользователя.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="c8c4e-139">Щелкните правой кнопкой **мыши приложение/java/com.example.graphtutorial** папку и выберите **New**, а затем **Java Class**.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c8c4e-140">Назови класс `GraphHelper` и выберите **ОК.**</span><span class="sxs-lookup"><span data-stu-id="c8c4e-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="c8c4e-141">Откройте новый файл и замените его содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-141">Open the new file and replace its contents with the following.</span></span>

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
    > <span data-ttu-id="c8c4e-142">Рассмотрим, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="c8c4e-143">Она предоставляет функцию для получения сведений пользователя в журнале из `getUser` конечной `/me` точки Graph.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-143">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>
    >   - <span data-ttu-id="c8c4e-144">Он использует для запроса только свойств `.select` пользователя, которое требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-144">It uses `.select` to request only the properties of the user that the application needs.</span></span>

1. <span data-ttu-id="c8c4e-145">Удалите следующие строки, задав имя пользователя и электронную почту:</span><span class="sxs-lookup"><span data-stu-id="c8c4e-145">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Lynne Robbins";
    mUserEmail = "lynner@contoso.com";
    mUserTimeZone = "Pacific Standard Time";
    ```

1. <span data-ttu-id="c8c4e-146">Замените имеющуюся функцию `handleSignInSuccess` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-146">Replace the existing `handleSignInSuccess` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="HandleSignInSuccessSnippet":::

1. <span data-ttu-id="c8c4e-147">Сохраните изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-147">Save your changes and run the app.</span></span> <span data-ttu-id="c8c4e-148">После регистрации пользовательский интерфейс обновляется с именем и адресом электронной почты пользователя.</span><span class="sxs-lookup"><span data-stu-id="c8c4e-148">After sign-in the UI is updated with the user's display name and email address.</span></span>
