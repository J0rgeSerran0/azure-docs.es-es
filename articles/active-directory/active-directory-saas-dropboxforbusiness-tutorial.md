---
title: 'Tutorial: Integración de Azure Active Directory con Dropbox for Business | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: aa00a88f8325345b1b45d7d0971a03590bce1029
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: Integración de Azure Active Directory con Dropbox para Empresas

En este tutorial, aprenderá a integrar Dropbox for Business con Azure Active Directory (Azure AD).

La integración de Dropbox for Business con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Dropbox for Business.
- Es posible habilitar que los usuarios inicien sesión automáticamente en Dropbox for Business (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Dropbox for Business, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a Dropbox for Business con inicio de sesión único habilitado.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Dropbox for Business desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adición de Dropbox for Business desde la galería
Para configurar la integración de Dropbox for Business en Azure AD, es preciso agregar Dropbox for Business desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Dropbox for Business desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Dropbox for Business**, seleccione **Dropbox for Business** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Dropbox for Business en la lista de resultados](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, se configura y prueba el inicio de sesión único de Azure AD con Dropbox for Business con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de Dropbox for Business para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Dropbox for Business.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** en Dropbox for Business.

Para configurar y probar el inicio de sesión único de Azure AD con Dropbox for Business, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Dropbox for Business](#create-a-dropbox-for-business-test-user)**: para tener un homólogo de Britta Simon en Dropbox for Business que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Dropbox for Business.

**Para configurar el inicio de sesión único de Azure AD con Dropbox for Business, siga estos pasos:**

1. En la página de integración de la aplicación **Dropbox for Business** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. En la sección de **dominio y direcciones URL de Dropbox for Business**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.dropbox.com/sso/<id>`.

    b. En el cuadro de texto **Identificador**, escriba un valor: `Dropbox`

    > [!NOTE] 
    > El valor de URL de inicio de sesión anterior no es real. El valor se actualizará con la dirección URL de inicio de sesión real, que se explica más adelante en el tutorial. Póngase en contacto con el [equipo de atención al cliente de Dropbox for Business](https://www.dropbox.com/business/contact) para obtener el valor. 
 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Dropbox for Business**, haga clic en **Configure Dropbox for Business** (Configurar Dropbox for Business) para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Para configurar el inicio de sesión único en **Dropbox for Business**, vaya al inquilino de Dropbox for Business.

    a. Inicie sesión en el inquilino de Dropbox for Business. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "Configurar inicio de sesión único")
   
    b. En el panel de navegación de la izquierda, haga clic en **Admin Console**(Consola de administración). 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "Configurar inicio de sesión único")
   
    c. En la **Admin Console** (Consola de administración), haga clic en la opción **Authentication** (Autenticación) del panel de navegación izquierdo. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "Configurar inicio de sesión único")
   
    d. En la sección **Single sign-on** (Inicio de sesión único), seleccione **Enable single sign-on** (Habilitar inicio de sesión único) y haga clic en **More** (Más) para expandir esta sección.  
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "Configurar inicio de sesión único")
   
    e. Copie la dirección URL que está al lado de **Users can sign in by entering their email address or they can go directly to** (Los usuarios pueden iniciar sesión especificando su dirección de correo electrónico o pueden ir directamente a) y péguela en el cuadro de texto **URL de inicio de sesión** de la sección **Dominio y direcciones URL de Dropbox for Business** de Azure Portal. 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
8. En la sección **Inicio de sesión único** de la página **Autenticación**siga estos pasos: 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurar inicio de sesión único")
   
    a. Haga clic en **Required**(Obligatorio).
   
    b. En el cuadro de texto **URL de inicio de sesión**, pegue el valor de **URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. Haga clic en **Elegir certificado** y vaya a su **archivo de certificado codificado en Base 64**.

    d. Haga clic en **Guardar cambios** para completar la configuración en el inquilino de DropBox for Business.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-dropbox-for-business-test-user"></a>Creación de un usuario de prueba de Dropbox for Business

En esta sección, creará un usuario llamado a Britta Simon en Dropbox for Business. Dropbox for Business admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si un usuario ya no existe en Dropbox for Business, se crea uno nuevo cuando se intenta acceder a esta aplicación.

>[!Note]
>Si necesita crear un usuario de forma manual, póngase en contacto con el [equipo de soporte de cliente de Dropbox for Business](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Dropbox for Business para que use el inicio de sesión único de Azure.

![Asignación de rol de usuario][200] 

**Siga estos pasos para asignar Britta Simon a Dropbox for Business:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Dropbox for Business**.

    ![El vínculo de Dropbox for Business en la lista Aplicaciones](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono Dropbox for Business en el Panel de acceso, debe ir a la página de inicio de sesión de la aplicación Dropbox for Business.
 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

