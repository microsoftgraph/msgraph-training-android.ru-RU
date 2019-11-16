<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f2ee3-101">В этом упражнении вы создадите новое собственное приложение Azure AD с помощью центра администрирования Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="f2ee3-102">Откройте браузер и перейдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) и войдите с помощью **личной учетной записи** (т.е. учетной записи Майкрософт) или **рабочей или учебной учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="f2ee3-103">Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="f2ee3-104">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="f2ee3-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="f2ee3-105">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-105">Select **New registration**.</span></span> <span data-ttu-id="f2ee3-106">На странице**Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="f2ee3-107">Введите **имя** `Android Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-107">Set **Name** to `Android Graph Tutorial`.</span></span>
    - <span data-ttu-id="f2ee3-108">Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="f2ee3-109">В разделе **URI перенаправления**установите для параметра раскрывающийся список **общедоступный клиент/основной (мобильный & Настольный)** и задайте для `msauth://YOUR_PACKAGE_NAME/callback`свойства значение, заменив `YOUR_PACKAGE_NAME` название пакета проекта.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-109">Under **Redirect URI**, set the dropdown to **Public client/native (mobile & desktop)** and set the value to `msauth://YOUR_PACKAGE_NAME/callback`, replacing `YOUR_PACKAGE_NAME` with your project's package name.</span></span>

    ![Снимок страницы "регистрация приложения"](./images/aad-register-an-app.png)

1. <span data-ttu-id="f2ee3-111">Выберите **регистр**.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-111">Select **Register**.</span></span> <span data-ttu-id="f2ee3-112">На странице **учебника по диаграмме Android** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="f2ee3-112">On the **Android Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)
