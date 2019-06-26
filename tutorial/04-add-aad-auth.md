<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7eeb1-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="7eeb1-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="7eeb1-103">Для этого необходимо интегрировать [библиотеку проверки подлинности Microsoft (MSAL) для Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) в приложение.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="7eeb1-104">Щелкните правой кнопкой мыши папку **app/RES/Values** и выберите команду **создать**, а затем **файл ресурсов со значениями**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-104">Right-click the **app/res/values** folder and select **New**, then **Values resource file**.</span></span>

1. <span data-ttu-id="7eeb1-105">Присвойте файлу `oauth_strings` имя и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-105">Name the file `oauth_strings` and select **OK**.</span></span>

1. <span data-ttu-id="7eeb1-106">Добавьте в `resources` элемент указанные ниже значения.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-106">Add the following values to the `resources` element.</span></span>

    ```xml
    <string name="oauth_app_id">YOUR_APP_ID_HERE</string>
    <string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
    <string-array name="oauth_scopes">
        <item>User.Read</item>
        <item>Calendars.Read</item>
    </string-array>
    ```

    <span data-ttu-id="7eeb1-107">Замените `YOUR_APP_ID_HERE` идентификатором приложения, указанным в регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-107">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7eeb1-108">Если вы используете систему управления версиями (например, Git), то теперь мы бы не могли исключить `oauth_strings.xml` файл из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-108">If you're using source control such as git, now would be a good time to exclude the `oauth_strings.xml` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="7eeb1-109">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="7eeb1-109">Implement sign-in</span></span>

<span data-ttu-id="7eeb1-110">В этом разделе описывается обновление манифеста, чтобы разрешить MSAL использовать браузер для проверки подлинности пользователя, зарегистрируйте URI перенаправления как обрабатываемого приложением, создайте вспомогательный класс проверки подлинности и обновите приложение, чтобы войти в систему и выйти из нее.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-110">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="7eeb1-111">Разверните папку **приложения** и манифесты и откройте **AndroidManifest. XML**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-111">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="7eeb1-112">Добавьте следующие элементы над `application` элементом.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-112">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="7eeb1-113">Эти разрешения необходимы для того, чтобы библиотека MSAL прошедшие проверку подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-113">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="7eeb1-114">Добавьте следующий элемент в `application` элемент.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-114">Add the following element inside the `application` element.</span></span>

    ```xml
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />

            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />

            <data
                android:host="auth"
                android:scheme="@string/oauth_redirect_uri" />
        </intent-filter>
    </activity>
    ```

1. <span data-ttu-id="7eeb1-115">Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-115">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="7eeb1-116">Назовите класс `AuthenticationHelper` и нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-116">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="7eeb1-117">Откройте новый файл и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-117">Open the new file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.app.Activity;
    import android.content.Context;
    import android.content.Intent;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAccount;
    import com.microsoft.identity.client.PublicClientApplication;

    // Singleton class - the app only needs a single instance
    // of PublicClientApplication
    public class AuthenticationHelper {
        private static AuthenticationHelper INSTANCE = null;
        private PublicClientApplication mPCA = null;
        private String[] mScopes;

        private AuthenticationHelper(Context ctx) {
            String appId = ctx.getResources().getString(R.string.oauth_app_id);
            mScopes = ctx.getResources().getStringArray(R.array.oauth_scopes);
            mPCA = new PublicClientApplication(ctx, appId);
        }

        public static synchronized AuthenticationHelper getInstance(Context ctx) {
            if (INSTANCE == null) {
                INSTANCE = new AuthenticationHelper(ctx);
            }

            return INSTANCE;
        }

        // Version called from fragments. Does not create an
        // instance if one doesn't exist
        public static synchronized AuthenticationHelper getInstance() {
            if (INSTANCE == null) {
                throw new IllegalStateException(
                        "AuthenticationHelper has not been initialized from MainActivity");
            }

            return INSTANCE;
        }

        public boolean hasAccount() {
            return !mPCA.getAccounts().isEmpty();
        }

        public void handleRedirect(int requestCode, int resultCode, Intent data) {
            mPCA.handleInteractiveRequestRedirect(requestCode, resultCode, data);
        }

        public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
            mPCA.acquireToken(activity, mScopes, callback);
        }

        public void acquireTokenSilently(AuthenticationCallback callback) {
            mPCA.acquireTokenSilentAsync(mScopes, mPCA.getAccounts().get(0), callback);
        }

        public void signOut() {
            for (IAccount account : mPCA.getAccounts()) {
                mPCA.removeAccount(account);
            }
        }
    }
    ```

1. <span data-ttu-id="7eeb1-118">Откройте **MainActivity** и добавьте приведенные `import` ниже операторы.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-118">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.content.Intent;
    import android.support.annotation.Nullable;
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.AuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="7eeb1-119">Добавьте в `MainActivity` класс следующее свойство члена.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-119">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="7eeb1-120">Добавьте следующий элемент в конец `onCreate` функции.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-120">Add the following to the end of the `onCreate` function.</span></span>

    ```java
    // Get the authentication helper
    mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
    ```

1. <span data-ttu-id="7eeb1-121">Добавьте переопределение `onActivityResult` для обработки ответов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-121">Add an override for `onActivityResult` to handle authentication responses.</span></span>

    ```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        mAuthHelper.handleRedirect(requestCode, resultCode, data);
    }
    ```

1. <span data-ttu-id="7eeb1-122">Добавьте в `MainActivity` класс следующие функции.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-122">Add the following functions to the `MainActivity` class.</span></span>

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn() {
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
            public void onSuccess(AuthenticationResult authenticationResult) {
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
                    doInteractiveSignIn();

                } else if (exception instanceof MsalClientException) {
                    // Exception inside MSAL, more info inside MsalError.java
                    Log.e("AUTH", "Client error authenticating", exception);
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

1. <span data-ttu-id="7eeb1-123">Замените имеющиеся `signIn` функции и `signOut` функции на приведенные ниже.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-123">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    ```java
    private void signIn() {
        showProgressBar();
        if (mAuthHelper.hasAccount()) {
            doSilentSignIn();
        } else {
            doInteractiveSignIn();
        }
    }

    private void signOut() {
        mAuthHelper.signOut();

        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="7eeb1-124">Обратите внимание `signIn` , что метод сначала проверяет, существует ли уже в кэше MSAL учетная запись пользователя.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-124">Notice that the `signIn` method first checks if there is a user account already in the MSAL cache.</span></span> <span data-ttu-id="7eeb1-125">В противном случае он пытается обновить свои маркеры без уведомления, избегая необходимости запрашивать пользователя каждый раз при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-125">If there is, it attempts to refresh its tokens silently, avoiding having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="7eeb1-126">Сохраните изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-126">Save your changes and run the app.</span></span>

1. <span data-ttu-id="7eeb1-127">Когда вы нажмете элемент меню **войти** , браузер откроется на странице входа в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-127">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="7eeb1-128">Выполните вход с помощью своей учетной записи.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-128">Sign in with your account.</span></span>

<span data-ttu-id="7eeb1-129">После возобновления работы приложения в журнале отладки в Android Studio должен появиться маркер доступа, напечатанный в журнале отладки.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-129">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Снимок экрана: окно Логкат в Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="7eeb1-131">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="7eeb1-131">Get user details</span></span>

<span data-ttu-id="7eeb1-132">В этом разделе описывается создание вспомогательного класса для хранения всех вызовов Microsoft Graph и обновление `MainActivity` класса для использования этого нового класса для получения пользователя, вошедшего в систему.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-132">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="7eeb1-133">Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-133">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="7eeb1-134">Назовите класс `GraphHelper` и нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-134">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="7eeb1-135">Откройте новый файл и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-135">Open the new file and replace its contents with the following.</span></span>

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
            mClient.me().buildRequest().get(callback);
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="7eeb1-136">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-136">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="7eeb1-137">Он реализует `IAuthenticationProvider` интерфейс для вставки маркера доступа в `Authorization` заголовке исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-137">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="7eeb1-138">Он предоставляет `getUser` функцию для получения сведений о вошедшего пользователя из конечной точки `/me` Graph.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-138">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="7eeb1-139">Добавьте приведенные `import` ниже операторы в начало файла **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="7eeb1-139">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="7eeb1-140">Добавьте указанную ниже функцию в `MainActivity` класс для создания `ICallback` вызова Graph.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-140">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    ```java
    private ICallback<User> getUserCallback() {
        return new ICallback<User>() {
            @Override
            public void success(User user) {
                Log.d("AUTH", "User: " + user.displayName);

                mUserName = user.displayName;
                mUserEmail = user.mail == null ? user.userPrincipalName : user.mail;

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });

            }

            @Override
            public void failure(ClientException ex) {
                Log.e("AUTH", "Error getting /me", ex);
                mUserName = "ERROR";
                mUserEmail = "ERROR";

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });
            }
        };
    }
    ```

1. <span data-ttu-id="7eeb1-141">Удалите следующие строки, которые задают имя пользователя и адрес электронной почты:</span><span class="sxs-lookup"><span data-stu-id="7eeb1-141">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="7eeb1-142">Замените `onSuccess` переопределение на `AuthenticationCallback` приведенный ниже.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-142">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    ```java
    @Override
    public void onSuccess(AuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        // Get Graph client and get user
        GraphHelper graphHelper = GraphHelper.getInstance();
        graphHelper.getUser(accessToken, getUserCallback());
    }
    ```

<span data-ttu-id="7eeb1-143">Если вы сохраните изменения и запустите приложение сейчас, после входа в пользовательский интерфейс отобразится отображаемое имя пользователя и адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="7eeb1-143">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
