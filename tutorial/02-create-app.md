<!-- markdownlint-disable MD002 MD041 -->

Начните с создания нового проекта Android Studio.

1. Откройте Android Studio и выберите **"Запустить новый проект Android Studio"** на экране приветствия.

1. В **диалоговом окте "Создание нового** проекта" выберите **"Пустое действие",** а затем выберите **"Далее".**

    ![Снимок экрана: диалоговое окно "Создание нового проекта" в Android Studio](./images/choose-project.png)

1. В **диалоговом окке** "Настройка проекта" установите имя, убедитесь, что для поля "Язык" установлено поле , и убедитесь, что установлен минимальный уровень  `Graph Tutorial`  `Java` **API.** `API 29: Android 10.0 (Q)` При необходимости **измените имя пакета** **и расположение сохранения.** Нажмите кнопку **Готово**.

    ![Снимок экрана: диалоговое окно "Настройка проекта"](./images/configure-project.png)

> [!IMPORTANT]
> В коде и инструкциях в этом руководстве используется имя пакета **com.example.graphtutorial.** Если при создании проекта используется другое имя пакета, обязательно используйте имя пакета, где бы вы ни видели это значение.

## <a name="install-dependencies"></a>Установка зависимостей

Прежде чем двигаться дальше, установите некоторые дополнительные зависимости, которые вы будете использовать позже.

- `com.google.android.material:material` чтобы сделать [представление навигации](https://material.io/develop/android/components/navigation-view/) доступным для приложения.
- [Библиотека проверки подлинности Майкрософт (MSAL) для Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) для обработки проверки подлинности Azure AD и управления маркерами.
- [Microsoft Graph SDK для Java](https://github.com/microsoftgraph/msgraph-sdk-java) для вызовов Microsoft Graph.

1. **Разкройте скрипты Gradle,** а затем откройте **build.gradle (Модуль: Graph_Tutorial.app).**

1. Добавьте следующие строки в `dependencies` значение.

    :::code language="gradle" source="../demo/GraphTutorial/app/build.gradle" id="DependenciesSnippet":::

1. Добавьте значение `packagingOptions` в значение `android` в **build.gradle (Module: Graph_Tutorial.app).**

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. Добавьте репозиторий Azure Maven для библиотеки MicrosoftDeviceSDK, зависимой от MSAL. Откройте **build.gradle (Project: Graph_Tutorial).** Добавьте следующее в значение `repositories` внутри `allprojects` значения.

    ```Gradle
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    ```

1. Сохраните изменения. В меню **"Файл"** выберите **"Синхронизировать проект с файлами Gradle".**

## <a name="design-the-app"></a>Проектирование приложения

Приложение будет использовать ящик навигации для навигации между разными представлениями. На этом этапе вы обновим действие, чтобы использовать макет навигационного ящика, и добавим фрагменты для представлений.

### <a name="create-a-navigation-drawer"></a>Создание ящика навигации

В этом разделе вы создадим значки для меню навигации приложения, создадим меню для приложения и обновим тему и макет приложения, чтобы они были совместимы с ящиком навигации.

#### <a name="create-icons"></a>Создание значков

1. Щелкните правой кнопкой **мыши папку app/res/drawable** и выберите **"Новый",** затем **"Векторный актив".**

1. Нажмите кнопку значка рядом с **изображением клипа.**

1. В **окне "Выбор значка"** введите в панели поиска значок "Главная" и `home` выберите "ОК".  

1. Измените **имя на** `ic_menu_home` .

    ![Снимок экрана с окном "Настройка векторного актива"](./images/create-icon.png)

1. Выберите **"Далее",** затем **"Готово".**

1. Повторите предыдущий шаг, чтобы создать еще четыре значка.

    - Имя: `ic_menu_calendar` , значок: `event`
    - Имя: `ic_menu_add_event` , значок: `add box`
    - Имя: `ic_menu_signout` , значок: `exit to app`
    - Имя: `ic_menu_signin` , значок: `person add`

#### <a name="create-the-menu"></a>Создание меню

1. Щелкните правой кнопкой **мыши папку res** и выберите **"Новый"** и "Каталог ресурсов **Android".**

1. Измените **тип ресурса на "ОК"** и выберите `menu` **"ОК".**

1. Щелкните правой кнопкой мыши **новую папку** меню и выберите **"Новый"** и "Файл **ресурсов меню".**

1. Наберем имя `drawer_menu` файла и выберите **"ОК".**

1. Когда файл откроется,  выберите вкладку "Код", чтобы просмотреть XML-код, а затем замените все содержимое на следующее.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/menu/drawer_menu.xml":::

#### <a name="update-application-theme-and-layout"></a>Обновление темы и макета приложения

1. Откройте **файл app/res/values/styles.xml** и замените `Theme.AppCompat.Light.DarkActionBar` на `Theme.AppCompat.Light.NoActionBar` .

1. Добавьте в элемент следующие `style` строки.

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. Щелкните правой **кнопкой мыши папку app/res/layout.**

1. Выберите **"Новый",** затем **"Файл ресурсов макета".**

1. Назовем `nav_header` файл и измените **корневой элемент** на `LinearLayout` ", а затем выберите **"ОК".**

1. Откройте файл **nav_header.xml** и выберите вкладку **"Текст".** Замените все содержимое на следующее.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/nav_header.xml":::

1. Откройте файл **app/res/layout/activity_main.xml** и замените существующий XML-файл на `DrawerLayout` следующий:

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/activity_main.xml":::

1. Откройте **app/res/values/strings.xml** добавьте в элемент следующие `resources` элементы.

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. Откройте файл **app/java/com.example/graphtutorial/MainActivity** и замените все содержимое на следующее.

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.widget.FrameLayout;
    import android.widget.ProgressBar;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.appcompat.app.ActionBarDrawerToggle;
    import androidx.appcompat.app.AppCompatActivity;
    import androidx.appcompat.widget.Toolbar;
    import androidx.core.view.GravityCompat;
    import androidx.drawerlayout.widget.DrawerLayout;
    import com.google.android.material.navigation.NavigationView;

    public class MainActivity extends AppCompatActivity implements NavigationView.OnNavigationItemSelectedListener {
        private static final String SAVED_IS_SIGNED_IN = "isSignedIn";
        private static final String SAVED_USER_NAME = "userName";
        private static final String SAVED_USER_EMAIL = "userEmail";
        private static final String SAVED_USER_TIMEZONE = "userTimeZone";

        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;
        private String mUserTimeZone = null;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // Set the toolbar
            Toolbar toolbar = findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            mDrawer = findViewById(R.id.drawer_layout);

            // Add the hamburger menu icon
            ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(this, mDrawer, toolbar,
                    R.string.navigation_drawer_open, R.string.navigation_drawer_close);
            mDrawer.addDrawerListener(toggle);
            toggle.syncState();

            mNavigationView = findViewById(R.id.nav_view);

            // Set user name and email
            mHeaderView = mNavigationView.getHeaderView(0);
            setSignedInState(mIsSignedIn);

            // Listen for item select events on menu
            mNavigationView.setNavigationItemSelectedListener(this);

            if (savedInstanceState == null) {
                // Load the home fragment by default on startup
                openHomeFragment(mUserName);
            } else {
                // Restore state
                mIsSignedIn = savedInstanceState.getBoolean(SAVED_IS_SIGNED_IN);
                mUserName = savedInstanceState.getString(SAVED_USER_NAME);
                mUserEmail = savedInstanceState.getString(SAVED_USER_EMAIL);
                mUserTimeZone = savedInstanceState.getString(SAVED_USER_TIMEZONE);
                setSignedInState(mIsSignedIn);
            }
        }

        @Override
        protected void onSaveInstanceState(@NonNull Bundle outState) {
            super.onSaveInstanceState(outState);
            outState.putBoolean(SAVED_IS_SIGNED_IN, mIsSignedIn);
            outState.putString(SAVED_USER_NAME, mUserName);
            outState.putString(SAVED_USER_EMAIL, mUserEmail);
            outState.putString(SAVED_USER_TIMEZONE, mUserTimeZone);
        }

        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
            // TEMPORARY
            return false;
        }

        @Override
        public void onBackPressed() {
            if (mDrawer.isDrawerOpen(GravityCompat.START)) {
                mDrawer.closeDrawer(GravityCompat.START);
            } else {
                super.onBackPressed();
            }
        }

        public void showProgressBar()
        {
            FrameLayout container = findViewById(R.id.fragment_container);
            ProgressBar progressBar = findViewById(R.id.progressbar);
            container.setVisibility(View.GONE);
            progressBar.setVisibility(View.VISIBLE);
        }

        public void hideProgressBar()
        {
            FrameLayout container = findViewById(R.id.fragment_container);
            ProgressBar progressBar = findViewById(R.id.progressbar);
            progressBar.setVisibility(View.GONE);
            container.setVisibility(View.VISIBLE);
        }

        // Update the menu and get the user's name and email
        private void setSignedInState(boolean isSignedIn) {
            mIsSignedIn = isSignedIn;

            mNavigationView.getMenu().clear();
            mNavigationView.inflateMenu(R.menu.drawer_menu);

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            if (isSignedIn) {
                menu.removeItem(R.id.nav_signin);
            } else {
                menu.removeItem(R.id.nav_home);
                menu.removeItem(R.id.nav_calendar);
                menu.removeItem(R.id.nav_create_event);
                menu.removeItem(R.id.nav_signout);
            }

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Lynne Robbins";
                mUserEmail = "lynner@contoso.com";
                mUserTimeZone = "Pacific Standard Time";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;
                mUserTimeZone = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a>Добавление фрагментов

В этом разделе вы создадим фрагменты для представлений дома и календаря.

1. Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **"Новый"** и "Файл ресурсов **макета".**

1. Назовем `fragment_home` файл и измените **корневой элемент** на `RelativeLayout` ", а затем выберите **"ОК".**

1. Откройте **fragment_home.xml** файла и замените его содержимое на следующее.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_home.xml":::

1. Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **"Новый"** и "Файл ресурсов **макета".**

1. Назовем `fragment_calendar` файл и измените **корневой элемент** на `RelativeLayout` ", а затем выберите **"ОК".**

1. Откройте **fragment_calendar.xml** файла и замените его содержимое на следующее.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="Calendar"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **"Новый"** и "Файл ресурсов **макета".**

1. Назовем `fragment_new_event` файл и измените **корневой элемент** на `RelativeLayout` ", а затем выберите **"ОК".**

1. Откройте **fragment_new_event.xml** файла и замените его содержимое на следующее.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="New Event"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**

1. Назовем `HomeFragment` класс, а затем выберите **"ОК".**

1. Откройте файл **HomeFragment** и замените его содержимое следующим:

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/HomeFragment.java" id="HomeSnippet":::

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**

1. Назовем `CalendarFragment` класс, а затем выберите **"ОК".**

1. Откройте файл **CalendarFragment** и замените его содержимое следующим:

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class CalendarFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public CalendarFragment() {}

        public static CalendarFragment createInstance(String timeZone) {
            CalendarFragment fragment = new CalendarFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. Щелкните правой кнопкой мыши папку **app/java/com.example.graphtutorial** и выберите **"Новый",** затем **"Класс Java".**

1. Назовем `NewEventFragment` класс, а затем выберите **"ОК".**

1. Откройте файл **NewEventFragment** и замените его содержимое на следующее.

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class NewEventFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public NewEventFragment() {}

        public static NewEventFragment createInstance(String timeZone) {
            NewEventFragment fragment = new NewEventFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_new_event, container, false);
        }
    }
    ```

1. Откройте файл **MainActivity.java** и добавьте в класс следующие функции.

    ```java
    // Load the "Home" fragment
    public void openHomeFragment(String userName) {
        HomeFragment fragment = HomeFragment.createInstance(userName);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_home);
    }

    // Load the "Calendar" fragment
    private void openCalendarFragment(String timeZone) {
        CalendarFragment fragment = CalendarFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
    }

    // Load the "New Event" fragment
    private void openNewEventFragment(String timeZone) {
        NewEventFragment fragment = NewEventFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_create_event);
    }

    private void signIn() {
        setSignedInState(true);
        openHomeFragment(mUserName);
    }

    private void signOut() {
        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

1. Замените имеющуюся функцию `onNavigationItemSelected` указанным ниже кодом.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnNavItemSelectedSnippet":::

1. Сохраните все изменения.

1. В меню **"Выполнить"** выберите пункт **"Запустить приложение".**

Меню приложения должно работать для перемещения между двумя фрагментами и  изменения при нажатии кнопки "Войти" или  "Выйти".

![Снимок экрана: приложение](./images/app-screens.png)
