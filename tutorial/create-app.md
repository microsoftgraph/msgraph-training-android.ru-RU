<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ad9f8-101">Откройте Android Studio и выберите **начать новый проект Android Studio** на экране приветствия.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-101">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span> <span data-ttu-id="ad9f8-102">В диалоговом окне **Создание нового проекта** выберите **пустое действие**, а затем нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-102">In the **Create New Project** dialog, select **Empty Activity**, then choose **Next**.</span></span>

![Снимок экрана: диалоговое окно "Создание нового проекта" в Android Studio](./images/choose-project.png)

<span data-ttu-id="ad9f8-104">В диалоговом окне **Настройка проекта** укажите **имя** `Graph Tutorial`, убедитесь, что для `Java`поля **язык** задано значение, а для `API 27: Android 8.1 (Oreo)`параметра **минимальный уровень API** задано значение.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-104">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 27: Android 8.1 (Oreo)`.</span></span> <span data-ttu-id="ad9f8-105">Измените **имя пакета** и **сохраните расположение** по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-105">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="ad9f8-106">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-106">Select **Finish**.</span></span>

![Снимок экрана: диалоговое окно "Настройка проекта"](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="ad9f8-108">Убедитесь, что вы вводите точно такое же имя проекта, которое указано в данных инструкциях лаборатории.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-108">Ensure that you enter the exact same name for the project that is specified in these lab instructions.</span></span> <span data-ttu-id="ad9f8-109">Имя проекта становится частью пространства имен в коде.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-109">The project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="ad9f8-110">Код в этих инструкциях зависит от пространства имен, которое соответствует имени проекта, указанному в этих инструкциях.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-110">The code inside these instructions depends on the namespace matching the project name specified in these instructions.</span></span> <span data-ttu-id="ad9f8-111">Если вы используете другое имя проекта, код не будет компилироваться, если не настроить все пространства имен так, чтобы они соотнесены с именем проекта, вводимым при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-111">If you use a different project name the code will not compile unless you adjust all the namespaces to match the project name you enter when you create the project.</span></span>

<span data-ttu-id="ad9f8-112">Перед перемещением установите некоторые дополнительные зависимости, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="ad9f8-113">`com.android.support:design`чтобы сделать макеты лотков навигации доступными для приложения.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-113">`com.android.support:design` to make the navigation drawer layouts available to the app.</span></span>
- <span data-ttu-id="ad9f8-114">[Библиотека проверки ПодлиннОсти Microsoft (MSAL) для Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) для обработки проверки подлинности и управления маркерами Azure AD.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="ad9f8-115">[Пакет SDK Microsoft Graph для Java](https://github.com/microsoftgraph/msgraph-sdk-java) для совершения вызовов в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="ad9f8-116">Разверните узел **сценарии Gradle**, а затем откройте файл **Build. Gradle (Module: App)** .</span><span class="sxs-lookup"><span data-stu-id="ad9f8-116">Expand **Gradle Scripts**, then open the **build.gradle (Module: app)** file.</span></span> <span data-ttu-id="ad9f8-117">Добавьте следующие строки в `dependencies` значение.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-117">Add the following lines inside the `dependencies` value.</span></span>

```Gradle
implementation 'com.android.support:design:28.0.0'
implementation 'com.microsoft.graph:microsoft-graph:1.1.+'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

> [!NOTE]
> <span data-ttu-id="ad9f8-118">Если вы используете другую версию пакета SDK, обязательно измените значение в поле `28.0.0` в соответствие с версией `com.android.support:appcompat-v7` зависимости, которая уже присутствует в файле **Build. gradle**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-118">If you are using a different SDK version, make sure to change the `28.0.0` to match the version of the `com.android.support:appcompat-v7` dependency already present in **build.gradle**.</span></span>

<span data-ttu-id="ad9f8-119">Добавьте `packagingOptions` внутреннее `android` значение в файл **Build. gradle (Module: App)** .</span><span class="sxs-lookup"><span data-stu-id="ad9f8-119">Add a `packagingOptions` inside the `android` value in the **build.gradle (Module: app)** file.</span></span>

```Gradle
packagingOptions {
    pickFirst 'META-INF/jersey-module-version'
}
```

<span data-ttu-id="ad9f8-120">Сохраните изменения.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-120">Save your changes.</span></span> <span data-ttu-id="ad9f8-121">В меню **файл** выберите команду **синхронизировать проект с файлами Gradle**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-121">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="ad9f8-122">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="ad9f8-122">Design the app</span></span>

<span data-ttu-id="ad9f8-123">Приложение будет использовать [входной ящик для навигации](https://developer.android.com/training/implementing-navigation/nav-drawer) по разным представлениям.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-123">The application will use a [navigation drawer](https://developer.android.com/training/implementing-navigation/nav-drawer) to navigate between different views.</span></span> <span data-ttu-id="ad9f8-124">На этом этапе вы обновите действие, чтобы оно использовало макет ящика навигации, и добавьте фрагменты для представлений.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-124">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="ad9f8-125">Создание ящика навигации</span><span class="sxs-lookup"><span data-stu-id="ad9f8-125">Create a navigation drawer</span></span>

<span data-ttu-id="ad9f8-126">Начните с создания значков для меню навигации приложения.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-126">Start by creating icons for the app's navigation menu.</span></span> <span data-ttu-id="ad9f8-127">Щелкните правой кнопкой мыши папку **app/RES/Draw** и выберите **создать**, затем **Векторный ресурс**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-127">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span> <span data-ttu-id="ad9f8-128">Нажмите кнопку со значком рядом с элементом **Коллекция картинок**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-128">Click the icon button next to **Clip Art**.</span></span> <span data-ttu-id="ad9f8-129">В окне **Выбор значка** введите текст `home` в строке поиска, а затем щелкните значок " **домой** " и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-129">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and choose **OK**.</span></span> <span data-ttu-id="ad9f8-130">Измените **имя** на `ic_menu_home`.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-130">Change the **Name** to `ic_menu_home`.</span></span>

![Снимок экрана с окном настройки векторного актива](./images/create-icon.png)

<span data-ttu-id="ad9f8-132">Нажмите кнопку **Далее**, а затем кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-132">Choose **Next**, then **Finish**.</span></span> <span data-ttu-id="ad9f8-133">Повторите это действие, чтобы создать еще два значка.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-133">Repeat this step to create two more icons.</span></span>

- <span data-ttu-id="ad9f8-134">Name: `ic_menu_calendar`, Icon:`event`</span><span class="sxs-lookup"><span data-stu-id="ad9f8-134">Name: `ic_menu_calendar`, Icon: `event`</span></span>
- <span data-ttu-id="ad9f8-135">Name: `ic_menu_signout`, Icon:`exit to app`</span><span class="sxs-lookup"><span data-stu-id="ad9f8-135">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
- <span data-ttu-id="ad9f8-136">Name: `ic_menu_signin`, Icon:`person add`</span><span class="sxs-lookup"><span data-stu-id="ad9f8-136">Name: `ic_menu_signin`, Icon: `person add`</span></span>

<span data-ttu-id="ad9f8-137">Затем создайте меню для приложения.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-137">Next, create a menu for the application.</span></span> <span data-ttu-id="ad9f8-138">Щелкните правой кнопкой мыши папку **RES** и выберите команду **создать**, а затем **Каталог ресурсов Android**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-138">Right-click the **res** folder and choose **New**, then **Android Resource Directory**.</span></span> <span data-ttu-id="ad9f8-139">Измените **тип ресурса** на `menu` и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-139">Change the **Resource type** to `menu` and choose **OK**.</span></span>

<span data-ttu-id="ad9f8-140">Щелкните правой кнопкой мыши новую папку **меню** и выберите команду **создать**, а затем — **файл ресурсов меню**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-140">Right-click the new **menu** folder and choose **New**, then **Menu resource file**.</span></span> <span data-ttu-id="ad9f8-141">ПриСвойте файлу `drawer_menu` имя и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-141">Name the file `drawer_menu` and choose **OK**.</span></span> <span data-ttu-id="ad9f8-142">Когда откроется файл, выберите вкладку **текст** для просмотра XML и замените все содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-142">When the file opens, choose the **Text** tab to view the XML, then replace the entire contents with the following.</span></span>

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

<span data-ttu-id="ad9f8-143">Теперь обновите тему приложения, чтобы обеспечить совместимость с ящиком навигации.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-143">Now update the application's theme to be compatible with a navigation drawer.</span></span> <span data-ttu-id="ad9f8-144">Откройте файл **app/RES/Values/Styles. XML** .</span><span class="sxs-lookup"><span data-stu-id="ad9f8-144">Open the **app/res/values/styles.xml** file.</span></span> <span data-ttu-id="ad9f8-145">Замените `Theme.AppCompat.Light.DarkActionBar` на `Theme.AppCompat.Light.NoActionBar`.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-145">Replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span> <span data-ttu-id="ad9f8-146">Затем добавьте следующие строки в `style` элемент.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-146">Then add the following lines inside the `style` element.</span></span>

```xml
<item name="windowActionBar">false</item>
<item name="windowNoTitle">true</item>
<item name="android:statusBarColor">@android:color/transparent</item>
```

<span data-ttu-id="ad9f8-147">Затем создайте заголовок меню.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-147">Next, create a header for the menu.</span></span> <span data-ttu-id="ad9f8-148">Щелкните правой кнопкой мыши папку **app/res/layout** .</span><span class="sxs-lookup"><span data-stu-id="ad9f8-148">Right-click the **app/res/layout** folder.</span></span> <span data-ttu-id="ad9f8-149">Выберите **создать**, а затем **файл ресурса макета**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-149">Choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="ad9f8-150">ПриСвойте файлу `nav_header` имя и замените **корневой элемент** на `LinearLayout`.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-150">Name the file `nav_header` and change the **Root element** to `LinearLayout`.</span></span> <span data-ttu-id="ad9f8-151">Нажмите кнопку **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-151">Choose **OK**.</span></span>

<span data-ttu-id="ad9f8-152">Откройте файл **нав_хеадер. XML** и выберите вкладку **текст** . Замените все содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-152">Open the **nav_header.xml** file and choose the **Text** tab. Replace the entire contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<<?xml version="1.0" encoding="utf-8"?>
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

<span data-ttu-id="ad9f8-153">Теперь откройте файл **app/res/layout/активити_маин. XML** .</span><span class="sxs-lookup"><span data-stu-id="ad9f8-153">Now, open the **app/res/layout/activity_main.xml** file.</span></span> <span data-ttu-id="ad9f8-154">Измените макет на a `DrawerLayout` , заменив существующий XML приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-154">Update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
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

        <android.support.v7.widget.Toolbar
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

    <android.support.design.widget.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:headerLayout="@layout/nav_header"
        app:menu="@menu/drawer_menu" />

</android.support.v4.widget.DrawerLayout>
```

<span data-ttu-id="ad9f8-155">Затем откройте **приложение/RES/Values/strings. XML**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-155">Next, open **app/res/values/strings.xml**.</span></span> <span data-ttu-id="ad9f8-156">Добавьте в `resources` элемент следующие элементы.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-156">Add the following elements inside the `resources` element.</span></span>

```xml
<string name="navigation_drawer_open">Open navigation drawer</string>
<string name="navigation_drawer_close">Close navigation drawer</string>
```

<span data-ttu-id="ad9f8-157">Наконец, откройте файл **app/Java/com. example/графтуториал/MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="ad9f8-157">Finally, open the **app/java/com.example/graphtutorial/MainActivity** file.</span></span> <span data-ttu-id="ad9f8-158">Замените все содержимое приведенным ниже содержимым.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-158">Replace the entire contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.support.annotation.NonNull;
import android.support.design.widget.NavigationView;
import android.support.v4.view.GravityCompat;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.Toolbar;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.TextView;

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

### <a name="add-fragments"></a><span data-ttu-id="ad9f8-159">Добавление фрагментов</span><span class="sxs-lookup"><span data-stu-id="ad9f8-159">Add fragments</span></span>

<span data-ttu-id="ad9f8-160">Щелкните правой кнопкой мыши папку **app/res/layout** и выберите команду **создать**, а затем — **файл ресурсов макета**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-160">Right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="ad9f8-161">ПриСвойте файлу `fragment_home` имя и замените **корневой элемент** на `RelativeLayout`.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-161">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`.</span></span> <span data-ttu-id="ad9f8-162">Нажмите кнопку **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-162">Choose **OK**.</span></span>

<span data-ttu-id="ad9f8-163">Откройте файл **фрагмент_хоме. XML** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-163">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="ad9f8-164">Затем щелкните правой кнопкой мыши папку **app/res/layout** и выберите команду **создать**, а затем — **файл ресурсов макета**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-164">Next, right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="ad9f8-165">ПриСвойте файлу `fragment_calendar` имя и замените **корневой элемент** на `RelativeLayout`.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-165">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`.</span></span> <span data-ttu-id="ad9f8-166">Нажмите кнопку **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-166">Choose **OK**.</span></span>

<span data-ttu-id="ad9f8-167">Откройте файл **фрагмент_календар. XML** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-167">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="ad9f8-168">Теперь щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-168">Now, right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="ad9f8-169">Присвойте классу `HomeFragment` имя и присвойте `android.support.v4.app.Fragment` **суперклассу** значение.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-169">Name the class `HomeFragment` and set the **Superclass** to `android.support.v4.app.Fragment`.</span></span> <span data-ttu-id="ad9f8-170">Нажмите кнопку **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-170">Choose **OK**.</span></span> <span data-ttu-id="ad9f8-171">Откройте файл **хомефрагмент** и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-171">Open the **HomeFragment** file and replace its contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

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

<span data-ttu-id="ad9f8-172">Затем щелкните правой кнопкой мыши папку **app/Java/com. example. графтуториал** и выберите команду **создать**, а затем — **класс Java**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-172">Next, right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="ad9f8-173">Присвойте классу `CalendarFragment` имя и присвойте `android.support.v4.app.Fragment` **суперклассу** значение.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-173">Name the class `CalendarFragment` and set the **Superclass** to `android.support.v4.app.Fragment`.</span></span> <span data-ttu-id="ad9f8-174">Нажмите кнопку **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-174">Choose **OK**.</span></span>

<span data-ttu-id="ad9f8-175">Откройте файл **календарфрагмент** и добавьте приведенную ниже функцию в `CalendarFragment` класс.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-175">Open the **CalendarFragment** file and add the following function to the `CalendarFragment` class.</span></span>

```java
@Nullable
@Override
public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
    return inflater.inflate(R.layout.fragment_calendar, container, false);
}
```

<span data-ttu-id="ad9f8-176">Теперь, когда фрагменты реализованы, обновите `MainActivity` класс для обработки `onNavigationItemSelected` события и используйте фрагменты.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-176">Now that the fragments are implemented, update the `MainActivity` class to handle the `onNavigationItemSelected` event and use the fragments.</span></span> <span data-ttu-id="ad9f8-177">Сначала добавьте в класс следующие функции.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-177">First, add the following functions to the class.</span></span>

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

<span data-ttu-id="ad9f8-178">Затем замените существующую `onNavigationItemSelected` функцию на приведенную ниже.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-178">Next, replace the existing `onNavigationItemSelected` function with the following.</span></span>

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

<span data-ttu-id="ad9f8-179">Наконец, добавьте в конец `onCreate` функции следующую функцию для загрузки фрагмента Home при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-179">Finally, add the following at the end of the `onCreate` function to load the home fragment when the app starts.</span></span>

```java
// Load the home fragment by default on startup
if (savedInstanceState == null) {
    openHomeFragment(mUserName);
}
```

<span data-ttu-id="ad9f8-180">Сохраните все изменения.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-180">Save all of your changes.</span></span> <span data-ttu-id="ad9f8-181">В меню **выполнить** выберите команду **запустить приложение**.</span><span class="sxs-lookup"><span data-stu-id="ad9f8-181">On the **Run** menu, choose **Run 'app'**.</span></span> <span data-ttu-id="ad9f8-182">Меню приложения должно работать для перехода между двумя фрагментами и изменения при касании кнопок **входа** и выхода. \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="ad9f8-182">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Снимок экрана приложения](./images/welcome-page.png)