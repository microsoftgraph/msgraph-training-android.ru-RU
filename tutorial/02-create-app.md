<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="42a6e-101">Начните с создания нового проекта Android Studio.</span><span class="sxs-lookup"><span data-stu-id="42a6e-101">Begin by creating a new Android Studio project.</span></span>

1. <span data-ttu-id="42a6e-102">Откройте Android Studio и выберите **начать новый проект Android Studio** на экране приветствия.</span><span class="sxs-lookup"><span data-stu-id="42a6e-102">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span>

1. <span data-ttu-id="42a6e-103">В диалоговом окне **Создание нового проекта** выберите **пустое действие**, а затем нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-103">In the **Create New Project** dialog, select **Empty Activity**, then select **Next**.</span></span>

    ![Снимок экрана: диалоговое окно "Создание нового проекта" в Android Studio](./images/choose-project.png)

1. <span data-ttu-id="42a6e-105">В диалоговом окне **Настройка проекта** укажите **имя** `Graph Tutorial`, убедитесь, что для `Java`поля **язык** задано значение, а для `API 29: Android 10.0 (Q)`параметра **минимальный уровень API** задано значение.</span><span class="sxs-lookup"><span data-stu-id="42a6e-105">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 29: Android 10.0 (Q)`.</span></span> <span data-ttu-id="42a6e-106">Измените **имя пакета** и **сохраните расположение** по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="42a6e-106">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="42a6e-107">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-107">Select **Finish**.</span></span>

    ![Снимок экрана: диалоговое окно "Настройка проекта"](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="42a6e-109">Код и инструкции в этом руководстве используют имя пакета **com. example. графтуториал**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-109">The code and instructions in this tutorial use the package name **com.example.graphtutorial**.</span></span> <span data-ttu-id="42a6e-110">Если при создании проекта используется другое имя пакета, обязательно используйте имя вашего пакета везде, где вы видите это значение.</span><span class="sxs-lookup"><span data-stu-id="42a6e-110">If you use a different package name when creating the project, be sure to use your package name wherever you see this value.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="42a6e-111">Установка зависимостей</span><span class="sxs-lookup"><span data-stu-id="42a6e-111">Install dependencies</span></span>

<span data-ttu-id="42a6e-112">Перед перемещением установите некоторые дополнительные зависимости, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="42a6e-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="42a6e-113">`com.google.android.material:material`чтобы сделать [представление навигации](https://material.io/develop/android/components/navigation-view/) доступным для приложения.</span><span class="sxs-lookup"><span data-stu-id="42a6e-113">`com.google.android.material:material` to make the [navigation view](https://material.io/develop/android/components/navigation-view/) available to the app.</span></span>
- <span data-ttu-id="42a6e-114">[Библиотека проверки подлинности Microsoft (MSAL) для Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) для обработки проверки подлинности и управления маркерами Azure AD.</span><span class="sxs-lookup"><span data-stu-id="42a6e-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="42a6e-115">[Пакет SDK Microsoft Graph для Java](https://github.com/microsoftgraph/msgraph-sdk-java) для совершения вызовов в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="42a6e-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="42a6e-116">Разверните узел **сценарии Gradle**, а затем откройте файл **Build. Gradle (Module: App)** .</span><span class="sxs-lookup"><span data-stu-id="42a6e-116">Expand **Gradle Scripts**, then open the **build.gradle (Module: app)** file.</span></span>

1. <span data-ttu-id="42a6e-117">Добавьте следующие строки в `dependencies` значение.</span><span class="sxs-lookup"><span data-stu-id="42a6e-117">Add the following lines inside the `dependencies` value.</span></span>

    ```Gradle
    implementation 'com.google.android.material:material:1.0.0'
    implementation 'com.microsoft.identity.client:msal:1.0.0'
    implementation 'com.microsoft.graph:microsoft-graph:1.6.0'
    ```

1. <span data-ttu-id="42a6e-118">Добавьте `packagingOptions` значение в `android` значение в файле **Build. gradle (Module: App)** .</span><span class="sxs-lookup"><span data-stu-id="42a6e-118">Add a `packagingOptions` value inside the `android` value in the **build.gradle (Module: app)** file.</span></span>

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. <span data-ttu-id="42a6e-119">Сохраните изменения.</span><span class="sxs-lookup"><span data-stu-id="42a6e-119">Save your changes.</span></span> <span data-ttu-id="42a6e-120">В меню **файл** выберите команду **синхронизировать проект с файлами Gradle**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-120">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="42a6e-121">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="42a6e-121">Design the app</span></span>

<span data-ttu-id="42a6e-122">Приложение будет использовать входной ящик для навигации по разным представлениям.</span><span class="sxs-lookup"><span data-stu-id="42a6e-122">The application will use a navigation drawer to navigate between different views.</span></span> <span data-ttu-id="42a6e-123">На этом этапе вы обновите действие, чтобы оно использовало макет ящика навигации, и добавьте фрагменты для представлений.</span><span class="sxs-lookup"><span data-stu-id="42a6e-123">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="42a6e-124">Создание ящика навигации</span><span class="sxs-lookup"><span data-stu-id="42a6e-124">Create a navigation drawer</span></span>

<span data-ttu-id="42a6e-125">В этом разделе вы создадите значки меню навигации приложения, создадите меню для приложения и обновите тему и макет приложения, чтобы обеспечить совместимость с ящиком навигации.</span><span class="sxs-lookup"><span data-stu-id="42a6e-125">In this section you will create icons for the app's navigation menu, create a menu for the application, and update the application's theme and layout to be compatible with a navigation drawer.</span></span>

#### <a name="create-icons"></a><span data-ttu-id="42a6e-126">Создание значков</span><span class="sxs-lookup"><span data-stu-id="42a6e-126">Create icons</span></span>

1. <span data-ttu-id="42a6e-127">Щелкните правой кнопкой мыши папку **app/RES/Draw** и выберите **создать**, затем **Векторный ресурс**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-127">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span>

1. <span data-ttu-id="42a6e-128">Нажмите кнопку со значком рядом с элементом **Коллекция картинок**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-128">Click the icon button next to **Clip Art**.</span></span>

1. <span data-ttu-id="42a6e-129">В окне **Выбор значка** введите текст `home` в строке поиска, а затем щелкните значок " **домой** " и нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-129">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and select **OK**.</span></span>

1. <span data-ttu-id="42a6e-130">Измените **имя** на `ic_menu_home`.</span><span class="sxs-lookup"><span data-stu-id="42a6e-130">Change the **Name** to `ic_menu_home`.</span></span>

    ![Снимок экрана с окном настройки векторного актива](./images/create-icon.png)

1. <span data-ttu-id="42a6e-132">Нажмите кнопку **Далее**, а затем кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-132">Select **Next**, then **Finish**.</span></span>

1. <span data-ttu-id="42a6e-133">Повторите предыдущий шаг, чтобы создать три значка.</span><span class="sxs-lookup"><span data-stu-id="42a6e-133">Repeat the previous step to create three more icons.</span></span>

    - <span data-ttu-id="42a6e-134">Name: `ic_menu_calendar`, Icon:`event`</span><span class="sxs-lookup"><span data-stu-id="42a6e-134">Name: `ic_menu_calendar`, Icon: `event`</span></span>
    - <span data-ttu-id="42a6e-135">Name: `ic_menu_signout`, Icon:`exit to app`</span><span class="sxs-lookup"><span data-stu-id="42a6e-135">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
    - <span data-ttu-id="42a6e-136">Name: `ic_menu_signin`, Icon:`person add`</span><span class="sxs-lookup"><span data-stu-id="42a6e-136">Name: `ic_menu_signin`, Icon: `person add`</span></span>

#### <a name="create-the-menu"></a><span data-ttu-id="42a6e-137">Создание меню</span><span class="sxs-lookup"><span data-stu-id="42a6e-137">Create the menu</span></span>

1. <span data-ttu-id="42a6e-138">Щелкните правой кнопкой мыши папку **RES** и выберите команду **создать**, а затем **Каталог ресурсов Android**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-138">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="42a6e-139">Измените **тип ресурса** на `menu` и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-139">Change the **Resource type** to `menu` and select **OK**.</span></span>

1. <span data-ttu-id="42a6e-140">Щелкните правой кнопкой мыши новую папку **меню** и выберите пункт **создать**, а затем **файл ресурсов меню**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-140">Right-click the new **menu** folder and select **New**, then **Menu resource file**.</span></span>

1. <span data-ttu-id="42a6e-141">Присвойте файлу `drawer_menu` имя и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-141">Name the file `drawer_menu` and select **OK**.</span></span>

1. <span data-ttu-id="42a6e-142">Когда откроется файл, перейдите на вкладку **текст** , чтобы просмотреть XML, а затем замените все содержимое на следующее:</span><span class="sxs-lookup"><span data-stu-id="42a6e-142">When the file opens, select the **Text** tab to view the XML, then replace the entire contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        tools:showIn="navigation_view">

        <group android:checkableBehavior="single">
            <item
                android:id="@+id/nav_home"
                android:icon="@drawable/ic_menu_home"
                android:title="Home" />

            <item
                android:id="@+id/nav_calendar"
                android:icon="@drawable/ic_menu_calendar"
                android:title="Calendar" />
        </group>

        <item android:title="Account">
            <menu>
                <item
                    android:id="@+id/nav_signin"
                    android:icon="@drawable/ic_menu_signin"
                    android:title="Sign In" />

                <item
                    android:id="@+id/nav_signout"
                    android:icon="@drawable/ic_menu_signout"
                    android:title="Sign Out" />
            </menu>
        </item>

    </menu>
    ```

#### <a name="update-application-theme-and-layout"></a><span data-ttu-id="42a6e-143">Обновление темы и макета приложения</span><span class="sxs-lookup"><span data-stu-id="42a6e-143">Update application theme and layout</span></span>

1. <span data-ttu-id="42a6e-144">Откройте файл **app/RES/Values/Styles. XML** и замените `Theme.AppCompat.Light.DarkActionBar` его `Theme.AppCompat.Light.NoActionBar`на.</span><span class="sxs-lookup"><span data-stu-id="42a6e-144">Open the **app/res/values/styles.xml** file and replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span>

1. <span data-ttu-id="42a6e-145">Добавьте следующие строки в `style` элемент.</span><span class="sxs-lookup"><span data-stu-id="42a6e-145">Add the following lines inside the `style` element.</span></span>

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. <span data-ttu-id="42a6e-146">Щелкните правой кнопкой мыши папку **app/res/layout** .</span><span class="sxs-lookup"><span data-stu-id="42a6e-146">Right-click the **app/res/layout** folder.</span></span>

1. <span data-ttu-id="42a6e-147">Выберите **создать**, а затем **файл ресурса макета**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-147">Select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="42a6e-148">Присвойте файлу `nav_header` имя и замените **корневой элемент** на `LinearLayout`, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-148">Name the file `nav_header` and change the **Root element** to `LinearLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="42a6e-149">Откройте файл **nav_header. XML** и выберите вкладку **текст** . Замените все содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="42a6e-149">Open the **nav_header.xml** file and select the **Text** tab. Replace the entire contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="176dp"
        android:background="@color/colorPrimary"
        android:gravity="bottom"
        android:orientation="vertical"
        android:padding="16dp"
        android:theme="@style/ThemeOverlay.AppCompat.Dark">

        <ImageView
            android:id="@+id/user_profile_pic"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@mipmap/ic_launcher" />

        <TextView
            android:id="@+id/user_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingTop="8dp"
            android:text="Test User"
            android:textAppearance="@style/TextAppearance.AppCompat.Body1" />

        <TextView
            android:id="@+id/user_email"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="test@contoso.com" />

    </LinearLayout>
    ```

1. <span data-ttu-id="42a6e-150">Откройте файл **app/res/layout/activity_main. XML** и измените макет на a `DrawerLayout` , заменив существующий XML на следующий.</span><span class="sxs-lookup"><span data-stu-id="42a6e-150">Open the **app/res/layout/activity_main.xml** file and update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/drawer_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        tools:context=".MainActivity"
        tools:openDrawer="start">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <ProgressBar
                android:id="@+id/progressbar"
                android:layout_width="75dp"
                android:layout_height="75dp"
                android:layout_centerInParent="true"
                android:visibility="gone"/>

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="@color/colorPrimary"
                android:elevation="4dp"
                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" />

            <FrameLayout
                android:id="@+id/fragment_container"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_below="@+id/toolbar" />
        </RelativeLayout>

        <com.google.android.material.navigation.NavigationView
            android:id="@+id/nav_view"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/nav_header"
            app:menu="@menu/drawer_menu" />

    </androidx.drawerlayout.widget.DrawerLayout>
    ```

1. <span data-ttu-id="42a6e-151">Откройте **приложение/RES/Values/strings. XML** и добавьте следующие элементы в `resources` элемент.</span><span class="sxs-lookup"><span data-stu-id="42a6e-151">Open **app/res/values/strings.xml** and add the following elements inside the `resources` element.</span></span>

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. <span data-ttu-id="42a6e-152">Откройте файл **app/Java/com. example/графтуториал/MainActivity** и замените все содержимое приведенным ниже содержимым.</span><span class="sxs-lookup"><span data-stu-id="42a6e-152">Open the **app/java/com.example/graphtutorial/MainActivity** file and replace the entire contents with the following.</span></span>

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
        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;

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

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            menu.findItem(R.id.nav_signin).setVisible(!isSignedIn);
            menu.findItem(R.id.nav_calendar).setVisible(isSignedIn);
            menu.findItem(R.id.nav_signout).setVisible(isSignedIn);

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Megan Bowen";
                mUserEmail = "meganb@contoso.com";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a><span data-ttu-id="42a6e-153">Добавление фрагментов</span><span class="sxs-lookup"><span data-stu-id="42a6e-153">Add fragments</span></span>

<span data-ttu-id="42a6e-154">В этом разделе вы создадите фрагменты для представлений "домашний" и "Календарь".</span><span class="sxs-lookup"><span data-stu-id="42a6e-154">In this section you will create fragments for the home and calendar views.</span></span>

1. <span data-ttu-id="42a6e-155">Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **создать**, затем **файл ресурсов макета**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-155">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="42a6e-156">Присвойте файлу `fragment_home` имя и замените **корневой элемент** на `RelativeLayout`, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-156">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="42a6e-157">Откройте файл **fragment_home. XML** и замените его содержимое приведенным ниже параметром.</span><span class="sxs-lookup"><span data-stu-id="42a6e-157">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:orientation="vertical">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:text="Welcome!"
                android:textSize="30sp" />

            <TextView
                android:id="@+id/home_page_username"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:paddingTop="8dp"
                android:text="Please sign in"
                android:textSize="20sp" />
        </LinearLayout>

    </RelativeLayout>
    ```

1. <span data-ttu-id="42a6e-158">Щелкните правой кнопкой мыши папку **app/res/layout** и выберите **создать**, затем **файл ресурсов макета**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-158">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="42a6e-159">Присвойте файлу `fragment_calendar` имя и замените **корневой элемент** на `RelativeLayout`, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-159">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="42a6e-160">Откройте файл **fragment_calendar. XML** и замените его содержимое приведенным ниже параметром.</span><span class="sxs-lookup"><span data-stu-id="42a6e-160">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="42a6e-161">Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-161">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="42a6e-162">Назовите класс `HomeFragment` и присвойте его свойству **Суперкласс** `androidx.fragment.app.Fragment`, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-162">Name the class `HomeFragment` and set the **Superclass** to `androidx.fragment.app.Fragment`, then select **OK**.</span></span>

1. <span data-ttu-id="42a6e-163">Откройте файл **хомефрагмент** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="42a6e-163">Open the **HomeFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class HomeFragment extends Fragment {
        private static final String USER_NAME = "userName";

        private String mUserName;

        public HomeFragment() {

        }

        public static HomeFragment createInstance(String userName) {
            HomeFragment fragment = new HomeFragment();

            // Add the provided username to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(USER_NAME, userName);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mUserName = getArguments().getString(USER_NAME);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View homeView = inflater.inflate(R.layout.fragment_home, container, false);

            // If there is a username, replace the "Please sign in" with the username
            if (mUserName != null) {
                TextView userName = homeView.findViewById(R.id.home_page_username);
                userName.setText(mUserName);
            }

            return homeView;
        }
    }
    ```

1. <span data-ttu-id="42a6e-164">Щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-164">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="42a6e-165">Назовите класс `CalendarFragment` и присвойте его свойству **Суперкласс** `androidx.fragment.app.Fragment`, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-165">Name the class `CalendarFragment` and set the **Superclass** to `androidx.fragment.app.Fragment`, then select **OK**.</span></span>

1. <span data-ttu-id="42a6e-166">Откройте файл **календарфрагмент** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="42a6e-166">Open the **CalendarFragment** file and replace its contents with the following.</span></span>

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

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. <span data-ttu-id="42a6e-167">Откройте файл **MainActivity. Java** и добавьте указанные ниже функции в класс.</span><span class="sxs-lookup"><span data-stu-id="42a6e-167">Open the **MainActivity.java** file and add the the following functions to the class.</span></span>

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
    private void openCalendarFragment() {
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, new CalendarFragment())
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
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

1. <span data-ttu-id="42a6e-168">Замените имеющуюся функцию `onNavigationItemSelected` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="42a6e-168">Replace the existing `onNavigationItemSelected` function with the following.</span></span>

    ```java
    @Override
    public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
        // Load the fragment that corresponds to the selected item
        switch (menuItem.getItemId()) {
            case R.id.nav_home:
                openHomeFragment(mUserName);
                break;
            case R.id.nav_calendar:
                openCalendarFragment();
                break;
            case R.id.nav_signin:
                signIn();
                break;
            case R.id.nav_signout:
                signOut();
                break;
        }

        mDrawer.closeDrawer(GravityCompat.START);

        return true;
    }
    ```

1. <span data-ttu-id="42a6e-169">Добавьте следующий оператор в конец `onCreate` функции для загрузки фрагмента Home при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="42a6e-169">Add the following at the end of the `onCreate` function to load the home fragment when the app starts.</span></span>

    ```java
    // Load the home fragment by default on startup
    if (savedInstanceState == null) {
        openHomeFragment(mUserName);
    }
    ```

1. <span data-ttu-id="42a6e-170">Сохраните все изменения.</span><span class="sxs-lookup"><span data-stu-id="42a6e-170">Save all of your changes.</span></span>

1. <span data-ttu-id="42a6e-171">В меню **выполнить** выберите команду **запустить приложение**.</span><span class="sxs-lookup"><span data-stu-id="42a6e-171">On the **Run** menu, select **Run 'app'**.</span></span>

<span data-ttu-id="42a6e-172">Меню приложения должно работать для перехода между двумя фрагментами и изменения при касании кнопок **входа** **и выхода.**</span><span class="sxs-lookup"><span data-stu-id="42a6e-172">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Снимок экрана приложения](./images/app-screens.png)
