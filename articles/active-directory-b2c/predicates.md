---
title: Predicates y PredicateValidations - Azure Active Directory B2C | Microsoft Docs
description: Ejemplos de transformación de notificaciones de cuentas sociales para el esquema del marco de experiencia de identidad de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06879164c6f72891b734da077c667c6f90448fe4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512962"
---
# <a name="predicates-and-predicatevalidations"></a>Predicates y PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Los elementos **Predicates** y **PredicateValidations** le permiten realizar un proceso de validación para asegurarse de que solo se introducen datos con el formato adecuado en el inquilino de Azure Active Directory (Azure AD) B2C.  

En el siguiente diagrama se muestra la relación entre los elementos:  

![Predicados](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicados  

El elemento **Predicate** define una validación básica para comprobar el valor de un tipo de notificación y devuelve `true` o `false`. La validación se realiza mediante el uso de un elemento **Method** especificado y un conjunto de elementos **Parameter** relevantes para el método. Por ejemplo, un predicado puede comprobar si la longitud de un valor de notificación de cadena está dentro del intervalo de parámetros mínimos y máximos especificados, o si un valor de notificación de cadena contiene un juego de caracteres. Si se produce un error en la comprobación, el elemento **UserHelpText** proporciona un mensaje de error para los usuarios. El valor del elemento **UserHelpText** se puede localizar mediante la [personalización de idioma](localization.md).

El elemento **Predicates** contiene el elemento siguiente:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| Predicate | 1:n | Una lista de predicados. | 

El elemento **Predicate** contiene los siguientes atributos:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Id | Sí | Un identificador que se usa para el predicado. Hay otros elementos que pueden usar este identificador en la directiva. |
| Método | Sí | El tipo de método que se usará para la validación. Valores posibles: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters** o **IsDateRange**. El valor **IsLengthRange** comprueba si la longitud de un valor de notificación de cadena está dentro del intervalo de parámetros mínimos y máximos especificados. El valor **MatchesRegex** comprueba si un valor de notificación de cadena coincide con una expresión regular. El valor **IncludesCharacters** comprueba si un valor de notificación de cadena contiene un juego de caracteres. El valor **IsDateRange** comprueba si un valor de notificación de fecha se encuentra dentro de un intervalo de parámetros mínimos y máximos especificados. |

El elemento **Predicate** contiene los siguientes elementos:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | S se produce un error en la comprobación, un mensaje de error para los usuarios. Esta cadena se puede localizar con la [personalización de idioma](localization.md). |
| Parámetros | 1:1 | Parámetros para el tipo de método de la validación de cadenas. | 

El elemento **Parameters** contiene los siguientes elementos:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| Parámetro | 1:n | Parámetros para el tipo de método de la validación de cadenas. | 

El elemento **Parameter** contiene los siguientes atributos:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identificador del parámetro. |

En el ejemplo siguiente se muestra un método `IsLengthRange` con los parámetros `Minimum` y `Maximum` que especifican el intervalo de longitud de la cadena:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

En el ejemplo siguiente se muestra un método `MatchesRegex` con el parámetro `RegularExpression` que especifica una expresión regular:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

En el ejemplo siguiente se muestra un método `IncludesCharacters` con el parámetro `CharacterSet` que especifica un juego de caracteres:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

En el ejemplo siguiente se muestra un método `IsDateRange` con los parámetros `Minimum` y `Maximum` que especifican el intervalo de fechas con un formato de `yyyy-MM-dd` y `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

Mientras que los predicados definen la validación que se va a comparar con un tipo de notificación, **PredicateValidations** agrupa un conjunto de predicados para formar una validación de entrada de usuario que se puede aplicar a un tipo de notificación. Cada elemento **PredicateValidation** contiene un conjunto de elementos **PredicateGroup** que contienen un conjunto de elementos **PredicateReference** que dirigen a un elemento **Predicate**. Para pasar la validación, el valor de la notificación debe superar todas las pruebas de cualquier predicado en todo el **PredicateGroup** con su conjunto de elementos **PredicateReference**.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

El elemento **PredicateValidations** contiene el elemento siguiente:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | Una lista de validación de predicados. | 

El elemento **PredicateValidation** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Id | Sí | Un identificador que se usa para la validación del predicado. El elemento **ClaimType** puede usar este identificador en la directiva. |

El elemento **PredicateValidation** contiene el elemento siguiente:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Una lista de grupos de predicados. | 

El elemento **PredicateGroups** contiene el elemento siguiente:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | Una lista de predicados. | 

El elemento **PredicateGroup** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Id | Sí | Un identificador que se usa para el grupo de predicados.  |

El elemento **PredicateGroup** contiene los elementos siguientes:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Una descripción del predicado que puede ser útil para que los usuarios sepan qué valor deben introducir. | 
| PredicateReferences | 1:n | Una lista de referencias de predicados. | 

El elemento **PredicateReferences** contiene los siguientes atributos:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| MatchAtLeast | Sin | Especifica que el valor debe coincidir por lo menos con ese número de definiciones de predicado para que la entrada se acepte. |

El elemento **PredicateReferences** contiene los siguientes elementos:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | Una referencia a un predicado. | 

El elemento **PredicateReference** contiene los siguientes atributos:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Id | Sí | Un identificador que se usa para la validación del predicado.  |


## <a name="configure-password-complexity"></a>Configurar la complejidad de la contraseña

Con **Predicates** y **PredicateValidationsInput** puede controlar los requisitos de complejidad para las contraseñas proporcionadas por un usuario al crear una cuenta. De forma predeterminada, Azure AD B2C utiliza contraseñas seguras. Azure AD B2C también admite opciones de configuración para controlar la complejidad de las contraseñas que los clientes pueden usar. Puede definir la complejidad de la contraseña mediante el uso de estos predicados: 

- **IsLengthBetween8And64** con el método `IsLengthRange` valida que la contraseña debe tener entre 8 y 64 caracteres.
- **Lowercase** con el método `IncludesCharacters` valida que la contraseña contiene una letra minúscula.
- **Uppercase** con el método `IncludesCharacters` valida que la contraseña contiene una letra mayúscula.
- **Number** con el método `IncludesCharacters` valida que la contraseña contiene un dígito.
- **Symbol** con el método `IncludesCharacters` valida que la contraseña contiene uno de los siguientes símbolos `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN** con el método `MatchesRegex` valida que la contraseña solo contiene números.
- **AllowedAADCharacters** con el método `MatchesRegex` valida que se ha proporcionado el único carácter no válido de la contraseña.
- **DisallowedWhitespace** con el método `MatchesRegex` valida que la contraseña no empiece ni termine con un carácter de espacio en blanco.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Después de definir las validaciones básicas, puede combinarlas y crear un conjunto de directivas de contraseña que puede usar en la directiva:

- **SimplePassword** valida los predicados DisallowedWhitespace, AllowedAADCharacters y IsLengthBetween8And64.
- **StrongPassword** valida los predicados DisallowedWhitespace, AllowedAADCharacters y IsLengthBetween8And64. El último grupo `CharacterClasses` ejecuta un conjunto adicional de predicados con `MatchAtLeast` establecido en 3. La contraseña del usuario debe tener entre 8 y 16 caracteres e incluir los siguientes caracteres: minúsculas, mayúsculas, números o símbolos.
- **CustomPassword** solo valida DisallowedWhitespace y AllowedAADCharacters. Por lo tanto, el usuario puede proporcionar cualquier contraseña de cualquier longitud, siempre y cuando los caracteres sean válidos.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

En el tipo de notificación, agregue el elemento **PredicateValidationReference** y especifique el identificador como una de las validaciones de predicado, como SimplePassword, StrongPassword o CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

A continuación, se muestra cómo se organizan los elementos cuando Azure AD B2C muestra el mensaje de error siguiente:

![Proceso de predicado](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Configurar un intervalo de fechas

Con los elementos **Predicates** y **PredicateValidations** puede controlar los valores de fecha mínimos y máximos de **UserInputType** utilizando `DateTimeDropdown`. Para ello, cree un **Predicate** con el método `IsDateRange` y especifique los parámetros mínimos y máximos.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Agregue un **PredicateValidation** con una referencia al predicado `DateRange`.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

En el tipo de notificación, agregue el elemento **PredicateValidationReference** y especifique el identificador como `CustomDateRange`. 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
