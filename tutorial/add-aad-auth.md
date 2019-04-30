<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе выполняется интеграция [библиотеки проверки ПодлиннОсти Microsoft (MSAL) для Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) с приложением.

Щелкните правой кнопкой мыши папку **app/RES/Values** и выберите команду **создать**, а затем **файл ресурсов со значениями**. ПриСвойте файлу `oauth_strings` имя и нажмите кнопку **ОК**. Добавьте в `resources` элемент указанные ниже значения.

```xml
<string name="oauth_app_id">YOUR_APP_ID_HERE</string>
<string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
<string-array name="oauth_scopes">
    <item>User.Read</item>
    <item>Calendars.Read</item>
</string-array>
```

> [!IMPORTANT]
> Если вы используете систему управления версиями (например, Git), то теперь мы бы не могли исключить `oauth_strings.xml` файл из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.

## <a name="implement-sign-in"></a>Реализация входа

Разверните папку **приложения** и манифесты и откройте **AndroidManifest. XML**. Добавьте следующие элементы над `application` элементом.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Эти разрешения необходимы для того, чтобы библиотека MSAL прошедшие проверку подлинности пользователя.

Теперь добавьте указанный ниже элемент в `application` элемент.

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

Это позволяет MSAL использовать браузер для проверки подлинности пользователя и регистрирует URI перенаправления в качестве обработки приложением.

### <a name="implement-an-authentication-helper"></a>Реализация помощника по проверке подлинности

Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**. НаЗовите класс `AuthenticationHelper` и нажмите кнопку **ОК**. Откройте новый файл и замените его содержимое на приведенный ниже код.

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

Теперь обновите `MainActivity` его, чтобы использовать этот новый класс. Откройте **MainActivity** и добавьте приведенные `import` ниже операторы.

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

Затем добавьте в `MainActivity` класс следующее свойство Member.

```java
private AuthenticationHelper mAuthHelper = null;
```

Обновление `onCreate` для установки `mAuthHelper`. Добавьте следующий элемент в конец `onCreate` функции.

```java
// Get the authentication helper
mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
```

Добавьте переопределение `onActivityResult` для обработки ответов проверки подлинности.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    mAuthHelper.handleRedirect(requestCode, resultCode, data);
}
```

Теперь добавьте в `MainActivity` класс следующие функции.

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

Наконец, замените имеющиеся `signIn` функции и `signOut` функции на приведенные ниже.

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

Обратите внимание `signIn` , что метод сначала проверяет, существует ли уже в кэше MSAL учетная запись пользователя. В противном случае он пытается обновить свои маркеры без уведомления, избегая необходимости запрашивать пользователя каждый раз при запуске приложения.

Сохраните изменения и запустите приложение. Когда вы нажмете элемент меню **войти** , браузер откроется на странице входа в Azure AD. Войдите с помощью своей учетной записи. После возобновления работы приложения в журнале отладки в Android Studio должен появиться маркер доступа, напечатанный в журнале отладки.

![Снимок экрана: окно Логкат в Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Получение сведений о пользователе

Начните с создания вспомогательного класса для хранения всех вызовов Microsoft Graph. Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**. НаЗовите класс `GraphHelper` и нажмите кнопку **ОК**. Откройте новый файл и замените его содержимое на приведенный ниже код.

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

Обратите внимание на то, что делает этот код.

- Он реализует `IAuthenticationProvider` интерфейс для вставки маркера доступа в `Authorization` заголовке исходящих HTTP-запросов.
- Он предоставляет `getUser` функцию для получения сведений о вошедшего пользователя из конечной точки `/me` Graph.

Теперь обновите `MainActivity` класс, чтобы он использовал этот новый класс, чтобы получить вошедшего в систему пользователя. Добавьте приведенные `import` ниже операторы в начало файла **MainActivity** .

```java
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.IGraphServiceClient;
import com.microsoft.graph.models.extensions.User;
```

Затем добавьте приведенную ниже функцию в `MainActivity` класс, чтобы создать объект `ICallback` для вызова Graph.

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

Удалите следующие строки, которые задают имя пользователя и адрес электронной почты:

```java
// For testing
mUserName = "Megan Bowen";
mUserEmail = "meganb@contoso.com";
```

В заключение замените `onSuccess` переопределение на `AuthenticationCallback` приведенный ниже.

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

Если вы сохраните изменения и запустите приложение сейчас, после входа в пользовательский интерфейс отобразится отображаемое имя пользователя и адрес электронной почты.
